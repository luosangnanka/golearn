## golang 入门概述

如果您刚听说 golang, 或者之前就听说过但是一直止步于听说或者简单看过一些入门教程, 本文档旨在帮助更快的学习 golang.

本文档将通过实例由浅入深的可以快速上手进行基本业务代码的书写.

下面是本文档讲解的提纲:

- 常见类型转换
- 常见的字符串处理
- slice
- map 与 map 线程安全
- 常见的时间处理
- 简单地模板处理
- 常见的文件操作
- 实现简单的 http server
- http 请求接口
- ip 操作
- 接口类型
- 错误和异常处理
- json 与 xml
- db 封装
- 配置文件
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

* 遍历操作:
	
	var useSlice = make([]string, 0)
	for k, v := range tmpSlice {
		useSlice = append(useSlice, v)
		fmt.Println(k)
	}
	fmt.Println(useSlice)

* 检测元素是否在数组中.
	
	func InInterfaceSlice(v interface{}, sl []interface{}) (in bool) {
		for _, vv := range sl {
			if vv == v {
				return true
			}
		}
	
		return false
	}

* 合并 slice.

	func SliceMerge(slice1, slice2 []interface{}) (sl []interface{}) {
		sl = append(slice1, slice2...)
	
		return
	}

* 将 slice 的元素经过一个 reduce 函数重新生成.
	type reducetype func(interface{}) interface{}

	func SliceReduce(slice []interface{}, fc reducetype) (dslice []interface{}) {
		for _, v := range slice {
			dslice = append(dslice, fc(v))
		}
	
		return
	}

* 随机取出 slice 中的一个元素.

	func SliceRand(a []interface{}) (b interface{}) {
		randnum := rand.Intn(len(a))
		b = a[randnum]
	
		return
	}

* slice 元素加和.

	func SliceSum(intslice []int64) (sum int64) {
		for _, v := range intslice {
			sum += v
		}
	
		return
	
	}

* slice 过滤, 将不满足函数的元素剔除.

	type reducetype func(interface{}) interface{}

	func SliceFilter(slice []interface{}, a filtertype) (ftslice []interface{}) {
		for _, v := range slice {
			if a(v) {
				ftslice = append(ftslice, v)
			}
		}
	
		return
	}

* 取出 slice1 和 slice2 的区别.

	func SliceDiff(slice1, slice2 []interface{}) (diffslice []interface{}) {
		for _, v := range slice1 {
			if !InInterfaceSlice(v, slice2) {
				diffslice = append(diffslice, v)
			}
		}
	
		return
	}

* 生成一个 int 的 range slice.

	func SliceRange(start, end, step int64) (intslice []int64) {
		for i := start; i <= end; i += step {
			intslice = append(intslice, i)
		}
	
		return
	}

* slice 去重.

	func SliceUnique(slice []interface{}) (uniqueslice []interface{}) {
		for _, v := range slice {
			if !InInterfaceSlice(v, uniqueslice) {
				uniqueslice = append(uniqueslice, v)
			}
		}
	
		return
	}

* slice 随机打乱。

	func SliceShuffle(slice []interface{}) (rs []interface{}) {
		length := len(slice)
		for i := 0; i < length; i++ {
			a := rand.Intn(length)
			b := rand.Intn(length)
			slice[a], slice[b] = slice[b], slice[a]
		}
	
		return slice
	}

# map 与 map 线程安全

map 即为 key - value 形式的 hash 字典, 使用 map 的时候需要注意一下事项:

- `map`是无序的，每次打印出来的`map`都会不一样，它不能通过`index`获取，而必须通过`key`获取
- `map`的长度是不固定的，也就是和`slice`一样，也是一种引用类型, 如果有两个 map 同时指向同一个底层, 那么一个改变, 另一个也相应改变.
- 内置的`len`函数同样适用于`map`，返回`map`拥有的`key`的数量
- `map`的值可以很方便的修改，通过`numbers["one"]=11`可以很容易的把key为`one`的字典值改为`11`
- `map`和其他基本型别不同，它不是thread-safe，在多个go-routine存取时，必须使用mutex lock机制.

`map`的初始化可以通过`key:val`的方式初始化值，同时`map`内置有判断是否存在`key`的方式

通过`delete`删除`map`的元素.

# 常见的时间处理

在业务处理的时候, 另一个最常用到的地方就是时间的处理, 如当前时间戳、当前格式化时间等等, 下面就是使用 time 包进行的相关常用的事件处理的整理:

- 获取当前时间戳:

	now := time.Now().Unix()

- 获取当前格式化时间(格式化为 2014-08-06 11:08:09)

	now := time.Now().Format("2006-01-02 15:04:05")

- 将 int64 转换成 Unix 时间戳:

	var t int64 = 1414002230
	now := time.Unix(t, 0)

