---
title: QuizGameInGolang
date: 2020-11-30 14:47:40
tags:
hide: true
---

先占坑

<!--more-->

# Go的常量是无类型的

这正好是上面一句话说的意思：无类型的常量有一个与它们相关联的默认类型，并且当且仅当一行代码需要时才提供它

其实你不用纠结这一点，只要记住：常量可以赋值给 “合适的” 类型，而不需要类型转换。

比如：

```go
const a = 0
var b = 0

var f1 float64 = a
var f2 float64 = b

fmt.Println(f1, f2)
```

以上代码，`f1` 的赋值正常，但 `f2` 编译不通过。







## Slice的append操作对原数组的影响

#### 学习知识点：

1.cap不越界，slice直接引用原数组，并改变数组内元素的值；
2.cap越界，分配新的数组，cap是原slice cap的两倍。

#### 实验：

###### 知识点一验证：

输入：

```go
arr := [...]int{0,1,2,3,4}
s1 := arr[3:4] // [3]
s2 := append(s1,14)
fmt.Printf("arr:%v,len(arr)为%d,cap(arr为%d\n",arr,len(arr),cap(arr))
fmt.Printf("s1:%v,len(s1)为%d,cap(s1)为%d\n",s1,len(s1),cap(s1))
fmt.Printf("s2:%v,len(s2)为%d,cap(s2)为%d\n",s2,len(s2),cap(s2))
123456
```

输出：

```go
arr:[0 1 2 3 14],len(arr)为5,cap(arr)为5
s1:[3],len(s1)为1,cap(s1)为2
s2:[3 14],len(s2)为2,cap(s2)为2
123
```

实验结果：cap不越界（slice的cap值为切片起始位置到数组最后一个元素的数量），slice直接引用原数组，进行append操作，原数组arr[4]由4变为14。

###### 知识点二验证：

输入：

```go
arr := [...]int{0,1,2,3,4}
s1 := arr[3:4] // [3]
s2 := append(s1,14，15)
fmt.Printf("arr:%v,len(arr)为%d,cap(arr为%d\n",arr,len(arr),cap(arr))
fmt.Printf("s1:%v,len(s1)为%d,cap(s1)为%d\n",s1,len(s1),cap(s1))
fmt.Printf("s2:%v,len(s2)为%d,cap(s2)为%d\n",s2,len(s2),cap(s2))
123456
```

输出：

```go
arr:[0 1 2 3 4],len(arr)为5,cap(arr)为5
s1:[3],len(s1)为1,cap(s1)为2
s2:[3 14 15],len(s2)为3,cap(s2)为4
123
```

实验结果：append元素导致len(slice)超过cap值，会分配新的数组，cap由原来的2扩展到4，append操作原数组不影响。



## Go语言读取文件

### 将整个文件读取到内存--读到字节切片

程序会读取文件，并返回一个字节切片，而这个切片保存在 file 中。我们将file转换为string，显示出文件的内容

```go
package main

import (
	"fmt"
	"io/ioutil"
)

func main() {
	file, err := ioutil.ReadFile("default.txt")
	if err != nil {
		fmt.Println(err.Error())
		return
	}
	fmt.Println(string(file))
}
```

```shell
$go run .                                 
a default file
hello world
nice to meet you
```



### 分块读取文件

当文件非常大时，尤其在 RAM 存储量不足的情况下，把整个文件都读入内存是没有意义的。更好的方法是分块读取文件。这可以使用bufio包来完成。

1. 打开文件；
2. 在文件上新建一个Reader；
3. 确定分块的大小
4. 分块读取

```go
package main

import (
	"fmt"
	"os"
	"bufio"
)

func main() {
	file, err := os.Open("default.txt")
	if err != nil {
		fmt.Println(err.Error())
		return
	}

	reader := bufio.NewReader(file)
	b := make([]byte, 3)

	for {
		_, err := reader.Read(b)
		if err != nil {
			fmt.Println(err.Error())
			break
		}
		fmt.Println(string(b))
	}
}
```

```shell
$ go run .
a d
efa
ult
 fi
le

hel
lo 
wor
ld

nic
e t
o m
eet
 yo
uyo
EOF
```

### 逐行读取文件

1. 打开文件；
2. 在文件上新建一个 scanner；
3. 扫描文件并且逐行读取。

