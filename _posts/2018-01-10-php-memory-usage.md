---
layout: post
title:  "PHP内存使用量计算方法"
date:   2018-01-10
categories: php
tags: php
excerpt: 统计php使用的内存
---


测试代码：

```php
<?php
$startMemory = memory_get_usage();
$array = range(1,100000);
$endMemory = memory_get_usage();
echo ($endMemory - $startMemory) . " bytes";

```

输出：
> 4194368 bytes