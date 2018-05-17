---
layout: post
title: "PHP的session被变量覆盖bug修复记"
date:   2018-02-01
categories: php
tags: php
excerpt: 一个奇怪的bug
---


```php
<?php 

$_SESSION['abc'] = 123;
print_r($_SESSION['abc']); // 123
$abc = $_SESSION['abc'] == 123 ? "yes" : "no";
print_r($_SESSION['abc']); // yes ?

```

按照常规的理解，第二次输出的`$_SESSION['abc']`应该仍然为`123`才对，可是结果是`yes`？好奇怪！为什么会这样呢？

经过各种尝试，最后发现是php.ini中的一个参数设置引起的。


```ini
register_globals ON
```

这个配置项设置为`OFF`即可解决上述bug。

[参考文档](http://php.net/manual/zh/security.globals.php)

