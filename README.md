# zlog
loggin daily activity

## 2016-06-06

docker security check

* run your docker containers e.g. via docker-compose
* then git clone https://github.com/docker/docker-bench-security.git
* cd docker-bench-security
* docker-compose run --rm docker-bench-security
* docker-bench-security prints security check result via stdout

## 2016-05-27

slack time signal

```
/remind #channel to morning standup at 10am every weekday
/remind #channel to evening standup at 5pm every weekday
/remind list
```

## 2016-05-24

install redis-cli on ec2

```
$ sudo yum install -y gcc
$ wget http://download.redis.io/redis-stable.tar.gz
$ tar xvzf redis-stable.tar.gz
$ cd redis-stable
$ make
$ src/redis-cli -h %elasticacheendpoint% -p 6379
```

## 2016-05-17

setting timezone at alpine linux on docker

```

FROM gliderlabs/alpine:latest

RUN apk --no-cache add tzdata && \
  cp /usr/share/zoneinfo/Asia/Tokyo /etc/localtime && \
  apk del tzdata 

```

## 2016-05-11

mysql(RDS or Aurora) dump restore from local file

```
$ ls
dump.sql

$ mysql -h your.rds.amazonaws.com -P 3306 -u userid -p
mysql> source dump.sql
```

## 2016-05-10

aws ecs service limits

via 
http://docs.aws.amazon.com/ja_jp/AmazonECS/latest/developerguide/service_limits.html

The following table provides the default limits for Amazon ECS for an AWS account which can be changed. For more information, see AWS Service Limitsin the Amazon Web Services General Reference.

Resource	Default Limit
Number of clusters per region, per account	1000
Number of container instances per cluster	1000
Number of services per cluster	500

The following table provides other limitations for Amazon ECS that cannot be changed.

Resource	Default Limit
Number of load balancers per service	1
Number of tasks per service (the desired count)	1000
Number of tasks launched (count) per run-task	10
Number of container instances per start-task	10
Throttle on container instance registration rate	1 per second / 60 max per minute
Task definition size limit	32 KiB
Task definition max containers	10
Throttle on task definition registration rate	1 per second / 60 max per minute


## 2016-05-09

running coreos on aws ecs

via
* https://coreos.com/os/docs/latest/booting-on-ecs.html
* https://coreos.com/os/docs/latest/booting-on-ec2.html

* on ec2 console
* create launchconfiguration with coreos ami and user-data

user-data are as follows

* get etcd token via https://discovery.etcd.io/new?size=X
* set ECS_CLUSTER env by ecs cluster name
* start amazon-ecs-agent.service

```
#cloud-config

coreos:
  etcd2:
    # generate a new token for each unique cluster from https://discovery.etcd.io/new?size=3
    # specify the initial size of your cluster with ?size=X
    discovery: https://discovery.etcd.io/a4ca4dd8ac25ff375ff9439c929b9eb3
    # multi-region and multi-cloud deployments need to use $public_ipv4
    advertise-client-urls: http://$private_ipv4:2379,http://$private_ipv4:4001
    initial-advertise-peer-urls: http://$private_ipv4:2380
    # listen on both the official ports and the legacy ports
    # legacy ports can be omitted if your application doesn't depend on them
    listen-client-urls: http://0.0.0.0:2379,http://0.0.0.0:4001
    listen-peer-urls: http://$private_ipv4:2380,http://$private_ipv4:7001
  units:
    - name: etcd2.service
      command: start
    - name: fleet.service
      command: start
    - name: amazon-ecs-agent.service
      command: start
      runtime: true
      content: |
       [Unit]
       Description=AWS ECS Agent
       Documentation=https://docs.aws.amazon.com/AmazonECS/latest/developerguide/
       Requires=docker.socket
       After=docker.socket
       [Service]
       Environment=ECS_CLUSTER=my-ecs-cluster-name
       Environment=ECS_LOGLEVEL=info
       Environment=ECS_VERSION=latest
       Restart=on-failure
       RestartSec=30
       RestartPreventExitStatus=5
       SyslogIdentifier=ecs-agent
       ExecStartPre=-/bin/mkdir -p /var/log/ecs /var/ecs-data /etc/ecs
       ExecStartPre=-/usr/bin/touch /etc/ecs/ecs.config
       ExecStartPre=-/usr/bin/docker kill ecs-agent
       ExecStartPre=-/usr/bin/docker rm ecs-agent
       ExecStartPre=/usr/bin/docker pull amazon/amazon-ecs-agent:${ECS_VERSION}
       ExecStart=/usr/bin/docker run --name ecs-agent \
                                     --env-file=/etc/ecs/ecs.config \
                                     --volume=/var/run/docker.sock:/var/run/docker.sock \
                                     --volume=/var/log/ecs:/log \
                                     --volume=/var/ecs-data:/data \
                                     --volume=/sys/fs/cgroup:/sys/fs/cgroup:ro \
                                     --volume=/run/docker/execdriver/native:/var/lib/docker/execdriver/native:ro \
                                     --publish=127.0.0.1:51678:51678 \
                                     --env=ECS_LOGFILE=/log/ecs-agent.log \
                                     --env=ECS_LOGLEVEL=${ECS_LOGLEVEL} \
                                     --env=ECS_DATADIR=/data \
                                     --env=ECS_CLUSTER=${ECS_CLUSTER} \
                                     amazon/amazon-ecs-agent:${ECS_VERSION}
```

* create elb and autoscaling

* on ecs console
* create ecs cluster
* start service

## 2016-04-28

docker network time out

```
docker-machine restart default
eval $(docker-machine env default)
```

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

* create autoscaling

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
