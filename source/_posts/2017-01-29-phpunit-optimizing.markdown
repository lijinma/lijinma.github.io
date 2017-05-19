---
layout: post
title: "【phpunit】这样跑测试，竟然节省了我们 90% 的时间"
date: 2017-01-29 17:01:51 +0800
comments: true
categories: 
---

关于 phpunit 我会写一个系列，把我们项目中使用 phpunit 遇到的每一个问题分享给大家。

项目背景：
----
我们的微服务使用 lumen 搭建，所以这里的测试都是指的是 api 的测试，而且我们没有写任何的单元测试，直接写的是系统测试，我举一个例子你就明白了。

```php
    /**
     * @test
     */
    public function itShouldReturnXXXList()
    {
        factory(XXX::class, 3)->create();
        $this->be(new User());
        $headers = [
            //headers
        ];
        $this->get('/api/xxxs', $headers);
        $this->assertResponseOk();
        $this->seeJsonStructure([self::XXX_STRUCTURE]);
    }
```
以上的例子是测试在登陆的情况下，获取 xxx 的列表的接口的返回数据情况。

测试背景
----
一般在测试的时候，因为每个测试数据库都要隔离，一般的解决方案有两种：
1. 第一种是使用 `transaction`，每次在一个测试开始的时候`transaction begin`，在断言结束后`transaction rollback`，这样一个数据库中实际没有写入任何数据，所以每次测试互不影响，`trait` 如下：
```php
trait DatabaseTransactions
{
    /**
     * Begin a database transaction.
     *
     * @return void
     */
    public function beginDatabaseTransaction()
    {
        $this->app->make('db')->beginTransaction();

        $this->beforeApplicationDestroyed(function () {
            $this->app->make('db')->rollBack();
        });
    }
}
```

2. 第二种是使用`migrate`，每次在一个测试开始的时候`migrate`，在断言结束后`migrate:rollback`，这样数据库每次测试都建表，写数据，清空数据和表格，所以每次测试也互不影响。
```php
trait DatabaseMigrations
{
    /**
     * Run the database migrations for the application.
     *
     * @return void
     */
    public function runDatabaseMigrations()
    {
        $this->artisan('migrate');

        $this->beforeApplicationDestroyed(function () {
            $this->artisan('migrate:rollback');
        });
    }
}
```
我们的测试使用的是 `sqlite :memory:`，配置如下：
```
        'testing' => [
            'driver' => 'sqlite',
            'database' => ':memory:',
        ],
```
很遗憾，`sqlite :memory:` 不支持 `transaction`，只能使用 `migrate`的方式。

问题：
----
随着测试越来越多，问题来了，我们一个有一个核心项目的系统测试已经有 97 个了，导致的问题是每次本地测试全部跑一次需要 8 分钟左右（每个人电脑不同，有些许差别），GitLab 上 ci 需要跑两次（一次自定义分支提交会跑，一次合并到 master 后会跑），结果就是一个 commit 从提交到审核代码到最终合并上线，需要十几分钟，真是的是心好痛。

最大的问题是，很多人一起开发，这个时间是会浪费在每一个人的头上，所以我们一直想办法尝试解决，但是并没有很好的解决方案。

尝试解决方案：
----
### 尝试解决方案1（失败）
因为我们使用的是 `migrate` 的方式，我们猜想可能使用 `transaction` 的方式可能会更快一点，而且也确实看到别人使用 `mysql transaction` 的方式测试速度加快了很多，就试用了一下，但是结果并不理想，甚至更慢。

放弃。

### 尝试解决方案2（失败）
某天我看到 `ruby` 里面有比较成熟的并行测试方案，比如 [parallel_tests](https://github.com/grosser/parallel_tests) ，觉得这是一个很好的思路，在测试使用多进程的方式来跑，就可以节省大量的时间，想想 php 不可能没有类似的工具 :)，于是我找到了一个 [Paratest](https://github.com/brianium/paratest)，你可以通过 https://code.tutsplus.com/tutorials/parallel-testing-for-phpunit-with-paratest--net-32105 对 `Paratest` 有一定了解。

但是经过试用后，发现出现在不同 php 版本下，`Paratest` 很不稳定的，偶尔还抽风，决定放弃试用。

期间还遇到了别的类似的工具，但在兼容性上都做的很差，略失望。

偶然发现：
----
偶然我观察到，在使用 `phpunit` 来跑全部测试的时候，比较慢的测试都在后面，这很奇怪，如果我使用 `phpunit --filter XxxTest` 的时候，事实上并没有那么慢，这是为什么？

既然单个文件跑的时候很快，那我试试一个文件一个文件来跑。如下：

```bash
for i in $(ls -R ./tests |grep php); do ./vendor/bin/phpunit --configuration phpunit.xml --filter $(echo $i|sed -e "s/.php//g"); done
```

以上代码的意思是从 tests 文件夹中找出所有 php 文件，最后单个使用 `filter` 的方式来跑测试，比如有一个文件是 `ExampleTest.php`，最终执行的是 `./vendor/bin/phpunit --configuration phpunit.xml --filter ExampleTest`

结果跑下来太震惊了，时间从 8 分 08 秒减到了 47 秒（在我机器），节省超过了 90% 的时间，一个字：”吓死人“。

尝试解释原理：
----
难道是内存的原因吗？如果是内存的原因我在跑 phpunit 的时候，把 memory 调整到 limited，看看效果，
```bash
php -d memory_limit=-1 vendor/bin/phpunit --configuration phpunit.xml
```
事实上，内存确实调整到了无限，但是仍然没有解决问题。

那到底是为什么呢？为什么把测试拆到多个文件，一个一个文件来跑比全部一起跑会快这么多？

而且，每个文件保持测试在 10 个以下，效果更佳。

直到现在，我还不可以更好的解释原因，如果你有线索，我们可以聊聊。

最终解决方案：
----
因为我们的 `GitLab ci` 里面会跑 phpunit，下面我分享下  `.gitlab-ci.yml` 配置

```yml
  script:
    - composer install --quiet
    - ./phpunit.sh
```
phpunit.sh，此脚本是我们架构师  [@Sin30](https://laravel-china.org/users/2210) 写的
```bash
#!/bin/sh

set -eo pipefail

for i in $(find tests -type f -name "*Test.php" | xargs -I {} basename {} .php)
do
    vendor/bin/phpunit --configuration phpunit.xml --filter $i
done
```

里面的 `set -eo pipfail` 解释以下：
>`set -e` 表示一旦脚本中命令返回值不是 0，脚本立即退出；

>`set -o pipefail` 表示在 `pipe |` 中，只要任何一个命令返回值不是 0（假设是 -1），整个 pipe 返回 -1，即使最后一个命令返回 0。

这样可以保证只要有一个 Test 出错，后面的就不用再跑了，节省时间。

总结
----
能节省程序员时间的事情是最重要的事情，怎么强调都不过分。



