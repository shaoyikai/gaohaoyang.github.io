## 依赖注入

```php

<?php

class DI
{
    protected $services = [];

    public function getServices()
    {
        return array_keys($this->services);
    }

    public function register($serviceName, callable $callable)
    {
        $this->services[$serviceName] = $callable;
    }

    public function getService($serviceName, $args = [])
    {
        if (!array_key_exists($serviceName, $this->services)) {
            throw new \Exception('The service:' . $serviceName . 'does not exists!');
        }

        if(!empty($args)){
            return $this->services[$serviceName]($args);
        }
        return $this->services[$serviceName]();
    }

    function __get($serviceName)
    {
        return $this->getService($serviceName);
    }

    function __set($serviceName, Callable $callable)
    {
        $this->register($serviceName, $callable);
    }
}

$config = [
    'db' => [
        'dsn'=> 'mysql@localhost:test',
        'username'=> 'root',
        'password'=> '123456((98',
    ],
    'aws' => [
        'key' => '123',
        'primary_key' => 'abc',
    ]
];
$di = new DI();

$di->register('aws', function ($arg) use ($config) {
    $obj = new \stdClass();
    $obj->name = 'aws';
    $obj->key = $config['aws']['key'];
    $obj->primary_key = $config['aws']['primary_key'];

    // if has args
    $obj->arg1 = $arg[0];
    $obj->arg2 = $arg[1];
    return $obj;
});
$di->register('database', function () use ($config){
    $obj = new \stdClass();
    $obj->name = 'database';
    $obj->username = $config['db']['username'];
    $obj->password = $config['db']['password'];
    return $obj;
});

$aws = $di->getService('aws',['HI','MOM']);
$db = $di->getService('database');

$di->email = function () {
    $obj = new \stdClass();
    $obj->name = 'email';
    return $obj;
};

echo PHP_EOL;
echo $aws->primary_key;

echo PHP_EOL;
echo $db->password;

echo PHP_EOL;
echo $di->email->name;

echo PHP_EOL;
print_r($di->getServices());
print_r($aws);

```