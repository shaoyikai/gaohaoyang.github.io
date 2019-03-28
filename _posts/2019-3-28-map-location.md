---
layout: post
title: "通过地址获取其经纬度"
date:   2019-03-28
categories: php
tags: php
excerpt: 有时候需要根据地理信息，获取到其相应的经纬度信息，本文介绍一下通过高德地图api实现方法
---


### 注册高德开放平台账号

1. 新建app
2. 生成key，比如:d43b55fef15399d469c25c82079be3ee

### 示例代码

```php

	$address = '方恒国际中心A座'
	$city = '北京'
	$key = 'd43b55fef15399d469c25c82079be3ee'; // 高德淘宝号shaoyikai登录创建
	$url = 'http://restapi.amap.com/v3/geocode/geo?key=' . $key . '&address=' . $address. '&city=' . $city;
	$AmapData = file_get_contents($url);

	// $AmapData 为字符串： 
	// {"status":"1","info":"OK","infocode":"10000","count":"1","geocodes":[{"formatted_address":"北京市朝阳区方恒国际中心|A座","country":"中国","province":"北京市","citycode":"010","city":"北京市","district":"朝阳区","township":[],"neighborhood":{"name":[],"type":[]},"building":{"name":[],"type":[]},"adcode":"110105","street":[],"number":[],"location":"116.480656,39.989677","level":"门牌号"}]}

	$res = json_decode($AmapData);
	if($res->info === 'OK') {
	    $geocodes = $res->geocodes;

	    $location_2 = $geocodes[0]->location;
	    list($lon, $lat) = explode(',', $location_2);
	}

```

