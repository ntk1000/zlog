# zlog
loggin daily activity

## 2017-10-05

table diff for mysql via http://d.hatena.ne.jp/CAMUS/20060111/1136984749

* same table layout
* want to check key exists only on one side

```
SELECT KEY FROM (
　SELECT KEY FROM TBL_A
　UNION ALL
　SELECT KEY FROM TBL_B
)
GROUP BY KEY
HAVING COUNT(*) = 1
```

* want to check all columns differs one another = table diff

```
SELECT KEY FROM (
　SELECT * FROM TBL_A
　UNION
　SELECT * FROM TBL_B
)
GROUP BY KEY
HAVING COUNT(*) = 2
```

## 2017-05-02

terraform loop

```
resource "aws_ecr_repository" "example" {
  count = "${var.repos_count}"
  name = "${element(var.repos,count.index)}"
}

variable "repos_count" {
 default = 3
}

variable "repos" {
 default = ["ex1","ex2","ex3"]
}
```

## 2017-04-26

fluentd log check via http://qiita.com/yn-misaki/items/2d9aa010c5dccd44e156

fluentd pos file format
```
filename            read position           inode of file
/var/log/target.log 0000000005fe73a1        0000000000046bee
```

convert inode from hexadecimal to decimal
```
# replace lowercase with uppercase
$ echo "obase=10;ibase=16;0000000000046BEE" | bc
289774
```

check file inode
```
$ ls -i /var/log/target.log
289774 /var/log/target.log
```

convert read pos from hexadecimal to decial
```
$ echo "obase=10;ibase=16;0000000005FE73A1" | bc
100561825
```

check log from position
```
head -c 100561825 /var/log/target.log
```

## 2017-04-21

about network

* /etc/sysconfig/network-scripts/ifcfg-eth0

```
固定IPアドレスの場合。
--------------------------------
DEVICE=eth0
BOOTPROTO=static
BROADCAST=192.168.1.255
HWADDR=00:1E:90:AC:94:6A
IPADDR=192.168.1.11
NETMASK=255.255.255.0
NETWORK=192.168.1.0
ONBOOT=yes
--------------------------------


DEVICE・・・デバイス名eth0 eth1等を設定
BOOTPROTO・・・固定IPの場合:none,static DHCPでIP取得の場合:dhcpと設定
BROADCAST・・・ブロードキャストアドレスの設定
HWADDR・・・NICのMACアドレスを設定
IPADDR・・・IPアドレスの指定
NETMASK・・・サブネットマスクの設定
NETWORK・・・ネットワークアドレスの設定
ONBOOT・・・起動時に有効にするかどうか(yes/no) 

```

## 2017-04-20

rewrite git history (rm secret files)
via 
* http://qiita.com/go_astrayer/items/6e39d3ab16ae8094496c
* http://qiita.com/wnoguchi/items/62f5e64ef2ae14b4f0ee

```

# remove 
git filter-branch --tree-filter "rm -f [/path/to/file]" HEAD
git filter-branch --tree-filter "rm -f -r [/path/to/directory] " HEAD

# gc
git gc --aggressive --prune=now

# push
git push -f

```

nginx gzip on via http://qiita.com/cubicdaiya/items/2763ba2240476ab1d9dd

```
gzip on;
# add Content-Type with `gzip_types` directive, cause `gzip on` directive only compress text/html
gzip_types text/css text/javascript;
```

## 2017-04-05

 覚えておきたいlsコマンドのオプション15選
* -aオプション：すべて表示
* -lオプション：ファイルの詳細も表示する
* -1オプション：リストを縦に並べる
* -rオプション：逆順で表示する
* -tオプション：更新時間順に並べる
* -Sオプション：ファイルサイズ順でソートする
* -Xオプション：ファイルを拡張子ごとにまとめる
* -Rオプション：ディレクトリ内容を再帰的に表示する
* --full-timeオプション：タイムスタンプの詳細を表示する
* -mオプション：ファイル名をカンマで区切って表示する
* -hオプション：単位を読みやすい形式で表示する
* -kオプション：キロバイト単位で表示する
* -iオプション：ファイル名の左にi-node番号を表示する
* -Fオプション：情報の付加
* --helpオプション：ヘルプの表示

## 2017-02-20

docker (for mac) gc

```
docker rm $(docker ps -q -f 'status=exited')
docker rmi $(docker images -q -f "dangling=true")
```


## 2017-02-09

curl only shows headers via http://te2u.hatenablog.jp/entry/2015/08/04/001554

```
curl -s -D - http://example.com -o /dev/null
```

-s : silent mode
-D : show headers
-o : out responcebody to...

## 2017-02-07

rm under s3 bucket

```
aws s3 rm s3://bucket-name/ --recursive
```


## 2017-01-24

about td-agent

```
# conf dry-run
sudo td-agent --dry-run -c /etc/td-agent/td-agent.conf
# init
sudo /etc/init.d/td-agent status (etc.)
# log
sudo tail -f /var/log/td-agent/td-agent.log
```


## 2017-01-23

log location of ec2 user-data

```
less /var/log/cloud-init-output.log
```

## 2017-01-12

```
sudo td-agent-gem install fluent-plugin-rewrite-tag-filter
sudo td-agent-gem install fluent-plugin-slack
```


## 2017-01-11

via
* https://coreos.com/os/docs/latest/scheduling-tasks-with-systemd-timers.html
* http://stackoverflow.com/questions/29086918/docker-compose-to-coreos
* http://qiita.com/TakiTake@github/items/b66aeb1eb266f97fafe5

