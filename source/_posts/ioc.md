title: 如何在Lumen框架下写一个自己的Log类？
date: 2015-12-18 16:03:01
categories: 日薪越亿
tags: [lumen, ioc, Log]
---

## 前记
今天在使用__lumen__自带的类Log的时候，发现每次记录一个Log，总会在`storage/logs/lumen.log`文件末尾追加一条记录，那么问题来了，时间久了，lumen.log 这个文件就会非常大，而且也不利于管理，于是打算写一个自己的log类，在各位网友博客小伙伴的帮助下，终于完成了，记录一下写Log的历程，希望能帮助一些后来打算改造Log类的小伙伴吧。

## Lumen

开始前，先让我们了解下lumen：

Lumen 是一个由 Laravel 元件搭建而成的微框架, 由 Laravel 官方维护. Lumen 为速度而生, 是当前最快的 PHP 框架之一, 甚至比类似的微框架 Silex 速度还要快.

Lumen 比其他微框架的优点是, 构建在 Laravel 之上, 使其具备 Laravel 强大的功能, 如 路由, 依赖注入, Eloquent ORM, 数据库迁移管理, 队列和计划任务等.

Laravel 本来就是一个功能齐全, 速度飞快的框架, 但是 Lumen 因为去除了很多 Laravel 的配置和可自定义的选项, 速度越加飞快, 毫秒必争.

飞快的速度, 再加上 Laravel 非常方便的功能, 使用 Lumen 开发应用会是非常愉悦的体验.

Lumen 专为微服务或者 API 设计, 举个例子, 如果你的应用里面有部分业务逻辑的请求频率比较高, 就可以单独把这部分业务逻辑拿出来, 使用 Lumen 来构建一个小 App.

因为 Lumen 是对 Laravel 优化了框架的加载机制, 所以 Lumen 对资源的要求少很多.

当然, 你可以使用 队列系统 与你的主 Laravel 应用进行交互. Laravel 和 Lumen 从一开始就是设计成能一起很好的工作, 并且, 配合使用, 允许你构架一个强大的, 以微服务为驱动的应用程序.

Lumen 同时也非常适用于构建 API 接口, 此类型的应用通常情况下不需要具备 全栈框架 的所有功能, 如 HTTP 会话管理, Cookies, 和模版系统.

## 历程

