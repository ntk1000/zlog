# zlog
loggin daily activity

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
