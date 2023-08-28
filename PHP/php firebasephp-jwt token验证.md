# php firebase/php-jwt token验证

## 一：JWT介绍：

全称JSON Web Token，基于JSON的开放标准((RFC 7519) ，以token的方式代替传统的Cookie-Session模式，用于各服务器、客户端传递信息签名验证。

## 二：JWT优点：

1. 服务端不需要保存传统会话信息，没有跨域传输问题，减小服务器开销。

2. jwt构成简单，占用很少的字节，便于传输。

3. json格式通用，不同语言之间都可以使用。

## 三：JWT组成

1：jwt由三部分组成：

     头部（header)
     载荷（payload) 包含一些定义信息和自定义信息
     签证（signature)

2：具体构成：

**header：**

```json
{
  "typ": "JWT", //声明类型为jwt
  "alg": "HS256" //声明签名算法为SHA256
}
```

**载荷（payload)**

```json
{
  "iss": "http://www.helloweba.net",
  "aud": "http://www.helloweba.net",
  "iat": 1525317601,
  "nbf": 1525318201,
  "exp": 1525318201,
  "data": {
    "userid": 1,
    "username": "李小龙"
  }
}
```

载荷包括两部分：标准声明和其他声明。

**标准声明：JWT标准规定的声明，但不是必须填写的；**

**标准声明字段：**

接收该JWT的一方

> iss: jwt签发者
>
> sub: jwt所面向的用户
>
> aud: 接收jwt的一方
>
> exp: jwt的过期时间，过期时间必须要大于签发时间
>
> nbf: 定义在什么时间之前，某个时间点后才能访问
>
> iat: jwt的签发时间
>
> jti: jwt的唯一身份标识，主要用来作为一次性token。
>

**其他声明：自己定义的字段，因为这部分是可以解开的，建议不要加入敏感信息，这里的data就是我自己定义的声明**

最终是这样的：

三部分分别以逗号隔开

## 四：使用：

PHP有很多jwt包，包括比如：lcobucci/jwt，我这里使用firebase。可以从 git地址 下载

1：把代码拉取下来

`composer require firebase/php-jwt`

2：服务端签发Token

```php
<?php
use \Firebase\JWT\JWT; //导入JWT
class MainController extends Controller
{
    //签发Token
    public function lssue()
    {
      $key = '344'; //key
      $time = time(); //当前时间
          $token = [
          'iss' => 'http://www.helloweba.net', //签发者 可选
            'aud' => 'http://www.helloweba.net', //接收该JWT的一方，可选
            'iat' => $time, //签发时间
            'nbf' => $time , //(Not Before)：某个时间点后才能访问，比如设置time+30，表示当前时间30秒后才能使用
            'exp' => $time+7200, //过期时间,这里设置2个小时
              'data' => [ //自定义信息，不要定义敏感信息
                'userid' => 1,
                  'username' => '李小龙'
            ]
        ];
        echo JWT::encode($token, $key); //输出Token
    }
}
```
3：解析Token，为了演示，这里直接写。

```php
<?php
use \Firebase\JWT\JWT; //导入JWT
class MainController extends Controller
{
    public function verification()
    {
      $key = '344'; //key要和签发的时候一样

      $jwt = "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwOlwvXC93d3cuaGVsbG93ZWJhLm5ldCIsImF1ZCI6Imh0dHA6XC9cL3d3dy5oZWxsb3dlYmEubmV0IiwiaWF0IjoxNTI1MzQwMzE3LCJuYmYiOjE1MjUzNDAzMTcsImV4cCI6MTUyNTM0NzUxNywiZGF0YSI6eyJ1c2VyaWQiOjEsInVzZXJuYW1lIjoiXHU2NzRlXHU1YzBmXHU5Zjk5In19.Ukd7trwYMoQmahOAtvNynSA511mseA2ihejoZs7dxt0"; //签发的Token
      try {
            JWT::$leeway = 60;//当前时间减去60，把时间留点余地
            $decoded = JWT::decode($jwt, $key, ['HS256']); //HS256方式，这里要和签发的时候对应
            $arr = (array)$decoded;
            print_r($arr);
        } catch(\Firebase\JWT\SignatureInvalidException $e) {  //签名不正确
            echo $e->getMessage();
        }catch(\Firebase\JWT\BeforeValidException $e) {  // 签名在某个时间点之后才能用
            echo $e->getMessage();
        }catch(\Firebase\JWT\ExpiredException $e) {  // token过期
            echo $e->getMessage();
        }catch(Exception $e) {  //其他错误
            echo $e->getMessage();
        }
        //Firebase定义了多个 throw new，我们可以捕获多个catch来定义问题，catch加入自己的业务，比如token过期可以用当前Token刷新一个新Token
    }
}
```

 在src目录下的JWT.php中的方法 decode中，可以看到作者是通过多个 自定义 throw new 抛出异常的