auto start docker-compose on coreos

```
sudo coreos-cloudinit --from-file /path/to/cloud-config.yml
```

## 2016-12-12

via http://nekop.hatenablog.com/entry/2015/08/21/160436
docker clean-up w/o error

```
docker ps -q -f status=exited | xargs --no-run-if-empty docker rm
docker images -q -f dangling=true | xargs --no-run-if-empty docker rmi
```


## 2016-12-08

exclude commented out and blank line

```
grep -v -e '#' -e '^$' ./httpd.conf
```

wc under multi dir/files

```
wc -l ./*/*.conf | sort | $PAGER
```

## 2016-12-07

show ruby configure options

```
ruby -r rbconfig -e 'puts RbConfig::CONFIG["configure_args"]'
```

## 2016-12-01

destroy specific target with tf file via terraform

```
# plan
$ terraform plan -destroy -target=aws_efs_file_system.test -target=aws_efs_mount_target.test

# destroy
$ terraform destroy -target=aws_efs_file_system.test -target=aws_efs_mount_target.test
```


## 2016-11-18

erase specific git file/directory

```
file
$ git filter-branch -f --index-filter 'git rm --cached --ignore-unmatch filename' HEAD

directory
$ git filter-branch -f --index-filter 'git rm -rf --cached --ignore-unmatch dirname' HEAD

push
$ git push --force origin branch

```

## 2016-11-01

! not for production
docker-compose on coreos
via http://www.ericluwj.com/2015/10/20/installing-docker-compose-in-coreos.html
* sudo su -
* mkdir -p /opt/bin
* curl -L https://github.com/docker/compose/releases/download/X.Y.Z/docker-compose-`uname -s`-`uname -m` > /opt/bin/docker-compose
* chmod +x /opt/bin/docker-compose
* that's it

## 2016-10-31

coreos on idcf cloud

* add latest ISO(change URL from https to http)
* create server(name:core1) from ISO with newly created SSH Key
* access to `core1` via web console and type `sudo passwd core`
* create public ip and add firewall rule , port forward to `core1`
* ssh to `core1`
* create cloud-config.yml
* install coreos to disk via `sudo coreos-install -d /dev/sda -C stable -c ./cloud-config.yml`

```
#cloud-config

coreos:
 units:
   -
     name: docker.service
     command: restart
   -
     name: settimezone.service
     command: start
     content: |
       [Unit]
       Description=Set Timezone
       [Service]
       ExecStart=/usr/bin/timedatectl set-timezone Asia/Tokyo
       RemainAfterExit=yes
       Type=oneshot
```


## 2016-10-28

how 2 get image via aws ecr on docker / docker-compose?

* `aws ecr get-login` command provides `docker login` to aws ecr
* sample shell as follows

```
# check credentails
: "${AWS_ACCESS_KEY_ID?env empty}"
: "${AWS_SECRET_ACCESS_KEY?env empty}"
: "${AWS_DEFAULT_REGION?env empty}"

echo "docker login via aws ecr command"
#eval "$(docker-machine env default)"
$(aws ecr get-login --region $AWS_DEFAULT_REGION)
```

after `docker login` passes, docker / docker-compose can handle docker images on aws ecr.
**token via `get-login` command is valid for only 12 hours.**

## 2016-10-24

get stdin in shell

```
while read line
do
	result=$result$line
done <  "${1:-/dev/stdin}"

```

trim last charactor of variables

```
result=hogehoge
echo ${result:0:${#result}-1}
```

## 2016-10-20

terraform cheetsheat

* terraform loads all of `.tf` file
* `terraform plan` shows what changes will apply to infra (aka dry-run)
* `terraform apply` creates "real" resources
* `terraform show` shows actual infra status
* `terraform plan -destroy` shows what resources will destroy
* `terraform destroy` destroy infra


## 2016-08-04

docker entrypoint override

* overrider with --entrypoint flag
* command with end parameter

```
$ docker run -it --entrypoint /bin/consul gliderlabs/consul-server version
Consul v0.6.4
...
```

## 2016-07-15

rbenv init

```
eval "$(rbenv init -)"
rbenv rehash
gem install hogehoge
```

## 2016-06-27

personal memo

* https://talks.golang.org/2012/10things.slide 
* https://www.appneta.com/blog/automated-testing-with-docker/
* http://developers.linecorp.com/blog/ja/?p=3392
* https://thepracticalsysadmin.com/ecs-cluster-turnup-with-coreos-and-terraform/
* http://postd.cc/eight-docker-development-patterns/
* http://yut.hatenablog.com/entry/20130617/1371425713


## 2016-06-07

set timezone and ecs-agent to coreos

* userdata

```
#cloud-config

coreos:
 units:
   -
     name: settimezone.service
     command: start
     content: |
       [Unit]
       Description=Set Timezone
       [Service]
       ExecStart=/usr/bin/timedatectl set-timezone Asia/Tokyo
       RemainAfterExit=yes
       Type=oneshot
   -
     name: amazon-ecs-agent.service
     command: start
     runtime: true
     content: |
       [Unit]
       Description=AWS ECS Agent
       Documentation=https://docs.aws.amazon.com/AmazonECS/latest/developerguide/
       Requires=docker.socket
       After=docker.socket
       [Service]
       Environment=ECS_CLUSTER=your-ecs-cluster-name
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
                                     --env=ECS_AVAILABLE_LOGGING_DRIVERS=[\"json-file\",\"syslog\",\"fluentd\",\"awslogs\"] \
                                     amazon/amazon-ecs-agent:${ECS_VERSION}
```


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
