# zlog
loggin daily activity

## 2016-04-27

http://yuuki.hatenablog.com/entry/diy-container

## 2016-04-26

https://github.com/progrium/entrykit

## 2016-04-25

sick sick

## 2016-04-24

i'm sick

## 2016-04-23

https://play.golang.org/p/TYOd9ELo2m

https://play.golang.org/p/Xs2SQ59gez

http://play.golang.org/p/VdKMLKMYse

http://play.golang.org/p/sCcfzFuzqR

https://play.golang.org/p/tlaYWTKXGr

https://play.golang.org/p/DoBYGQruo6


## 2016-04-22

* golang testing
  * via https://medium.com/@benbjohnson/structuring-tests-in-go-46ddee7a25c#.x9lf1gida
  * no frameworks
  * "underscore test" file
  * test-specific types
  *
  * https://speakerdeck.com/mitchellh/advanced-testing-with-go is also fine slide for testing

* i love test 

## 2016-04-21

```
package main
 
import(
  "fmt"
  "reflect"
)

// Data Model
type User struct {
  Id  int
  Name string
  Origin string
  Query func()
  Tags map[string]string
  Fields []string
}
 
func main(){

  list := GetAttrName(&User{})
  // slice with ... options passes ... parameters
  PrintName(list...)

}

// GetAttrName returns struct attributes name
func GetAttrName(m interface{}) ([]string) {

  typ := reflect.TypeOf(m)
  typ = typ.Elem()

  attrs := []string{}

  // loop through the struct's fields and set the map
  for i := 0; i < typ.NumField(); i++ {
    attrs = append(attrs,typ.Field(i).Name)
  }

  return attrs
}

// PrintName passing arguments to ... parameters
func PrintName(list ...string){
  for _, name := range list {
    fmt.Println(name)
  }
}
```

## 2016-04-20

* sha256

```
package main

import (
	"fmt"
	"crypto/sha256"
	"strconv"
)

func main() {
	for i := 1; i <= 100; i++ {
		si := strconv.Itoa(i)
		fmt.Printf("SHA-256 : %x\n", sha256.Sum256([]byte(si)))
	}
}

```

## 2016-04-19

* neovim / deoplete error
  * https://github.com/Shougo/deoplete.nvim/issues/223

```
Failed to load python3 host. You can try to see what happened by starting Neovim with the environment variable $NVIM_PYTHON_LOG_FILE set to a file and
 opening the generated log file. Also, the host stderr will be available in Neovim log, so it may contain useful information. See also ~/.nvimlog.
```

below script fixed my issue

```
pip3 --no-cache-dir install -U neovim
```



## 2016-04-18

* go dynamic json

```
package main

import (
  "fmt"
  "strconv"
  "encoding/json"
)

type Hoge struct {
  Name string `json:name`
  Tags map[string]string `json:tags`
}

func main() {
  m := map[string]string{}
  for i := 1; i < 6; i ++ {
    ii := strconv.Itoa(i)
    m["tag"+ii] = "text"+ii
  }
  b, _ := json.Marshal(m)

  fmt.Println(m)
  // map[tag3:text3 tag4:text4 tag5:text5 tag1:text1 tag2:text2]
  fmt.Printf("%s\n",b)
  // {"tag1":"text1","tag2":"text2","tag3":"text3","tag4":"text4","tag5":"text5"}

  h := Hoge{Name:"hoge",Tags:m}
  b2, _ := json.Marshal(h)

  fmt.Println(h)
  // {hoge map[tag1:text1 tag2:text2 tag3:text3 tag4:text4 tag5:text5]}
  fmt.Printf("%s\n",string(b2))
  // {"Name":"hoge","Tags":{"tag1":"text1","tag2":"text2","tag3":"text3","tag4":"text4","tag5":"text5"}}

}
```

## 2016-04-17

* hubot lgtm

https://github.com/bouzuya/hubot-lgtm

## 2016-04-16

* go-kit

http://peter.bourgon.org/go-kit/

## 2016-04-15

* security checkpoint
  * firewall with port
  * ssh setting /sshd_config
  * ssl
    * https://www.ssllabs.com/ssltest/ でチェックできる
  * response header
  * data暗号化
  * tool
    * OWASP ZedAttackProxy
    * OpenCVE

* mkr with jq

```
# 無名roleを抽出
mkr hosts | jq '.[] | { name: .name, id: .id, role: .roleFullnames  } | select(.role==null)'
```

## 2016-04-14

via https://github.com/golang/go/issues/14795

* go binary failed to run on 2016.03 amazon linux ami
* seems to GOARCH=i386 option causes this error

## 2016-04-13

via http://stackoverflow.com/questions/21917661/can-you-connect-to-amazon-elasticache-redis-outside-of-amazon

* want to redis-cli from local to aws elasticache(redis)
* ssh port forwarding do the trick

```
ssh -f -N -L6379:<your redis node endpoint>:6379 <your EC2 node that you use to connect to redis>
```
