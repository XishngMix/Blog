---
title: ProtoBuf 填坑日记
date: 2022-12-27 15:13
tags:
- GRPC
- ProtoCol Buffer
categories:
- GRPC
- 踩坑日记
---

## package、go_package

**package：**主要是用于避免命名冲突的，不同的项目（project）需要指定不同的package。

**option go_package：** 

1. 表明如果要引用这个proto生成的文件的时候import后面的路径
2. 如果不指定--go_opt（默认值），生成的go文件存放的路径。

> 需要注意的是package 和 go_package 含义：
> 
> 1. **package**用于防止不同project之间定义了同名message结构的冲突，因为package名的一个作用是用于init方法中的注册
> 2. 当**go_package**存在时，其最后一个单词是生成的go文件的package名字
> 3. 当**go_package**不存在时，go文件的package名字就变成了proto中package指定的名字了。

## 如何使Proto生成的pb.go文件同源文件在相同目录

```bash
protoc {}.proto --go_out=. --go_opt=paths=source_relative
```

- —go_out：表明数据的文件位置
- —go_opt：表示生成go文件时候的目录选项，如上面写时表示生成的文件与proto在同一目录。