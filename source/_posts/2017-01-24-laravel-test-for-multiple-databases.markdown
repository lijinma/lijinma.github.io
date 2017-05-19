---
layout: post
title: "【phpunit】Laravel 测试的时候，如果有多个数据库怎么办？"
date: 2017-01-24 17:01:51 +0800
comments: true
categories: 
---

我们系统重构的时候，数据库是有多个的，所以在 database.php 里面定义了两个 connections

```php
'mysql' => [
    'driver'    => 'mysql',
    'host'      => env('MYSQL_HOSTNAME', 'localhost'),
    'port'      => env('MYSQL_PORT', 3306),
    'database'  => env('MYSQL_DATABASE', 'forge'),
    'username'  => env('MYSQL_USER', 'forge'),
    'password'  => env('MYSQL_PASSWORD', ''),
    'charset'   => env('MYSQL_CHARSET', 'utf8mb4'),
    'collation' => env('MYSQL_COLLATION', 'utf8mb4_unicode_ci'),
    'prefix'    => env('MYSQL_PREFIX', ''),
    'timezone'  => env('MYSQL_TIMEZONE', '+08:00'),
    'strict'    => env('MYSQL_STRICT_MODE', true),
],
'new_mysql' => [
    'driver'    => 'mysql',
    'host'      => env('NEW_NEW_MYSQL_HOSTNAME', 'localhost'),
    'port'      => env('NEW_MYSQL_PORT', 3306),
    'database'  => env('NEW_MYSQL_DATABASE', 'forge'),
    'username'  => env('NEW_MYSQL_USER', 'forge'),
    'password'  => env('NEW_MYSQL_PASSWORD', ''),
    'charset'   => env('NEW_MYSQL_CHARSET', 'utf8mb4'),
    'collation' => env('NEW_MYSQL_COLLATION', 'utf8mb4_unicode_ci'),
    'prefix'    => env('NEW_MYSQL_PREFIX', ''),
    'timezone'  => env('NEW_MYSQL_TIMEZONE', '+08:00'),
    'strict'    => env('NEW_MYSQL_STRICT_MODE', true),
]
```

很自然的，在 Model （表在 new_mysql 数据库）里面我们会覆盖 connection
```php
protected $connection = 'new_mysql';
```
这个时候测试出问题了，因为我的测试配置是：
database.php
```php
'testing' => [
    'driver' => 'sqlite',
    'database' => ':memory:',
],
```
phpunit.xml
```xml
<env name="DB_CONNECTION" value="testing" />
```
因为 Model 里面 connection 写死了，所以在测试的时候会尝试去连 `new_mysql`
正确的做法应该是在 Model 里面通过覆盖 getConnectionName 来处理 testing 的数据库问题。
```php
public function getConnectionName()
{
    return app()->environment('testing') ? config('database.default') : 'new_mysql';
}
```
