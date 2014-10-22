## golang 入门概述

如果您刚听说 golang, 或者之前就听说过但是一直止步于听说或者简单看过一些入门教程, 本文档旨在帮助更快的学习 golang.

本文档将通过实例由浅入深的可以快速上手进行基本业务代码的书写.

下面是本文档讲解的提纲:

- 常见类型转换
- 常见的字符串处理
- slice
- map 与 map 线程安全
- 反射
- 常见的时间处理
- 简单地模板处理
- 常见的文件
- 实现简单的 http server
- http 请求接口
- ip 操作
- 简单的 tcp 应用
- db 封装
- 配置文件
- 错误处理
- json 与 xml
- xmlib 简介

# 常见类型转换

由于 golang 是强类型语言, 所以会经常遇到常见类型之间的相互转换, 如 string 转换成 int 或者 float64、int 转换成 string 等, 下面我将列举一些常见的类型转换实例方法:

* [常见类型转换, 点此可以运行](http://play.golang.org/p/E6nZ6B8P6V)

下面是示例代码:

	package main
	
	import (
		"fmt"
		"strconv"
	)
	
	func main() {
		aString := "19"
	
		// string -> int.
		aInt, err := strconv.Atoi(aString)
		if err != nil {
			fmt.Println(err)
			return
		}
		fmt.Printf("aString convert to int, result is %d.\n", aInt)
	
		// string -> int64.
		aInt64, err := strconv.ParseInt(aString, 10, 64)
		if err != nil {
			fmt.Println(err)
			return
		}
		fmt.Printf("aString convert to int64, result is %d.\n", aInt64)
	
		bInt := 250
		// int -> string.
		bString := strconv.Itoa(bInt)
		// 或者使用 fmt 的格式化输出方式.
		bString = fmt.Sprintf("%d", bInt)
		fmt.Printf("bInt convert to string, result is %s.\n", bString)
	
		cString := "3.14"
		// string -> float64.
		cFloat64, err := strconv.ParseFloat(cString, 64)
		if err != nil {
			fmt.Println(err)
			return
		}
		fmt.Printf("cString convert to float64, result is %f.\n", cFloat64)
	
		dFloat64 := 7.89123445
		// float64 -> string.
		dString := strconv.FormatFloat(dFloat64, 'f', -1, 64)
		// 或者使用 fmt 的格式化输出方式.
		dString = fmt.Sprintf("%.2f", dFloat64)
		fmt.Printf("dFloat64 convert to string, result is %s.\n", dString)
	
		// int <-> float64 可通过直接的强制类型转换实现.
		var eInt int = 19
		fmt.Printf("int 19 convert to float64, result is %f.\n", float64(eInt))
		var fFloat64 float64 = 19.12345677
		fmt.Printf("float64 19.12345677 convert to int, result is %d.\n", int64(	fFloat64))
	
		// string -> bool.
		fmt.Println(strconv.ParseBool("1"))     // 输出 true <nil>.
		fmt.Println(strconv.ParseBool("t"))     // 输出 true <nil>.
		fmt.Println(strconv.ParseBool("T"))     // 输出 true <nil>.
		fmt.Println(strconv.ParseBool("true"))  // 输出 true <nil>.
		fmt.Println(strconv.ParseBool("True"))  // 输出 true <nil>.
		fmt.Println(strconv.ParseBool("TRUE"))  // 输出 true <nil>.
		fmt.Println(strconv.ParseBool("TRue"))  // false strconv.ParseBool: 	parsing "TRue": invalid syntax.
		fmt.Println(strconv.ParseBool("0"))     // 输出 false <nil>.
		fmt.Println(strconv.ParseBool("f"))     // 输出 false <nil>.
		fmt.Println(strconv.ParseBool("F"))     // 输出 false <nil>.
		fmt.Println(strconv.ParseBool("false")) // 输出 false <nil>.
		fmt.Println(strconv.ParseBool("False")) // 输出 false <nil>.
		fmt.Println(strconv.ParseBool("FALSE")) // 输出 false <nil>.
		fmt.Println(strconv.ParseBool("FALse")) // false strconv.ParseBool: 	parsing "FAlse": invalid syntax.
	
		// bool -> string.
		fmt.Println(strconv.FormatBool(0 < 1)) // 输出 true.
		fmt.Println(strconv.FormatBool(0 > 1)) // 输出 false.
	
		// interface 类型转换.
		interfaceTypeConversion("xiaomi v5")
		interfaceTypeConversion(19)
		interfaceTypeConversion(3.1415926)
		interfaceTypeConversion([]byte("lalalal"))
		interfaceTypeConversion('b')
	}
	
	func interfaceTypeConversion(in interface{}) {
		switch v := in.(type) {
		case string:
			fmt.Println("in is string type.", v)
		case int32, int64:
			fmt.Println("in is string int.", v)
		case float32, float64:
			fmt.Println("in is string float64.", v)
		case []byte:
			fmt.Println("in is string []byte.", v)
		case byte:
			fmt.Println("in is string byte.", v)
		default:
			fmt.Println("unknown")
		}
	
		return
	}

- 说明:
* 直接转换, 必须显示声明, 但必须在相互兼容类型之间;
* 可用于直接转换的类型对应表:
	同类型不同尾数: 如 int32 <-> int64, float32 <-> float64
	[]byte <-> string 
	float32 || folat64 <-> int
* 类型转换可使用强大的 strconv 和 fmt 系统包.
* interface 类型转换的时候通过 type switches, 如将 interface 转换成 string, 需要采用如下的方式:
	tmp := a.(string)

# 常见的字符串处理

由于各种业务需要, 不论什么语言, 编码过程中最常见的部分就是字符串的相关处理了, 我们可以从 PHP 中封装的大量的字符串相关函数中便可以看的出来, 下面是列举的常见的字符串相关操作:

* 拼接字符串:
- 拼接字符串有多种方式, 几种方式如下:

	var a string = "xiaomi "
	var b string = "v5"

1、简单的直接拼接:
	
	fmt.Println(a + b)

2、使用 bytes 包进行拼接:
	
	tmp := bytes.Buffer{}
	tmp.WriteString(a)
	tmp.WriteString(b)
	fmt.Println(tmp.String()) 

3、使用 fmt 包进行拼接:
	
	fmt.Printf("%s%s", a, b)

经过实际测试, 直接拼接的效率是最高的, 而使用的 bytes 包效率高于 fmt 包, 而低于直接拼接.

* 字符串切割:
- 会经常遇到字符串的切割问题, 因为字符串本身就是一个 slice, 所以直接的取字符串的某部分可以使用如下的方式:
	
	var a string = "xiaomiv5"
	fmt.Println(a[:4]) 	// 输出 xiao.
	fmt.Println(a[4:6]) // 输出 mi.
	fmt.Println(a[6:]) 	// 输出 v5.

- 但会遇到通过特殊字符的切割, 可以通过强大的 strings 包实现:
	
	var b string = "xiaomi_mi_v5_1"
	bSlice := strings.Split(b, "_")
	fmt.Println(bSlice[1])

也可以通过索引的方式, 进行切割:
	
	index1 := stirngs.Index(b, "_")
	fmt.Println(b[:index1]) // 输出 xiaomi

	index2 := strings.LastIndex(b, "_")
	fmt.Println(b[index2:]) // 输出 1

当然, 如果想要采用如上这样索引的方式取出中间的部分则需要连续索引切割的方式了.

# slice

slice, 即可动态分配内存的数组形式, 也是我们最长进行处理的数据形式, 下面是 slice 相关的处理方法列举:

* append 操作.

	var tmpSlice = make([]string, 0)
	tmpSlice = append(tmpSlice, "1")
	tmpSlice = append(tmpSlice, "10")
	tmpSlice = append(tmpSlice, "20", "30")
	fmt.Println(tmpSlice) // 输出 [1 10 20 30]