- 更多时间函数操作的封装, 请见:
* xmlib2/migo/utils/utils.go
* xmlib2/xmtime

# 简单地模板处理

在Go语言中, 我们使用 template 包来进行模板处理, 使用类似 Parse、ParseFile、Execute 等方法从文件或者字符串加载模板, 然后执行类似上面图片展示的模板的 merge 操作.

下面就是常见的模板操作, 程序运行请 [猛戳](http://play.golang.org/p/v9cWZ8pRiZ)

	package main
	
	import (
		"fmt"
		"os"
		"text/template"
	)
	
	// 模板函数.
	var (
		tplFuncMap template.FuncMap = make(template.FuncMap)
	)
	
	func init() {
		tplFuncMap["eq"] = Equal
	}
	
	// 等于
	func Equal(a, b interface{}) (result bool) {
		result = a == b
		return
	}
	
	type School struct {
		Group   int
		Student map[string]string
	}
	
	func main() {
		ss := make([]*School, 0)
		for i := 1; i < 4; i++ {
			s := new(School)
			s.Group = i
			s.Student = make(map[string]string)
			s.Student["age"] = fmt.Sprintf("%d", 110+i)
			s.Student["name"] = fmt.Sprintf("xiaomi %d", i)
			ss = append(ss, s)
		}
	
		t := template.New("test").Funcs(tplFuncMap)
		t, _ = t.Parse(`Hello xiaomi school:
	{{range $k, $v := .}}
	group: {{$v.Group}}, {{if eq $v.Group 2}}最强年级用来测试模板函数和 if 条件{{end}}
	Student info:
	{{range $kk, $vv := $v.Student}}
		{{$kk}}: {{$vv}}, 
	{{end}}
	{{end}}`)
		t.Execute(os.Stdout, ss)
	}

# 常见的文件操作

另一个常用的操作就是文件的相关操作, 如进行日志记录、读写文件等操作, 下面就是列举的常见的文件操作:

- 读取文件:

对于打开文件句柄, os 包有两个方法, 分别是 os.Open 和 os.OpenFile, 区别是, os.OpenFile 增加了 flag 参数, 分别为如下:

	const (
	    O_RDONLY int = syscall.O_RDONLY // 只读方式打开.
	    O_WRONLY int = syscall.O_WRONLY // 只写方式打开.
	    O_RDWR   int = syscall.O_RDWR   // 读写方式打开.
	    O_APPEND int = syscall.O_APPEND // 文件追加打开.
	    O_CREATE int = syscall.O_CREAT  // 当文件不存在时创建文件.
	    O_EXCL   int = syscall.O_EXCL   // 文件此时必须不存在.
	    O_SYNC   int = syscall.O_SYNC   // 打开时进行 io 加锁.
	    O_TRUNC  int = syscall.O_TRUNC  // 打开文件的时候清空文件.
	)

下面是读取文件内容的代码:

	f, err := os.OpenFile("tmp.txt", os.O_CREATE|os.O_WRONLY|os.O_APPEND, 0644)
	if err != nil {
		fmt.Println(err)
		return
	}
	defer f.Close()

	body, err := ioutil.ReadAll(f)
	if err != nil {
		fmt.Println(err)
		return
	}
	data := strings.Splite(string(body), "\n")
	for _, line := range data {
		...
	}

- 写入文件:

当读取文件句柄后, 便可以通过 WriteString 进行写入文件.
	
	f, err := os.OpenFile("tmp.txt", os.O_CREATE|os.O_WRONLY|os.O_APPEND, 0644)
	if err != nil {
		fmt.Println(err)
		return
	}
	defer f.Close()

	f.WriteString("test")

- 下面是常见的文件方法封装:
* 获取文件绝对路径:

	func SelfPath() (path string) {
		path, _ = filepath.Abs(os.Args[0])
	
		return
	}

* 获取文件的目录:

	func SelfDir() (dir string) {
		dir = filepath.Dir(SelfPath())
	
		return
	}

* 判断文件是否存在:

	func FileExists(name string) (is bool) {
		if _, err := os.Stat(name); err != nil {
			if os.IsNotExist(err) {
				return false
			}
		}
	
		return true
	}

* 在 path 下搜索文件:

	func SearchFile(filename string, paths ...string) (fullpath string, err error) {
		for _, path := range paths {
			if fullpath = filepath.Join(path, filename); FileExists(fullpath) {
				return
			}
		}
		err = fmt.Errorf("%s not fount in paths", filename)
	
		return
	}

* 查找文件内容:
	
	func GrepFile(patten string, filename string) (lines []string, err error) {
		re, err := regexp.Compile(patten)
		if err != nil {
			return
		}
	
		fd, err := os.Open(filename)
		if err != nil {
			return
		}
		lines = make([]string, 0)
		reader := bufio.NewReader(fd)
		prefix := ""
		for {
			byteLine, isPrefix, er := reader.ReadLine()
			if er != nil && er != io.EOF {
				return
			}
			line := string(byteLine)
			if isPrefix {
				prefix += line
				continue
			}
	
			line = prefix + line
			if re.MatchString(line) {
				lines = append(lines, line)
			}
			if er == io.EOF {
				break
			}
		}
	
		return
	}

# 实现简单的 http server

golang 强大的 net/http 包, 可以通过三种不同的方式来实现简单的 http 的 server. 下面就是三种不同方式的实现:

- 使用 http.ListenAndServe 快速搭建 http server.

	package main

	import (
		"fmt"
		"io"
		"net/http"
	)
	
	// hello world, the web server
	func HelloServer(w http.ResponseWriter, req *http.Request) {
		io.WriteString(w, "hello, world!\n")
	}
	
	// 使用 http.ListenAndServe 快速搭建 http server.
	func main() {
		http.HandleFunc("/hello", HelloServer)
		err := http.ListenAndServe(":12345", nil)
		if err != nil {
			fmt.Println("ListenAndServe: ", err)
			return
		}
	}

- 使用 http.ServeMux 搭建多线程 accept server.

	package main

	import (
		"fmt"
		"io"
		"net"
		"net/http"
		"os"
		"runtime"
	)
	
	// hello world, the web server
	func HelloServer(w http.ResponseWriter, req *http.Request) {
		io.WriteString(w, "hello, world!\n")
	}
	
	// 使用 http.ServeMux 搭建多线程 accept server.
	type Server struct {
		svr    *http.Server
		svrMux *http.ServeMux
	}
	
	var gListener net.Listener = nil
	var gServerList = make([]*Server, 0)
	
	func main() {
		numSvrs := runtime.NumCPU()
		for i := 0; i < numSvrs; i++ {
			s := &Server{}
			s.svr = &http.Server{}
			s.svrMux = http.NewServeMux()
			s.svr.Handler = s.svrMux
			s.svr.Addr = ":12345"
			gServerList = append(gServerList, s)
		}
	
		for _, s := range gServerList {
			s.svrMux.HandleFunc("/hello", HelloServer)
		}
	
		var err error
		gListener, err = net.Listen("tcp", ":12345")
		if err != nil {
			fmt.Fprintf(os.Stderr, "listen error: %s", err)
			return
		}
		for _, s := range gServerList {
			go s.svr.Serve(gListener)
		}
	
		select {}
	}	

- 使用 http.Server 接口搭建 server.

	package main

	import (
		"fmt"
		"io"
		"net/http"
		"os"
		"time"
	)
	
	// 使用 http.Server 接口搭建 server.
	type Handlers struct {
	}
	
	func main() {
		var handler = new(Handlers)
	
		server := &http.Server{
			Addr:         ":12345",
			Handler:      handler,
			ReadTimeout:  time.Duration(30) * time.Second,
			WriteTimeout: time.Duration(30) * time.Second,
		}
	
		err := server.ListenAndServe()
		if err != nil {
			fmt.Fprintf(os.Stderr, "listen error: %s", err)
			return
		}
	}
	
	func (h *Handlers) ServeHTTP(rw http.ResponseWriter, r *http.Request) {
		io.WriteString(rw, "hello, world!\n")
	}

- 更多请看:
* xmlib2/migo/router.go

# http 请求接口.

在我们通过 http 请求接口的时候, 常使用的就是类似于 CURL 的操作, 那么 golang 的 net/http 也提供了简单地 http post、get 等基本方法, 以及 https 接口的请求, 下面是基本的 http 请求方式:

# ip 操作

当我们处理 web 端请求的时候常需要进行相关的 ip 操作, 如获取请求的 ip、ip 字符串和 int64 类型之间的相互转换等, 下面是如何获取客户端和本级 ip 的代码示例:

- 获取客户端请求的 ip:
	
	var ip string
	// 首先判断代理.
	if ips := req.Header("X-Forwarded-For"); ips != "" {
		ip = strings.Split(ips, ",")
	} else { // 不含代理.
		ipS := strings.Split(req.RemoteAddr, ":")
		if len(ipS) > 0 {
			if ipS[0] != "[" {
				ip = ipS[0]
			}
		}
	}

- 获取系统本地 ip:
- To Do.

# 错误和异常处理
	
# 接口类型

golang 中的接口类型可以理解为一种组件式的协议.

![](golearn/interface.jpg?raw=true)

# db 封装

golang 与 PHP 不同的是没有官方封装的数据库驱动, 而是为开发者开发数据库驱动定义了一些标准接口，开发者可以根据定义的接口来开发相应的数据库驱动, 这样做有一个好处, 只要按照标准接口开发的代码,  以后需要迁移数据库时, 不需要任何修改. 那么Go都定义了哪些标准接口呢? 让我们来详细的分析一下:

- sql.Register:

这个存在于database/sql的函数是用来注册数据库驱动的, 当第三方开发者开发数据库驱动时, 都会实现init函数, 在init里面会调用这个Register(name string, driver driver.Driver)完成本驱动的注册.

我们来看一下mymysql、sqlite3的驱动里面都是怎么调用的:

	// https://github.com/mattn/go-sqlite3 驱动.
	func init() {
	    sql.Register("sqlite3", &SQLiteDriver{})
	}
	
	// https://github.com/mikespook/mymysql驱动.
	// Driver automatically registered in database/sql.
	var d = Driver{proto: "tcp", raddr: "127.0.0.1:3306"}
	func init() {
	    Register("SET NAMES utf8")
	    sql.Register("mymysql", &d)
	}

- driver.Driver:

	Driver是一个数据库驱动的接口, 他定义了一个method: Open(name string), 这个方法返回一个数据库的Conn接口。

	type Driver interface {
	    Open(name string) (Conn, error)
	}

- 你可以这样理解 database/sql 的接口, 就是我官方不针对某一种具体的数据库类型提供相应的封装, 而是为你提供了一个协议, 当你今天自己封装了 100 个 mysql 的数据库操作方法, 当明天新增了一种数据库的支持的需求, 如 sqlite3、mymysql 等, 便可以直接修改一下注册的函数 
	
	func Register(name string, driver driver.Driver)

的 name, 其他的具体方法无需更改便可以继续使用.

- 虽然 golang 官方没有针对各种数据库类型的驱动, 但是由于十分灵活的接口形式, 加上 golang 开放的语言特性, 在 github 上面有充足十分健全的各种数据库的驱动开源 package, 如下:
* [mysql](https://github.com/go-sql-driver/mysql) 
* [sqlite](https://github.com/mattn/go-sqlite3)
* [PostgreSQL](https://github.com/jbarham/gopgsqldriver)
* [oracle](https://github.com/mattn/go-oci8)

- 常用的 mysql 的方法封装, 请见 xmlib2/xmdb.

# 配置文件

当我们进行程序开发的时候, 避不可免的会遇到到读取配置文件的需求, 于是我们封装了 xmlib/config 的 package, 旨在方便快速的进行配置文件的使用, 下面是 xmlib/config 的说明:

- 原理:

读取指定路径下面的配置文件, 然后按行进行读取, 遇到我们设置的关键字:

	";": 	 注释
	"[]": 	 配置块
	"a = b": 配置项

- config interface 说明:

在查看 xmlib/config 的代码的时候, 你会看到一个如下的类型:

	// Configer define the interface of configure
	type Configer interface {
		GetSection(sectionName string) (section Section, err error)
		GetAllSections() (sectionList map[string]Section)
		GetSetting(sectionName, keyName string) (value string, err error)
		GetBoolSetting(sectionName, keyName string, dfault bool) (value bool, err error)
		GetIntSetting(sectionName, keyName string, dfault int64) (value int64, err error)
		LastModify() time.Time
		FileName() string
		DumpConf() (err error)
	}

正式这样的接口形式, 为后期的配置文件扩展做了充足的工作, 因为这只是约定了配置文件的协议形式, 这样当日后我们想要支持更多形式的配置形式的时候, 只需更改初始化 config 时的类型参数, 其他的上述方法均不需要进行更改.

- 下面是使用配置文件的常见形式:

	// 首先通过 flag 参数指定 config 路径, 这样当我们运行我们的二进制程序 xxx 的时候, 只需加上如下的 flag 参数即可:
	// ./xx -c=etc/xxx.conf
	// 这种的实现模式是通过 golang 官方的 flag 包支持的.
	flag.Parse()
	config := flag.String("c", "", "Config file name")
	// 当传入的配置文件路径为空的时候, 输出 flag 的默认提示.
	if *config == "" {
		fmt.Fprintln(os.Stderr, "Usage of ", os.Args[0], " [-c etc/guessing.conf]")
		flag.PrintDefaults()
		fmt.Fprintln(os.Stderr)
		os.Exit(0)
	}
	// 初始化配置文件.
	gCfg, err := config.NewConfiger(*gConfig)
	if err != nil {
		fmt.Println(err)
		return
	}

这样, 我们就完成了 config 包的初始化, 之后当我们想要获取配置块、配置选项均可以通过 GetSection、GetSetting 等方法实现了.

- 更多详情请见, xmlib/migo/config.go 269 行 ~ 387 行.

