批量查找并保存数据库中相关地址的经纬度信息

脚本的应用场景：数据库中已经有地址信息，但是没有相关的经纬度信息，需求有变化，需要用到经纬度信息。
以下是便捷的方法，大致流程以及示例代码：

a.pdo连接mysql查出要操作的数据
b.使用curl调用百度api获取相应地址的经纬度
c.执行sql更新地址的经纬度信息

```php
<?php
/**
 * @author shaoyikai@qq.com
 * 批量获取相关地址的经纬度
 */
$dsn = 'mysql:dbname=tmscent;host=172.23.140.165';
$user = 'test';
$password = '123456';

try {
    $pdo = new PDO($dsn, $user, $password);
} catch (PDOException $e) {
    echo 'Connection failed: ' . $e->getMessage();
}

$sql = 'SELECT `theatreID`, `name`, `address`, `addr_lng`, `addr_lat` FROM `theatre_info` ORDER BY `name`';

foreach ($pdo->query($sql) as $row) {

    $address = $row['name'];
    $city = $row['address'];
    
    $ch = curl_init();
    curl_setopt($ch, CURLOPT_URL, "http://api.map.baidu.com/geocoder?address=$address&output=json&key=6eea93095ae93db2c77be9ac910ff311&city=$city");
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
    curl_setopt($ch, CURLOPT_HEADER, 0);
    $output = curl_exec($ch);
    curl_close($ch);
    
    $output = json_decode($output,true); // 将获取到的结果decode为array数据

    $lng = isset($output['result']['location']) ? $output['result']['location']['lng'] : 0; // 经度
    $lat = isset($output['result']['location']) ? $output['result']['location']['lat'] : 0; // 维度

    $query="update theatre_info set addr_lng = $lng , addr_lat = $lat WHERE theatreID = ".$row['theatreID'];
    $res=$pdo->exec($query);
}

```
截图效果
![](img)