```go
package main

import (
	"fmt"
	"os"
	"bufio"
	"log"
)

func main() {
	file, err := os.Open("default.txt")
	if err != nil {
		fmt.Println(err.Error())
		return
	}

	scanner := bufio.NewScanner(file)
	
	for scanner.Scan() {
		fmt.Println(scanner.Text())
	}

	err = scanner.Err()
	if err != nil {
		log.Fatal(err)
	}
}
```



### 使用命令行传递文件名称

使用flag包，我们可以从输入的命令行获取到文件路径，接着读取文件内容。

首先我们来看看 `flag` 包是如何工作的。`flag` 包有一个名为String的函数。该函数接收三个参数。第一个参数是标记名，第二个是默认值，第三个是标记的简短描述。

```go
package main

import (
	"fmt"
	"os"
	"bufio"
	"log"
	"flag"
)

func main() {
	filename := flag.String("filename", "default.txt", "the file to open")
	flag.Parse()

	file, err := os.Open(*filename)
	if err != nil {
		fmt.Println(err.Error())
		return
	}

	scanner := bufio.NewScanner(file)
	
	for scanner.Scan() {
		fmt.Println(scanner.Text())
	}

	err = scanner.Err()
	if err != nil {
		log.Fatal(err)
	}
}
```

```shell
$ go build .
$ ./File -filename=default.txt
a default file
hello world
nice to meet you
$ ./File -filename=default
open default: no such file or directory
```







## 读取csv file的内容

```go
package main

import (
	"fmt"
	"flag"
	"os"
	"encoding/csv"
)

func main() {
	csvFilename := flag.String("csv", "problems.csv", "a csv file in the format of 'question, answer'")
	flag.Parse()

	file, err := os.Open(*csvFilename)

	if err != nil {
		fmt.Printf("Failed to open the CSV file: %s", *csvFilename)
		os.Exit(1)
	}

	r := csv.NewReader(file)

	lines, err := r.ReadAll()
	if err != nil {
		fmt.Printf("Failed to parse the provided CSV file.")
		os.Exit(1)
	}
	fmt.Println(lines)
}
```



```shell
$ go run .
[[5+5 10] [7+3 10] [1+1 2] [8+3 11] [1+2 3] [8+6 14] [3+1 4] [1+4 5] [5+1 6] [2+3 5] [3+3 6] [2+4 6] [5+2 7]]
```



## 处理csv中的数据

```go
func ParseLines(lines [][]string) []problem {
	var problems = make([]problem, len(lines))
	for i, line := range lines {
		problems[i] = problem {
			question: line[0],
			answer: line[1],
		}
	}
	return problems
}
```





## 简单的处理

```go
problems := ParseLines(lines)
correct := 0

for i, p := range problems {
    fmt.Printf("Problem #%d: %s = \n", i + 1, p.question)
    var answer string 
    fmt.Scanf("%s\n", &answer)
    if answer == p.answer {
        correct++
    }
}
fmt.Printf("You scored %d out of %d.\n", correct, len(problems))
```













```go
package main

import (
	"fmt"
	"flag"
	"os"
	"encoding/csv"
	"strings"
)

func main() {
	csvFilename := flag.String("csv", "problems.csv", "a csv file in the format of 'question, answer'")
	flag.Parse()
	
	file, err := os.Open(*csvFilename)
	if err != nil {
		exit(fmt.Sprintf("Failed to open the CSV file: %s", *csvFilename))
		
	}

	r := csv.NewReader(file)
	lines, err := r.ReadAll()
	if err != nil {
		exit("Failed to parse the provided CSV file.")
	}

	problems := parseLines(lines)

	correct := 0
	for i, p := range problems {
		fmt.Printf("Problem #%d: %s = \n", i + 1, p.q)
		var answer string
		fmt.Scanf("%s\n", &answer)

		if answer == p.a {
			correct++
		}
	}
	fmt.Printf("You scored %d out of %d.\n", correct, len(problems))
}

type problem struct {
	q string
	a string
}

func parseLines(lines [][]string) []problem {
	ret := make([]problem, len(lines))
	for i, line := range lines {
		ret[i] = problem {
			q: line[0],
			a: strings.TrimSpace(line[1]),
		}
	}
	return ret
}


func exit(msg string) {
	fmt.Println(msg)
	os.Exit(1)
}
```

