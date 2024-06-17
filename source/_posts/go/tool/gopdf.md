---
title: GoPDF书写PDF文件
date: 2023-04-17 00:17
tags:
- Go工具
- 文件导处
categories:
- Go工具
---

## INSTALLATION

```go
go get -u github.com/signintech/gopdf
```

## DEMO

```go
package main
import (
	"log"
	"github.com/signintech/gopdf"
)

func main() {
	// 初始化PDF页面
	pdf := gopdf.GoPdf{}
	// 设定PDF页面大小
	pdf.Start(gopdf.Config{ PageSize: *gopdf.PageSizeA4 })  
	// 添加页面
	pdf.AddPage()
	// 添加需要使用到的字体
	err := pdf.AddTTFFont("wts11", "../ttf/wts11.ttf")
	if err != nil {
	    log.Print(err.Error())
	    return
	}

	// 设定当前书写字体
	err = pdf.SetFont("wts11", "", 14)
	if err != nil {
	    log.Print(err.Error())
	    return
	}
	// 在指定位置书写
	pdf.Cell(nil, "您好")
	// 导出PDF
	pdf.WritePdf("hello.pdf")
}
```

## 文字书写

### 添加字体集

> 注：GoPDF虽然支持中文字体输入，但需要导入的TTF字体包支持中文，否则在书写文字时会呈现空白
> 

```go
	err = pdf.AddTTFFontWithOption(calibri, "/font/calibri-regular.ttf", gopdf.TtfOption{
	    Style: gopdf.Regular,
	})
	if err != nil {
	    return err
	}
	err = pdf.AddTTFFontWithOption(calibri, "/font/calibri-bold.ttf", gopdf.TtfOption{
	    Style: gopdf.Bold,
	})
	if err != nil {
	    return err
	}
	err = pdf.AddTTFFontWithOption(calibri, "/font/calibri-italic.ttf", gopdf.TtfOption{
	    Style: gopdf.Italic,
	})
	if err != nil {
	    return err
	}
```

### 使用字体

```go
	// 使用calibri **常规** 字体
	if err = pdf.SetFont(calibri, "", 10); err != nil {
	    return err
	}
	// 使用calibri **加粗** 字体
	if err = pdf.SetFont(calibri, "B", 10); err != nil {
	    return err
	}
	// 使用calibri **斜体** 字体
	if err = pdf.SetFont(calibri, "I", 10); err != nil {
	    return err
	}
```

### 书写内容

**常规书写**

```go
	// 设定书写起始位置
	pdf.SetXY(x, y)
	
	// 从起始位置书写：行效果
	if err = pdf.Cell(nil, "这是行效果"); err != nil {
	    return err
	}

	// 从起始位置书写：行效果 + 靠右 + 居中
	if err = p.f.CellWithOption(&gopdf.Rect{W: 70, H: lineBoth}, detail.Price, gopdf.CellOption{
	    Align: gopdf.Right,
	    Float: gopdf.Center,
	}); err != nil {
	    return err
	}
	
	// 从起始位置书写：块效果
	if err = pdf.MultiCell(&gopdf.Rect{W: 400, H: 100}, "这是块效果"); err != nil {
	    return err
	}

	// 从起始位置书写：块效果 + 靠右 + 居中
	if err = pdf.MultiCellWithOption(&gopdf.Rect{W: 400, H: 100}, "这是块效果"， gopdf.CellOption{
	    Align: gopdf.Right,
	    Float: gopdf.Center,
	}); err != nil {
	    return err
	}
```

**流式布局书写**

```go
	// 设定书写起始位置
	pdf.SetXY(x, y)
	// IsFitMultiCell 判断给定块大小中书写，是否会超出块限制
	// 如果超出返回：ok: false, height: 给定高度, err: nil
	// 如果未超出返回： ok: true, height: 实际使用高度, err: nil
	ok, height, err := pdf.IsFitMultiCell(&gopdf.Rect{W: 400, H: 50}, detail.Title)
	if err != nil {
	    logger.Errorf("Cell has err: %v", err)
	    return err
	}

	// 如果OK则按照实际使用高度进行书写，同时后面的页面效果需要根据此高度进行调整
	if ok {
	    if err = pdf.MultiCell(&gopdf.Rect{W: 400, H: height}, detail.Title); err != nil {
	        return err
	    }
	}

	// 如果非OK，可以通过递归，每次将给定高度增加：行高 + 字体大小 （每次增加一行）
	...
```

## 添加图片

```go
if err = pdf.Image("/pic/logo.png", x, y, &gopdf.Rect{
    W: logoWidth,  // 图片宽度
    H: logoHeight, // 图片高度
}); err != nil {
    logger.Errorf("Image has err: %v", err)
    return err
}
```

## 画直线

> 直线的类型一共有三种：`solid, dashed, dotted`
> 

```go
// 设定直线类型
pdf.SetLineType("")
// 设定直线的宽度
pdf.SetLineWidth(2)
// 在指定的起止位置画直线
pdf.Line(startX, startY, endX, endY)
```