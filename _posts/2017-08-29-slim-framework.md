---
layout: post
title:  "优秀的php小框架slim"
date:   2017-08-23
categories: php
tags: Slim
excerpt: php框架slim
---

#### 简介

slim是一个很特别的小框架，之所以说小是因为代码量很少，功能没有像symfony，yii2之流那么多。它仅仅保留了最基本的功能，没有设计成万能的“瑞士军刀”，但是却架构良好，极其灵活简洁，容易扩展。

slim将互联网服务抽象为request和response两个过程，再配合灵活的router机制对程序进行控制。它有DI容器的概念，可以将一些功能组件（middleware）“注入”到app之中，所以扩展起来非常方便。

slim使用composer作为包管理，这意味着你可以随心所欲的使用packages上面的无数优秀第三方代码。


#### 安装


```bash
composer require slim/slim "^3.0"
```

引入autoload文件

```php
<?php
use \Psr\Http\Message\ServerRequestInterface as Request;
use \Psr\Http\Message\ResponseInterface as Response;

require 'vendor/autoload.php';

$app = new \Slim\App();

$app->get('/hello/{name}', function (Request $request, Response $response) {
    $name = $request->getAttribute('name');
    $response->getBody()->write("Hello, $name");

    return $response;
});

$app->run();
```
#### GET或者POST获取变量

```php
// Getting _GET and _POST 变量
$app->get('/', function (Request $req,  Response $res, $args = []) {
    $myvar1 = $req->getParam('myvar'); //checks both _GET and _POST [NOT PSR-7 Compliant]
    $myvar2 = $req->getParsedBody()['myvar']; //checks _POST  [IS PSR-7 compliant]
    $myvar3 = $req->getQueryParams()['myvar']; //checks _GET [IS PSR-7 compliant]
});



```

#### apache配置.htaccess文件

```
RewriteEngine On
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^ index.php [QSA,L]
```

#### 更多

示例代码：[slim-example](https://github.com/shaoyikai/slim-example)

官方文档：[www.slimframework.com/docs/](https://www.slimframework.com/docs/)