简单的了解了Lumen，那么接下来就来介绍Log的封装过程吧，在介绍前，各位看官如果对lumen的加载机制不清楚，不妨看下这篇文章：[Laravel 架构中的 Container/ServiceProvider/Facade](https://phphub.org/topics/769)。

### 了解系统的Log类

Lumen文档 [错误和日志](http://laravelacademy.org/post/465.html) 介绍了如何使用Lumen的错误和日志类，而且该日志记录器提供了[RFC 5424](http://tools.ietf.org/html/rfc5424)中定义的七种日志级别：`alert, critical, error,warning, notice, info 和 debug`。我们看到以下代码
```php
use Log;
```
既然Log类可以直接应用，说明Lumen在初始化的时候就定义了 Log 的 `namespace` 或者 起了 Log的 `aliases`, 首先找namespace，没有找到定义Log 的 namespace的地方，那肯定是起了 Log 的别名，找了些资料，终于发现Log的加载流程：

0. 根目录下`bootstrap\app.php`文件有一行代码：
``` php
$app->withFacades();
```
这一行是调用 `vendor\laravel\lumen-framework\src\Application->withFacades()` 方法，会加载Lumen的一些Facades，下面列了 application的几个方法：

``` php
/**
 * Create a new Lumen application instance.
 *
 * @param  string|null  $basePath
 * @return void
 */
public function __construct($basePath = null)
{
    date_default_timezone_set(env('APP_TIMEZONE', 'Asia/Chongqing'));

    $this->basePath = $basePath;
    $this->bootstrapContainer();
    $this->registerErrorHandling();
}

/**
 * Bootstrap the application container.
 *
 * @return void
 */
protected function bootstrapContainer()
{
    static::setInstance($this);

    $this->instance('app', $this);
    $this->instance('path', $this->path());

    $this->registerContainerAliases();
}
/**
 * Register the facades for the application.
 *
 * @return void
 */
public function withFacades()
{
    Facade::setFacadeApplication($this);

    if (! static::$aliasesRegistered) {
        static::$aliasesRegistered = true;

        class_alias('Illuminate\Support\Facades\App', 'App');
        class_alias('Illuminate\Support\Facades\Auth', 'Auth');
        class_alias('Illuminate\Support\Facades\Bus', 'Bus');
        class_alias('Illuminate\Support\Facades\DB', 'DB');
        class_alias('Illuminate\Support\Facades\Cache', 'Cache');
        class_alias('Illuminate\Support\Facades\Cookie', 'Cookie');
        class_alias('Illuminate\Support\Facades\Crypt', 'Crypt');
        class_alias('Illuminate\Support\Facades\Event', 'Event');
        class_alias('Illuminate\Support\Facades\Hash', 'Hash');
        class_alias('Illuminate\Support\Facades\Log', 'Log');
        class_alias('Illuminate\Support\Facades\Mail', 'Mail');
        class_alias('Illuminate\Support\Facades\Queue', 'Queue');
        class_alias('Illuminate\Support\Facades\Request', 'Request');
        class_alias('Illuminate\Support\Facades\Schema', 'Schema');
        class_alias('Illuminate\Support\Facades\Session', 'Session');
        class_alias('Illuminate\Support\Facades\Storage', 'Storage');
        class_alias('Illuminate\Support\Facades\Validator', 'Validator');
    }
}

/**
 * Register container bindings for the application.
 *
 * @return void
 */
protected function registerLogBindings()
{
    $this->singleton('Psr\Log\LoggerInterface', function () {
        return new Logger('lumen', [$this->getMonologHandler()]);
    });
}

/**
 * Get the Monolog handler for the application.
 *
 * @return \Monolog\Handler\AbstractHandler
 */
protected function getMonologHandler()
{
    return (new StreamHandler(storage_path('logs/lumen.log'), Logger::DEBUG))
                        ->setFormatter(new LineFormatter(null, null, true, true));
}

/**
 * Register the core container aliases.
 *
 * @return void
 */
protected function registerContainerAliases()
{
    $this->aliases = [
        'Illuminate\Contracts\Foundation\Application' => 'app',
        'Illuminate\Contracts\Auth\Guard' => 'auth.driver',
        'Illuminate\Contracts\Auth\PasswordBroker' => 'auth.password',
        'Illuminate\Contracts\Cache\Factory' => 'cache',
        'Illuminate\Contracts\Cache\Repository' => 'cache.store',
        'Illuminate\Contracts\Config\Repository' => 'config',
        'Illuminate\Container\Container' => 'app',
        'Illuminate\Contracts\Container\Container' => 'app',
        'Illuminate\Contracts\Cookie\Factory' => 'cookie',
        'Illuminate\Contracts\Cookie\QueueingFactory' => 'cookie',
        'Illuminate\Contracts\Encryption\Encrypter' => 'encrypter',
        'Illuminate\Contracts\Events\Dispatcher' => 'events',
        'Illuminate\Contracts\Filesystem\Factory' => 'filesystem',
        'Illuminate\Contracts\Hashing\Hasher' => 'hash',
        'log' => 'Psr\Log\LoggerInterface',
        'Illuminate\Contracts\Mail\Mailer' => 'mailer',
        'Illuminate\Contracts\Queue\Factory' => 'queue',
        'Illuminate\Contracts\Queue\Queue' => 'queue.connection',
        'Illuminate\Redis\Database' => 'redis',
        'Illuminate\Contracts\Redis\Database' => 'redis',
        'request' => 'Illuminate\Http\Request',
        'Illuminate\Session\SessionManager' => 'session',
        'Illuminate\Contracts\View\Factory' => 'view',
    ];
}

/**
 * The available container bindings and their respective load methods.
 *
 * @var array
 */
public $availableBindings = [
    'auth' => 'registerAuthBindings',
    'auth.driver' => 'registerAuthBindings',
    'Illuminate\Contracts\Auth\Guard' => 'registerAuthBindings',
    'auth.password' => 'registerAuthBindings',
    'Illuminate\Contracts\Auth\PasswordBroker' => 'registerAuthBindings',
    'Illuminate\Contracts\Broadcasting\Broadcaster' => 'registerBroadcastingBindings',
    'Illuminate\Contracts\Bus\Dispatcher' => 'registerBusBindings',
    'cache' => 'registerCacheBindings',
    'Illuminate\Contracts\Cache\Factory' => 'registerCacheBindings',
    'Illuminate\Contracts\Cache\Repository' => 'registerCacheBindings',
    'config' => 'registerConfigBindings',
    'composer' => 'registerComposerBindings',
    'cookie' => 'registerCookieBindings',
    'Illuminate\Contracts\Cookie\Factory' => 'registerCookieBindings',
    'Illuminate\Contracts\Cookie\QueueingFactory' => 'registerCookieBindings',
    'db' => 'registerDatabaseBindings',
    'Illuminate\Database\Eloquent\Factory' => 'registerDatabaseBindings',
    'encrypter' => 'registerEncrypterBindings',
    'Illuminate\Contracts\Encryption\Encrypter' => 'registerEncrypterBindings',
    'events' => 'registerEventBindings',
    'Illuminate\Contracts\Events\Dispatcher' => 'registerEventBindings',
    'Illuminate\Contracts\Debug\ExceptionHandler' => 'registerErrorBindings',
    'files' => 'registerFilesBindings',
    'filesystem' => 'registerFilesBindings',
    'Illuminate\Contracts\Filesystem\Factory' => 'registerFilesBindings',
    'hash' => 'registerHashBindings',
    'Illuminate\Contracts\Hashing\Hasher' => 'registerHashBindings',
    'log' => 'registerLogBindings',
    'Psr\Log\LoggerInterface' => 'registerLogBindings',
    'mailer' => 'registerMailBindings',
    'Illuminate\Contracts\Mail\Mailer' => 'registerMailBindings',
    'queue' => 'registerQueueBindings',
    'queue.connection' => 'registerQueueBindings',
    'Illuminate\Contracts\Queue\Factory' => 'registerQueueBindings',
    'Illuminate\Contracts\Queue\Queue' => 'registerQueueBindings',
    'redis' => 'registerRedisBindings',
    'request' => 'registerRequestBindings',
    'Illuminate\Http\Request' => 'registerRequestBindings',
    'session' => 'registerSessionBindings',
    'session.store' => 'registerSessionBindings',
    'Illuminate\Session\SessionManager' => 'registerSessionBindings',
    'translator' => 'registerTranslationBindings',
    'url' => 'registerUrlGeneratorBindings',
    'validator' => 'registerValidatorBindings',
    'view' => 'registerViewBindings',
    'Illuminate\Contracts\View\Factory' => 'registerViewBindings',
];
```
1. 通过看源码，我们发现有这么一个变量 `$availableBindings`, log最终是 `Monolog\Logger`类。如果看官不明白，可以参照上文中的`Facade`的原理链接，了解一下lumen的机制。

## 集成自己的Log类

上文我们明白了Log既然是加载了 `Monolog\Logger` 类，那我们可以集成一个自己的Log类，最终代码如下
``` php
<?php
/*
 * To change this license header, choose License Headers in Project Properties.
 * To change this template file, choose Tools | Templates
 * and open the template in the editor.
 * default log directory is storage/api/
 * default log name format is YYYY-mm-dd.log
 */

namespace App\Sphenginx;

use Monolog\Logger;
use Monolog\Handler\StreamHandler;
use Monolog\Formatter\LineFormatter;

/**
 * user defined Log class, with StreamHandler and LineFormatter 
 *
 * @author Sphenginx
 */
class Log 
{

    //define static log instance.
    protected static $_log_instance;

    /**
     * 获取log实例
     *
     * @return obj
     * @author Sphenginx
     **/
    public static function getLogInstance()
    {
        if (static::$_log_instance === null) {
            static::$_log_instance = new Logger('Sphenginx');
        }
        return static::$_log_instance;
    }

    /**
     * Handle dynamic, static calls to the object.
     *
     * @param  string  $method 可用方法: debug|info|notice|warning|error|critical|alert|emergency 可调用的方法详见 Monolog\Logger 类
     * @param  array   $args 调用参数
     * @return mixed
     * @author Sphenginx
     */
    public static function __callStatic($method, $args)
    {
        $instance = static::getLogInstance();

        //组织参数信息
        $message = $args[0];
        $context = isset($args[1]) ? $args[1] : [];
        $path    = isset($args[2]) ? $args[2] : 'api/';

        //设置日志处理手柄，默认为写入文件（还有mail、console、db、redis等方式，详见Monolog\handler 目录）
        $handler = new StreamHandler(storage_path($path) . date('Y-m-d').'.log');

        //设置输出格式LineFormatter(Monolog\Formatter\LineFormatter)， ignore context and extra
        $handler->setFormatter(new LineFormatter(null, null, true, true));

        $instance->pushHandler($handler);
        $instance->$method($message, $context);
    }

}

```
说明下，`__callStatic`  方法是php5.3+以来 非常方便的一个魔术方法。

## More Info
- [Lumen中文文档](http://laravelacademy.org/docs/lumen)
- [IoC/DIP其实是一种管理思想](http://coolshell.cn/articles/9949.html)
