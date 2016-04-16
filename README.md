# zlog
loggin daily activity

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
* 

## 2016-04-13

via http://stackoverflow.com/questions/21917661/can-you-connect-to-amazon-elasticache-redis-outside-of-amazon

* want to redis-cli from local to aws elasticache(redis)
* ssh port forwarding do the trick

```
ssh -f -N -L6379:<your redis node endpoint>:6379 <your EC2 node that you use to connect to redis>
```
