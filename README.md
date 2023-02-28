# dcr-redis
Swoole Redis连接池 自动归还 
基础的Redis分布式 互斥锁

## 说明
    使用 Lock（互斥锁） 之前需要先 初始化 Redis
    如果你是在多进程的模式下使用 那么请在每个进程 Start 的时候 初始化Redis
    如若不然会导致 你的连接 每个进程都在使用，那么就会崩~
    swoole 的很多框架 都有实现 什么Redis代理的 你连进程隔离都不用管自动维护了但是好像都不支持完全协程化的写法 
    
## 安装
`composer require guanhui07/redis`

## 使用

```php


    \DcrRedis\Redis::initialize((new RedisConfig())
       ->withHost('127.0.0.1')
        ->withPort(6379)
        ->withAuth('')
        ->withDbIndex(1)
        ->withTimeout(1),
        64
    );
    
    go(function (){
        $ArtLock = new \DcrRedis\Lock('goods_100',5000);
        $status = $ArtLock->lock();
        if (!$status){
            echo '请求一:进入失败'.PHP_EOL;
            return;
        }
        echo '请求一:进入成功'.PHP_EOL;
        \Co\System::sleep(7);
        $status = $ArtLock->unLock();
        if(!$status){
            echo '请求一:退出失败'.PHP_EOL;
            return;
        }
        echo '请求一:退出成功'.PHP_EOL;
    });
    go(function (){
        $ArtLock = new \DcrRedis\Lock('goods_100',5000);
        $status = $ArtLock->lock();
        if (!$status){
            echo '请求二:进入失败'.PHP_EOL;
            return;
        }
        echo '请求二:进入成功'.PHP_EOL;
        \Co\System::sleep(3);
        $status = $ArtLock->unLock();
        if(!$status){
            echo '请求二:退出失败'.PHP_EOL;
            return;
        }
        echo '请求二:退出成功'.PHP_EOL;
    });
    });
```

  
    
## 我的其他包：
https://github.com/guanhui07/dcr  借鉴Laravel实现的 PHP Framework ，FPM模式、websocket使用的workerman、支持容器、PHP8特性attributes实现了路由注解、中间件注解、Laravel Orm等特性

https://github.com/guanhui07/redis Swoole模式下 Redis连接池

https://github.com/guanhui07/facade  facade、门面 fpm模式下可使用

https://github.com/guanhui07/dcr-swoole-crontab 基于swoole实现的crontab秒级定时任务

https://github.com/guanhui07/database  基于 illuminate/database 做的连接池用于适配Swoole的协程环境

https://github.com/guanhui07/dcr-swoole  高性能PHP Framework ，Cli模式，基于Swoole实现，常驻内存，协程框架，支持容器、切面、PHP8特性attributes实现了路由注解、中间件注解、支持Laravel Orm等特性    
    