所以我们可以根据不同的throw new设置多个catch来捕获。

输出的数据：

> Array
> (
>     [iss] => http://www.helloweba.net
>     [aud] => http://www.helloweba.net
>     [iat] => 1525340317
>     [nbf] => 1525340317
>     [exp] => 1525347517
>     [data] => stdClass Object
>         (
>             [userid] => 1
>             [username] => 李小龙
>         )
> )

## 五：模拟实战

1：方案：客户端通过用户名密码登录以后，服务端返回给客户端两个token：access_token和refresh_token。

access_token：请求接口的token

refresh_token：刷新access_token

举个例子：比如access_token设置2个小时过期，refresh_token设置7天过期，2小时候后，access_token过期，但是refresh_token还在7天以内，那么客户端通过refresh_token来服务端刷新，服务端重新生成一个access_token；如果refresh_token也超过了7天，那么客户端需要重新登录获取access_token和refresh_token。

为了区分两个token，我们在载荷（payload)加一个字段 scopes ：作用域。

access_token中设置：scopes:role_access

refresh_token中设置：scopes:role_refresh

有些人是用一个token，要么设置很长时间过期；要么设置几个小时过期，如果过期，用过期的token去换取新的token，这种其实是有问题的，有些人说token有两个时间，一个是过期时间，一个是刷新时间，只要在刷新时间内就可以换取新token。假如别人拿到了你这token，如果也在过期时间之内，不是同样可以刷新token？除非两个token都拿到，相对来说第二种更安全。

直接上代码：

```php
<?php
use \Firebase\JWT\JWT; //导入JWT
class MainController extends Controller
{
    public function authorizations()
    {
      $key = 'ffdsfsd@4_45'; //key
      $time = time(); //当前时间

      //公用信息
      $token = [
          'iss' => 'http://www.helloweba.net', //签发者 可选
            'iat' => $time, //签发时间
            'data' => [ //自定义信息，不要定义敏感信息
              'userid' => 1,
            ]
        ];

      $access_token = $token;
      $access_token['scopes'] = 'role_access'; //token标识，请求接口的token
      $access_token['exp'] = $time+7200; //access_token过期时间,这里设置2个小时

      $refresh_token = $token;
      $refresh_token['scopes'] = 'role_refresh'; //token标识，刷新access_token
      $refresh_token['exp'] = $time+(86400 * 30); //access_token过期时间,这里设置30天

      $jsonList = [
        'access_token'=>JWT::encode($access_token,$key),
        'refresh_token'=>JWT::encode($refresh_token,$key),
        'token_type'=>'bearer' //token_type：表示令牌类型，该值大小写不敏感，这里用bearer
      ];
                Header("HTTP/1.1 201 Created");
      echo json_encode($jsonList); //返回给客户端token信息
    }
}
```

 看起来输出是这样的：

```json
{
	"access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwOlwvXC93d3cuaGVsbG93ZWJhLm5ldCIsImlhdCI6MTUyNTQxNzg5NywiZGF0YSI6eyJ1c2VyaWQiOjF9LCJzY29wZXMiOiJyb2xlX2FjY2VzcyIsImV4cCI6MTUyNTQyNTA5N30.Nxp1yutwt8Fxj5XEzes4j-X4tCBQwE0htEV3Msm2D8s",
	"refresh_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwOlwvXC93d3cuaGVsbG93ZWJhLm5ldCIsImlhdCI6MTUyNTQxNzg5NywiZGF0YSI6eyJ1c2VyaWQiOjF9LCJzY29wZXMiOiJyb2xlX3JlZnJlc2giLCJleHAiOjE1MjgwMDk4OTd9.YY8Lid3nk3bnV-ZnAYneKJxGiaD73waqTpC3bHz3wsY",
	"token_type": "bearer"
}
```

http头部响应是这样的：两个token客户端保存，一个用来取接口，一个用来刷新接口。

客户端请求的时候在http头部携带 Authorization: bearer token，注意bearer后面有个空格，我们用PHP模拟一下。

请求信息是这样的，PHP获取Authorization信息判断。

这个只是一种思路，大家可以按照自己的思路去弄，这种思路服务端不用保存token，但如果涉及到token的注销就必须保存，比如token没过期，但是要注销token，这时候可以上redis。

最后请务必使用HTTPS
