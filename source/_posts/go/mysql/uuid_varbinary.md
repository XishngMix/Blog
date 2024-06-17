---
title: MySQL VarBinary使用
date: 2023-11-27 00:17
tags:
- 基本知识
- 底层原理
categories:
- 基本知识
- MySQL
- VarBinary
---

VARBINARY 是 MySQL 数据库中用于存储二进制数据的一种数据类型，它可以存储任何类型的二进制数据，例如图像、声音、视频等。与 BLOB 类型不同， VARBINARY 类型没有指定最大长度，而是根据实际需要动态调整存储空间。
如VARBINARY(16)所代表的含义为，16位的二进制数据流。

大部分情况下，我们这边使用VARBINARY类型时，更多的是针对于UUID作为索引的场景。

在业务代码中，我们可能会这样子来实现VARBINARY类型数据的写入/读取：
```golang
type UUIDBinary string

// 将UUID字符串，转换为二进制，并通过interface来实现gorm引擎可识别的数据格式，以供写入MySQL中
func (id UUIDBinary) Value() (v driver.Value, err error) {
    if id == "" {
       return nil, nil
    }
    // uuid.Parse 会将当前UUID，转换为Hex：以32个字符表示的UUID，去除了其中的'-'
    u, err := uuid.Parse(string(id))
    if err != nil {
       return nil, err
    }
    // 将UUID的 hex 转换为 16 进制的 数据流
    uuidBinary, err := u.MarshalBinary()
    if err != nil {
       return nil, err
    }
    return uuidBinary, nil
}

// 读取到数据后，将其转换为二进制，再进一步转换为原本的UUID数据
func (id *UUIDBinary) Scan(value interface{}) (err error) {
    if value == nil {
       *id = ""
       return nil
    }
    s, ok := value.([]byte)

    if !ok {
       *id = ""
       return errors.New("invalid scan source")
    }

    // 从 16 进制的数据流中，unhex出UUID
    u, err := uuid.FromBytes(s)
    if err != nil {
       *id = ""
       return err
    }
    *id = UUIDBinary(u.String())
    return nil
}
```
而如果我们在插入数据后，去检索插入的数据时，可以发现该数据无法直接辨识出来：
![无法直接辨识的数据](http://pic.xishng.top/img/202311271715620.png)
那么如何在不通过ORM的情形下，快速的可以查找到我们想要看到的数据呢？

现在定义一张数据表，如下所示：
```sql
create table stores
(
    id                    varbinary(16)        not null primary key, // 店铺ID对应的UUID主键
    origin_id             varchar(255)         not null, // 店铺ID
    created_at            datetime             not null,
    updated_at            datetime             not null,
    name                  varchar(255)         null comment '店铺名称'
)
```
并通过我们的业务代码来插入了几条数据【代码不贴了，结合上面的UUIDBinary，就可以快速的搞出来了】。
现在有如下数据：
那么其中ID，便是UUID取hex后的生成的16进制数据流字符串【如果再此处还不理解，回头再看一下业务代码中的注释部分】
通过MySQL的Hex函数，我们可以还原ID的原貌：
![新建的数据](http://pic.xishng.top/img/202311271724094.png)
那么，假如此时你知晓一个UUID，如：000b3ef9-2b37-4f5b-a8f9-33f313357c41，接下来你想知道这个Id对应的真实店铺ID是多少，那就可以尝试去查询了：
1. 按照原理进行推导，可得如下查询语句
```sql
SELECT * FROM stores WHERE hex(id) = REPLACE('000b3ef9-2b37-4f5b-a8f9-33f313357c41', '-', '');
```
![匹配到的数据](http://pic.xishng.top/img/202311271725384.png)
1. 尝试反推，进行优化
```sql
SELECT * FROM stores WHERE id = UNHEX(REPLACE('000b3ef9-2b37-4f5b-a8f9-33f313357c41', '-', ''));
```
![优化后匹配到的数据](http://pic.xishng.top/img/202311271726038.png)
两次查询耗时，可以发现有着10倍速度的差距。
那么为什么会出现这种情况呢？
- 当我们的条件类型与字段类型一致时，MySQL本身是不需要做出任何的处理，即可快速的根据索引命中目标数据
- 当我们的条件类型是需要将字段类型进行变更才可以比较时，MySQL会显式的将所有参与比较的字段进行转换，直至可以命中目标索引。