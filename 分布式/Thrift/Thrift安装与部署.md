### Thrift安装与部署

#### 1. Thrift安装

```sh
brew install thrift
```

通过在命令行调用 **thrift -version**确定安装成功

#### 2. Thrift部署

```sh
thrift -r --gen php tutorial.thrift
```

#### 3. 编写IDL文件

```protobuf
//example.thrift
namespace php example

struct Data {
    1:string text
}


service format_data {
    Data do_format(1:Data data)
}
```

#### 4. go server的实现

1.在根目录里创建example.thrift 文件
2.执行命令

```css
 thrift -r --gen go example.thrift
```

3.根目录会多出一个gen-go 的目录，里面是thrift的实例化代码，都是自动生成的。

4.引入thrift-go 的库

```sh
go get git.apache.org/thrift.git/lib/go/thrift

```

完整代码

```go
package main

import (
    "context"
    "fmt"
    "github.com/apache/thrift/lib/go/thrift"
    "log"
    "strings"
    "go-thrift-example/gen-go/example"
)

type FormatDataImpl struct {}

func (fdi *FormatDataImpl) DoFormat(ctx context.Context,data *example.Data) (r *example.Data, err error) {
    var rData example.Data
    rData.Text = strings.ToUpper(data.Text)

    fmt.Println(data, rData)

    return &rData, nil
}

const (
    HOST = "localhost"
    PORT = "8080"
)

func main()  {
    handler := &FormatDataImpl{}
    processor := example.NewFormatDataProcessor(handler)
    serverTransport, err := thrift.NewTServerSocket(HOST + ":" + PORT)
    if err != nil {
        log.Fatalln("Error:", err)
    }

    transportFactory := thrift.NewTFramedTransportFactory(thrift.NewTTransportFactory())
    protocolFactory := thrift.NewTBinaryProtocolFactoryDefault()

    server := thrift.NewTSimpleServer4(processor, serverTransport, transportFactory, protocolFactory)
    fmt.Println("Running at:", HOST + ":" + PORT)
    server.Serve()

}
```

#### 5. php client的实现 (基于php7)

1. 准备工作

 thrift的php官方代码仓库在[github](https://github.com/apache/thrift/tree/master/lib/php),我们在根目录创建lib,然后在lib下创建Thrift这个目录，然后把链接点开，把lib下面的所有代码都拷贝到Thrift这个目录下面。

![img](../assets/12890383-976c0fea0b014541.png)

2. 接下来就是生成我们的php代码，依然用的还是那个thrift文件
   比较简单的生成方式是

```undefined
thrift -r --gen php thrift/example.thrift
```

这样生成的方式会将所有类都糅合进一个文件，所以我们也可以这样

```undefined
thrift -r --gen php:psr4,validate,json thrift/example.thrift
```

- **psr4** 代码代码生成会遵循psr4标准，每个类一个文件
- **validate** 会生成校验代码，对参数进行校验
- **json** 生成的代码会实例化 **JsonSerializable**接口

当然这样生成的代码是client端的，代码量其实不是那么多，如果要生成server端代码，还需要加上**server** 参数

```sh
thrift --gen php:server,psr4,validate,json
```

3. 接下来是我们如何引入thrift-php 的类，我们因为是直接拷贝的代码，所以需要手动引入，

```php
require_once __DIR__.'/lib/Thrift/ClassLoader/ThriftClassLoader.php';

use Thrift\ClassLoader\ThriftClassLoader;

$GEN_DIR = realpath(dirname(__FILE__)).'/gen-php';
$loader = new ThriftClassLoader();
$loader->registerNamespace('Thrift',__DIR__.'/lib');
$loader->registerNamespace('example',$GEN_DIR);
$loader->register();
```

需要注意的一点，如果没有加上psr4,我们生成的代码都在一个类，需要引入我们的 **example**的命名空间需要换成

```php
$loader->registerDefinition('example',$GEN_DIR);
```

4. 接下来是我们的调用

```php
    $socket = new TSocket('localhost',8080);
    $transport = new TFramedTransport($socket);
    $protocol = new TBinaryProtocol($transport);

    $client = new format_dataClient($protocol);

    $transport->open();

    $data = new Data();
    $data->text = 'World!';

    $res = $client->do_format($data);
    var_dump($res);

    $transport->close();
```

5. 我们完整的client.php 代码如下

```php
<?php


error_reporting(E_ALL);
require_once __DIR__.'/lib/Thrift/ClassLoader/ThriftClassLoader.php';

use Thrift\ClassLoader\ThriftClassLoader;

$GEN_DIR = realpath(dirname(__FILE__)).'/gen-php';
$loader = new ThriftClassLoader();
$loader->registerNamespace('Thrift',__DIR__.'/lib');
$loader->registerNamespace('example',$GEN_DIR);
$loader->register();

use Thrift\Protocol\TBinaryProtocol;
use Thrift\Transport\TFramedTransport;
use Thrift\Transport\TSocket;
use example\Data;
use example\format_dataClient;

try {
    $socket = new TSocket('localhost',8080);
    $transport = new TFramedTransport($socket);
    $protocol = new TBinaryProtocol($transport);

    $client = new format_dataClient($protocol);

    $transport->open();

    $data = new Data();
    $data->text = 'World!';

    $res = $client->do_format($data);
    var_dump($res);

    $transport->close();
} catch (\Exception $e) {
    print 'TException:'.$e->getMessage().PHP_EOL;
}
```

6. 接下来执行我们的client,结果就如下

```csharp
13:02:54 › php Client.php
object(example\Data)#9 (1) {
  ["text"]=>
  string(6) "WORLD!"
}
```

