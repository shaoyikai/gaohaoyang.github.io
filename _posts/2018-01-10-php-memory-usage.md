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
// PHP的普通数组
$start1 = memory_get_usage();
$array = range(1,100000);
$end1 = memory_get_usage();
echo ($end1 - $start1) . " bytes";

echo '<br>';

// PHP的定长数组
$start2 = memory_get_usage();
$fixedArray = new SplFixedArray(100000);
for($i=0; $i<100000; $i++){
	$fixedArray[$i] = $i;
}
$end2 = memory_get_usage();
echo ($end2 - $start2) . " bytes";

```

输出：
> 4194368 bytes
> 1601608 bytes

从这个示例中页可以发现 SplFixedArray 比 array 节省了大量的内存，因而如果长度确定，优先使用 SplFixedArray。