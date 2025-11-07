### 主线任务

- 木犀骇客
  - [准备工作](#准备工作)
  - [Checkpoint1](#Checkpoint1)
  - [Checkpoint2](#Checkpoint2)
  - [Checkpoint3](#Checkpoint3)
  - [Checkpoint4](#Checkpoint4)
  - [Checkpoint5](#Checkpoint5)
- [小结](#小结)

---

#### 准备工作

首先把 hacker-support http-theft-bank-start-template分别fork一份，并且做了些许修改使其可用

- [hacker-support](https://github.com/AHuangMeow/hacker-support.git)
- [http-theft-bank-start-template](https://github.com/AHuangMeow/http-theft-bank-start-template.git)

[🏠](#主线任务)

---

#### Checkpoint1

访问https://gtainmuxi.muxixyz.com/api/v1/organization/code，得到如下页面

![1.1](./Pictures/checkpoint1/1.1.png)

先大概看一下Header：

```go
// checkpoint1/main.go

package main

import (
	"github.com/AHuangMeow/hacker-support/httptool"
)

func main() {
	request, err := httptool.NewRequest(
		httptool.GETMETHOD,
		"https://gtainmuxi.muxixyz.com/api/v1/organization/code",
		"",
		httptool.DEFAULT,
	)
	if err != nil {
		panic(err)
	}

	response, err := request.SendRequest()
	if err != nil {
		panic(err)
	}

	response.ShowHeader()
}
```

运行结果：

```text
Send request successfully! Please check your response body.
response header:
Cache-Control : no-cache, no-store, max-age=0, must-revalidate, value
X-Content-Type-Options : nosniff
Content-Length : 360
Alt-Svc : h3=":443"; ma=2592000
Server : Caddy
X-Request-Id : b8e3c3d3-9493-4fdc-a452-f45d05d868d8
Access-Control-Allow-Origin : *
Expires : Thu, 01 Jan 1970 00:00:00 GMT
Last-Modified : Thu, 13 Nov 2025 00:48:23 GMT
X-Frame-Options : DENY
X-Xss-Protection : 1; mode=block
Content-Type : application/json; charset=utf-8
Date : Thu, 13 Nov 2025 00:48:23 GMT
Map-Fragments : muxi
Passport : eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJjb2RlIjoiQUh1YW5nTWVvdyIsImlhdCI6MTc2Mjk5NDkwMywibmJmIjoxNzYyOTk0OTAzfQ.t3uPobRQKbBE0xqafESebOCSXNfk4gvG8bHKXQfnSro
```

可以看到passport所在字段了，添加用于解析passport的逻辑：

```go
// checkpoint1/main.go

package main

import (
	"github.com/AHuangMeow/hacker-support/httptool"
)

func main() {
	request, err := httptool.NewRequest(
		httptool.GETMETHOD,
		"https://gtainmuxi.muxixyz.com/api/v1/organization/code",
		"",
		httptool.DEFAULT,
	)
	if err != nil {
		panic(err)
	}

	response, err := request.SendRequest()
	if err != nil {
		panic(err)
	}

	passport, err := response.GetHeader("Passport")
	if err != nil {
		panic(err)
	}

	request, err = httptool.NewRequest(
		httptool.GETMETHOD,
		"https://gtainmuxi.muxixyz.com/api/v1/organization/code",
		"",
		httptool.DEFAULT,
	)
	if err != nil {
		panic(err)
	}

	request.SetHeader("Passport", passport[0])

	response, err = request.SendRequest()
	if err != nil {
		panic(err)
	}

	response.ShowBody()
}
```

运行结果：

```text
Send request successfully! Please check your response body.
Send request successfully! Please check your response body.
Message:
response body:
1.Message:
OK
2.Text:
访问成功后，网站会给你返回信息，在header中找到你的passport。
将passport加入到你以后的每次请求头中。
完成上述步骤后，用代码访问 http://http-theft-bank.gtainccnu.muxixyz.com/api/v1/organization/secret_key，注意查收其中response的信息。
3.ExtraInfo:
```

继续：

```go
// checkpoint1/main.go

package main

import (
	"github.com/AHuangMeow/hacker-support/httptool"
)

func main() {
	request, err := httptool.NewRequest(
		httptool.GETMETHOD,
		"https://gtainmuxi.muxixyz.com/api/v1/organization/code",
		"",
		httptool.DEFAULT,
	)
	if err != nil {
		panic(err)
	}

	response, err := request.SendRequest()
	if err != nil {
		panic(err)
	}

	passport, err := response.GetHeader("Passport")
	if err != nil {
		panic(err)
	}

	request, err = httptool.NewRequest(
		httptool.GETMETHOD,
		"https://gtainmuxi.muxixyz.com/api/v1/organization/secret_key",
		"",
		httptool.DEFAULT,
	)
	if err != nil {
		panic(err)
	}

	request.SetHeader("Passport", passport[0])

	response, err = request.SendRequest()
	if err != nil {
		panic(err)
	}

	response.ShowBody()
}
```

运行结果：

```text
Send request successfully! Please check your response body.
Send request successfully! Please check your response body.
Message:
response body:
1.Message:
OK
2.Text:
恭喜你拿到了 passport，现在你可以着手准备骇入银行。
银行的第一道门是代码安全门，我们计划将错误代码写入此门来破解它。
但是这个门具有识别明文代码的功能，所以我们还需要一个密钥加密我们的错误代码，再写入至此门。
不需要担心，两者我们都为你提供了，你只需要解析 response 中的密文（在 ExtraInfo 中）来得到它们。
你现在的任务：
解析密文，获取 error_code 和 secret_key
使用 secret_key 加密 error_code
然后将加密过的 error_code 放入 请求body 并以 "正确的请求方法" 发送至 http://http-theft-bank.gtainccnu.muxixyz.com/api/v1/bank/gate , 同时注意response的信息。
3.ExtraInfo:
c2VjcmV0X2tleTpNdXhpU3R1ZGlvMjAzMzA0LCBlcnJvcl9jb2RlOmZvciB7Z28gZnVuYygpe3RpbWUuU2xlZXAoMSp0aW1lLkhvdXIpfSgpfQ==
```

[🏠](#主线任务)

---
#### Checkpoint2
`c2VjcmV0X2tleTpNdXhpU3R1ZGlvMjAzMzA0LCBlcnJvcl9jb2RlOmZvciB7Z28gZnVuYygpe3RpbWUuU2xlZXAoMSp0aW1lLkhvdXIpfSgpfQ==`, 貌似是一段base64编码的数据，先尝试解码

```go
// checkpoint2/main.go

package main

import (
	"fmt"

	"github.com/AHuangMeow/hacker-support/encrypt"
)

func main() {
	original, err := encrypt.Base64Decode("c2VjcmV0X2tleTpNdXhpU3R1ZGlvMjAzMzA0LCBlcnJvcl9jb2RlOmZvciB7Z28gZnVuYygpe3RpbWUuU2xlZXAoMSp0aW1lLkhvdXIpfSgpfQ==")
	if err != nil {
		panic(err)
	}

	fmt.Println(original)
}
```

运行结果：

```text
secret_key:MuxiStudio203304, error_code:for {go func(){time.Sleep(1*time.Hour)}()}
```

成功拿到secret_key和error_code，尝试攻破第一道密码安全门

```go
// checkpoint2/main.go

package main

import (
	"github.com/AHuangMeow/hacker-support/encrypt"
	"github.com/AHuangMeow/hacker-support/httptool"
)

func main() {
	encrypted, err := encrypt.AESEncryptOutInBase64([]byte("for {go func(){time.Sleep(1*time.Hour)}()}"), []byte("MuxiStudio203304"))
	if err != nil {
		panic(err)
	}

	request, err := httptool.NewRequest(
		httptool.GETMETHOD,
		"http://http-theft-bank.gtainccnu.muxixyz.com/api/v1/bank/gate",
		string(encrypted),
		httptool.DEFAULT,
	)
	if err != nil {
		panic(err)
	}

	request.SetHeader("Passport", "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJjb2RlIjoiQUh1YW5nTWVvdyIsImlhdCI6MTc2Mjk5NDkwMywibmJmIjoxNzYyOTk0OTAzfQ.t3uPobRQKbBE0xqafESebOCSXNfk4gvG8bHKXQfnSro")

	response, err := request.SendRequest()
	if err != nil {
		panic(err)
	}

	response.ShowBody()
}
```

运行结果：

```text
http 400 failed! the wrong data is: 
{"code":20301,"message":"你因为使用 错误的方法 写入病毒，被银行安全系统识别。很遗憾，你被逮捕了。（请尝试使用其他方法重新访问): ","data":nul
l}
Body:
{"code":20301,"message":"你因为使用 错误的方法 写入病毒，被银行安全系统识别。很遗憾，你被逮捕了。（请尝试使用其他方法重新访问): ","data":nul
l}
```

口瓜！补药抓窝口牙！

![尖叫](./Pictures/checkpoint2/2.1.png)

~~然后又被抓了几次~~

最终找到了正确的请求方法

```go
    request, err := httptool.NewRequest(
		httptool.PUTMETHOD,
		"http://http-theft-bank.gtainccnu.muxixyz.com/api/v1/bank/gate",
		string(encrypted),
		httptool.DEFAULT,
	)
```

运行结果：

```text
Send request successfully! Please check your response body.
Message:
response body:
1.Message:
OK
2.Text:
干的漂亮！你已经突破了第一扇门，请继续访问 http://http-theft-bank.gtainccnu.muxixyz.com/api/v1/bank/iris_recognition_gate 。
3.ExtraInfo:
```

[🏠](#主线任务)

---
#### Checkpoint3

让我们继续：

```go
// checkpoint3/main.go

package main

import (
	"github.com/AHuangMeow/hacker-support/httptool"
)

func main() {
	request, err := httptool.NewRequest(
		httptool.GETMETHOD,
		"http://http-theft-bank.gtainccnu.muxixyz.com/api/v1/bank/iris_recognition_gate",
		"",
		httptool.DEFAULT,
	)
	if err != nil {
		panic(err)
	}

	request.SetHeader("Passport", "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJjb2RlIjoiQUh1YW5nTWVvdyIsImlhdCI6MTc2Mjk5NDkwMywibmJmIjoxNzYyOTk0OTAzfQ.t3uPobRQKbBE0xqafESebOCSXNfk4gvG8bHKXQfnSro")

	response, err := request.SendRequest()
	if err != nil {
		panic(err)
	}

	response.ShowBody()
}
```

运行结果：

```text
Send request successfully! Please check your response body.
Message:
response body:
1.Message:
OK
2.Text:
你现在已经到第二扇门了，是虹膜识别安全门。
你需要向组织请求已准备好的虹膜样本，访问 http://http-theft-bank.gtainccnu.muxixyz.com/api/v1/organization/iris_sample 下载图片。
再将此图片上传至 http://http-theft-bank.gtainccnu.muxixyz.com/api/v1/bank/iris_recognition_gate 以破解此门，加油！
3.ExtraInfo:
```

继续：

```go
// checkpoint3/main.go

package main

import (
	"github.com/AHuangMeow/hacker-support/httptool"
)

func main() {
	request, err := httptool.NewRequest(
		httptool.GETMETHOD,
		"http://http-theft-bank.gtainccnu.muxixyz.com/api/v1/organization/iris_sample",
		"",
		httptool.DEFAULT,
	)
	if err != nil {
		panic(err)
	}

	request.SetHeader("Passport", "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJjb2RlIjoiQUh1YW5nTWVvdyIsImlhdCI6MTc2Mjk5NDkwMywibmJmIjoxNzYyOTk0OTAzfQ.t3uPobRQKbBE0xqafESebOCSXNfk4gvG8bHKXQfnSro")

	response, err := request.SendRequest()
	if err != nil {
		panic(err)
	}

	response.Save("./iris.png")

	request, err = httptool.NewRequest(
		httptool.POSTMETHOD,
		"http://http-theft-bank.gtainccnu.muxixyz.com/api/v1/bank/iris_recognition_gate",
		"./iris.png",
		httptool.FILE,
	)
	if err != nil {
		panic(err)
	}

	request.SetHeader("Passport", "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJjb2RlIjoiQUh1YW5nTWVvdyIsImlhdCI6MTc2Mjk5NDkwMywibmJmIjoxNzYyOTk0OTAzfQ.t3uPobRQKbBE0xqafESebOCSXNfk4gvG8bHKXQfnSro")

	response, err = request.SendRequest()
	if err != nil {
		panic(err)
	}

	response.ShowBody()
}
```

运行结果：

```text
Send request successfully! Please check your response body.
Send request successfully! Please check your response body.
Message:
response body:
1.Message:
OK
2.Text:
还剩最后一道门了！
我们需要银行结构图碎片，这些碎片就隐藏在前面某四个路由的响应头中，位于 map-fragments 字段。
将它们用"/"拼起来就是最后一道门的所在位置！注意response的信息。
3.ExtraInfo:
```

[🏠](#主线任务)

---
#### Checkpoint4

近在咫尺了，都试试吧：

```go
// checkpoint4/main.go

package main

import (
	"fmt"

	"github.com/AHuangMeow/hacker-support/encrypt"
	"github.com/AHuangMeow/hacker-support/httptool"
)

func main() {
	passport := "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJjb2RlIjoiQUh1YW5nTWVvdyIsImlhdCI6MTc2Mjk5NDkwMywibmJmIjoxNzYyOTk0OTAzfQ.t3uPobRQKbBE0xqafESebOCSXNfk4gvG8bHKXQfnSro"

	request, err := httptool.NewRequest(
		httptool.GETMETHOD,
		"https://gtainmuxi.muxixyz.com/api/v1/organization/code",
		"",
		httptool.DEFAULT,
	)
	if err != nil {
		panic(err)
	}

	request.SetHeader("Passport", passport)

	response, err := request.SendRequest()
	if err != nil {
		panic(err)
	}

	pcs1, err := response.GetHeader("map-fragments")
	if err != nil {
		panic(err)
	}

	request, err = httptool.NewRequest(
		httptool.GETMETHOD,
		"https://gtainmuxi.muxixyz.com/api/v1/organization/secret_key",
		"",
		httptool.DEFAULT,
	)
	if err != nil {
		panic(err)
	}

	request.SetHeader("Passport", passport)

	response, err = request.SendRequest()
	if err != nil {
		panic(err)
	}

	pcs2, err := response.GetHeader("map-fragments")
	if err != nil {
		panic(err)
	}

	encrypted, err := encrypt.AESEncryptOutInBase64([]byte("for {go func(){time.Sleep(1*time.Hour)}()}"), []byte("MuxiStudio203304"))
	if err != nil {
		panic(err)
	}
	request, err = httptool.NewRequest(
		httptool.PUTMETHOD,
		"http://http-theft-bank.gtainccnu.muxixyz.com/api/v1/bank/gate",
		string(encrypted),
		httptool.DEFAULT,
	)
	if err != nil {
		panic(err)
	}

	request.SetHeader("Passport", passport)

	response, err = request.SendRequest()
	if err != nil {
		panic(err)
	}

	pcs3, err := response.GetHeader("map-fragments")
	if err != nil {
		panic(err)
	}

	request, err = httptool.NewRequest(
		httptool.POSTMETHOD,
		"http://http-theft-bank.gtainccnu.muxixyz.com/api/v1/bank/iris_recognition_gate",
		"../checkpoint3/iris.png",
		httptool.FILE,
	)
	if err != nil {
		panic(err)
	}

	request.SetHeader("Passport", passport)

	response, err = request.SendRequest()
	if err != nil {
		panic(err)
	}

	pcs4, err := response.GetHeader("map-fragments")
	if err != nil {
		panic(err)
	}

	request, err = httptool.NewRequest(
		httptool.GETMETHOD,
		"http://http-theft-bank.gtainccnu.muxixyz.com/api/v1/organization/iris_sample",
		"",
		httptool.DEFAULT,
	)
	if err != nil {
		panic(err)
	}

	request.SetHeader("Passport", passport)

	response, err = request.SendRequest()
	if err != nil {
		panic(err)
	}

	pcs5, err := response.GetHeader("map-fragments")
	if err != nil {
		panic(err)
	}

	fmt.Println(pcs1, pcs2, pcs3, pcs4, pcs5)
}
```

运行结果：

```text
Send request successfully! Please check your response body.
Send request successfully! Please check your response body.
Send request successfully! Please check your response body.
Send request successfully! Please check your response body.
Send request successfully! Please check your response body.
[muxi] [backend] [computer] [examination] []
```

muxi/backend/computer/examination, 就是这里了：

```go
// checkpoint4/main.go

package main

import (
	"github.com/AHuangMeow/hacker-support/httptool"
)

func main() {
	passport := "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJjb2RlIjoiQUh1YW5nTWVvdyIsImlhdCI6MTc2Mjk5NDkwMywibmJmIjoxNzYyOTk0OTAzfQ.t3uPobRQKbBE0xqafESebOCSXNfk4gvG8bHKXQfnSro"

	request, err := httptool.NewRequest(
		httptool.GETMETHOD,
		"https://gtainmuxi.muxixyz.com/api/v1/muxi/backend/computer/examination",
		"",
		httptool.DEFAULT,
	)
	if err != nil {
		panic(err)
	}

	request.SetHeader("Passport", passport)

	response, err := request.SendRequest()
	if err != nil {
		panic(err)
	}

	response.ShowBody()
}
```

运行结果：

```text
Send request successfully! Please check your response body.
Message:
response body:
1.Message:
OK
2.Text:
AHuangMeow，真亏你能来到这里！在你眼前的就是最后的密码门了。
但是密码位数未知，看来只能通过全排列程序去暴力破解。

>示例如下：
============================================
输入：
3
1 2 3
输出：
[[1 2 3][1 3 2][2 1 3][2 3 1][3 1 2][3 2 1]]
============================================

>代码模板:

func permute(nums []int) [][]int {
    // insert your code

}

func main() {
    var n int
    fmt.Scanf("%d", &n)

        testSlice := make([]int, n)
    // 标准输入n个不重复的数字

        res := permute(testSlice)
    fmt.Println(res)
}

请在完成此程序后上传至 http://http-theft-bank.gtainccnu.muxixyz.com/api/v1/muxi/backend/computer/examination 来破解最后的密码门
3.ExtraInfo:
```

[🏠](#主线任务)

---
#### Checkpoint5

算法题时间♥️

```go
// checkpoint5/code/main.go

package main

import (
    "fmt"
    "sort"
)

func Insert(slice []int, index int, n int) []int {
    result := make([]int, 0, len(slice)+1)
    result = append(result, slice[:index]...)
    result = append(result, n)
    result = append(result, slice[index:]...)
    return result
}

func permute(nums []int) [][]int {
    if len(nums) == 1 {
        return [][]int{{nums[0]}}
    }

    permuteTail := permute(nums[1:])
    var result [][]int

    for _, arr := range permuteTail {
        for i := 0; i <= len(arr); i++ {
            result = append(result, Insert(arr, i, nums[0]))
        }
    }

    return result
}

func main() {
    var n int
    fmt.Scanf("%d", &n)

    testSlice := make([]int, n)
    for i := 0; i < n; i++ {
        fmt.Scan(&testSlice[i])
    }

    res := permute(testSlice)

    sort.Slice(res, func(i, j int) bool {
        for k := 0; k < len(res[0]); k++ {
            if res[i][k] != res[j][k] {
                return res[i][k] < res[j][k]
            }
        }
        return false
    })
    
    fmt.Println(res)
}
```

```go
// checkpoint5/main.go

package main

import (
	"github.com/AHuangMeow/hacker-support/httptool"
)

func main() {
	request, err := httptool.NewRequest(
		httptool.POSTMETHOD,
		"http://http-theft-bank.gtainccnu.muxixyz.com/api/v1/muxi/backend/computer/examination",
		"./code/main.go",
		httptool.FILE,
	)
	if err != nil {
		panic(err)
	}

	request.SetHeader("Passport", "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJjb2RlIjoiQUh1YW5nTWVvdyIsImlhdCI6MTc2Mjk5NDkwMywibmJmIjoxNzYyOTk0OTAzfQ.t3uPobRQKbBE0xqafESebOCSXNfk4gvG8bHKXQfnSro")

	response, err := request.SendRequest()
	if err != nil {
		panic(err)
	}

	response.ShowBody()
}
```

运行结果：

```text
Send request successfully! Please check your response body.
Message:
response body:
1.Message:
OK
2.Text:
END
我就知道你能成功！Backend组织欢迎你！
3.ExtraInfo:
```

[🏠](#主线任务)

---
#### 小结

又是一周摸鱼结束了awa，这周的任务相当好玩

期待下周💤️

~~↓↓↓认真学习~~
![学习](./Pictures/summary/learning.png)

[🏠](#主线任务)