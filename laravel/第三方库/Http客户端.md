###  参考地址

* [laravel8.5文档](https://learnku.com/docs/laravel/8.5/http-client/10390#introduction)
* [后盾人](https://houdunren.gitee.io/note/%E6%89%8B%E5%86%8C/laravel%208.x/6%20%E7%BB%BC%E5%90%88%E7%9F%A5%E8%AF%86/10%20HTTP%E5%AE%A2%E6%88%B7%E7%AB%AF.html)

#### 基本使用



> 控制器中使用

```php
        use GuzzleHttp\Client;

        $client = new Client(['verify' => false]);
        $res = $client->request('GET', 'https://www.zhipin.com/wapi/zpCommon/data/oldindustry.json');
        $data=json_decode($res->getBody(),true);
        return response()->json($data);
```

# HTTP客户端

## [#](https://houdunren.gitee.io/note/手册/laravel 8.x/6 综合知识/10 HTTP客户端.html#介绍)介绍

Laravel在[Guzzle HTTP客户端 (opens new window)](http://docs.guzzlephp.org/en/stable/)提供了一种表达性极强的API ，使您可以快速发出传出的HTTP请求以与其他Web应用程序进行通信。Laravel围绕Guzzle的包装专注于其最常见的用例和出色的开发人员体验。

在开始之前，您应确保已将Guzzle软件包安装为应用程序的依赖项。默认情况下，Laravel自动包含此依赖项：

```php
composer require guzzlehttp/guzzle
```

## [#](https://houdunren.gitee.io/note/手册/laravel 8.x/6 综合知识/10 HTTP客户端.html#发出请求)发出请求

为了使请求，您可以使用`get`，`post`，`put`，`patch`，和`delete`方法。首先，让我们研究一下如何发出基本`GET`请求：

```php
use Illuminate\Support\Facades\Http;

$response = Http::get('http://test.com');
```

该`get`方法返回的实例`Illuminate\Http\Client\Response`，该实例提供了可用于检查响应的多种方法：

```php
$response->body() : string;
$response->json() : array|mixed;
$response->status() : int;
$response->ok() : bool;
$response->successful() : bool;
$response->failed() : bool;
$response->serverError() : bool;
$response->clientError() : bool;
$response->header($header) : string;
$response->headers() : array;
```

该`Illuminate\Http\Client\Response`对象还实现PHP`ArrayAccess`接口，使您可以直接在响应上访问JSON响应数据：

```php
return Http::get('http://test.com/users/1')['name'];
```

### [#](https://houdunren.gitee.io/note/手册/laravel 8.x/6 综合知识/10 HTTP客户端.html#请求数据)请求数据

当然，使用时很常见`POST`，`PUT`和`PATCH`对您的要求发送额外的数据。因此，这些方法接受数据数组作为它们的第二个参数。默认情况下，将使用`application/json`内容类型发送数据：

```php
$response = Http::post('http://test.com/users', [
    'name' => 'Steve',
    'role' => 'Network Administrator',
]);
```

#### [#](https://houdunren.gitee.io/note/手册/laravel 8.x/6 综合知识/10 HTTP客户端.html#get请求查询参数)GET请求查询参数

发出`GET`请求时，您可以将查询字符串直接附加到URL，也可以将键/值对数组作为方法的第二个参数传递`get`：

```php
$response = Http::get('http://test.com/users', [
    'name' => 'Taylor',
    'page' => 1,
]);
```

#### [#](https://houdunren.gitee.io/note/手册/laravel 8.x/6 综合知识/10 HTTP客户端.html#发送表单url编码的请求)发送表单URL编码的请求

如果要使用`application/x-www-form-urlencoded`内容类型发送数据，则应`asForm`在发出请求之前调用该方法：

```php
$response = Http::asForm()->post('http://test.com/users', [
    'name' => 'Sara',
    'role' => 'Privacy Consultant',
]);
```

#### [#](https://houdunren.gitee.io/note/手册/laravel 8.x/6 综合知识/10 HTTP客户端.html#发送原始请求正文)发送原始请求正文

`withBody`如果要在发出请求时提供原始请求正文，则可以使用此方法：

```php
$response = Http::withBody(
    base64_encode($photo), 'image/jpeg'
)->post('http://test.com/photo');
```

#### [#](https://houdunren.gitee.io/note/手册/laravel 8.x/6 综合知识/10 HTTP客户端.html#多部分请求)多部分请求

如果您希望将文件作为多部分请求发送，则应`attach`在发出请求之前调用该方法。此方法接受文件名及其内容。（可选）您可以提供第三个参数，它将被视为文件的文件名：

```php
$response = Http::attach(
    'attachment', file_get_contents('photo.jpg'), 'photo.jpg'
)->post('http://test.com/attachments');
```

除了传递文件的原始内容，您还可以传递流资源：

```php
$photo = fopen('photo.jpg', 'r');

$response = Http::attach(
    'attachment', $photo, 'photo.jpg'
)->post('http://test.com/attachments');
```

### [#](https://houdunren.gitee.io/note/手册/laravel 8.x/6 综合知识/10 HTTP客户端.html#标头)标头

可以使用该`withHeaders`方法将标头添加到请求中。此`withHeaders`方法接受键/值对的数组：

```php
$response = Http::withHeaders([
    'X-First' => 'foo',
    'X-Second' => 'bar'
])->post('http://test.com/users', [
    'name' => 'Taylor',
]);
```

### [#](https://houdunren.gitee.io/note/手册/laravel 8.x/6 综合知识/10 HTTP客户端.html#认证方式)认证方式

您可以分别使用`withBasicAuth`和`withDigestAuth`方法指定基本身份验证凭据和摘要身份验证凭据：

```php
// Basic authentication...
$response = Http::withBasicAuth('taylor@laravel.com', 'secret')->post(...);

// Digest authentication...
$response = Http::withDigestAuth('taylor@laravel.com', 'secret')->post(...);
```

#### [#](https://houdunren.gitee.io/note/手册/laravel 8.x/6 综合知识/10 HTTP客户端.html#承载令牌)承载令牌

如果您想快速`Authorization`向请求添加承载令牌标头，则可以使用以下`withToken`方法：

```php
$response = Http::withToken('token')->post(...);
```

### [#](https://houdunren.gitee.io/note/手册/laravel 8.x/6 综合知识/10 HTTP客户端.html#超时)超时

该`timeout`方法可用于指定等待响应的最大秒数：

```php
$response = Http::timeout(3)->get(...);
```

如果超过了给定的超时时间，`Illuminate\Http\Client\ConnectionException`将抛出的实例。

### [#](https://houdunren.gitee.io/note/手册/laravel 8.x/6 综合知识/10 HTTP客户端.html#重试)重试

如果您希望HTTP客户端在发生客户端或服务器错误时自动重试请求，则可以使用该`retry`方法。该`retry`方法接受两个参数：应该尝试请求的次数以及两次尝试之间Laravel应该等待的毫秒数：

```php
$response = Http::retry(3, 100)->post(...);
```

如果所有请求均失败，`Illuminate\Http\Client\RequestException`将抛出的实例。

### [#](https://houdunren.gitee.io/note/手册/laravel 8.x/6 综合知识/10 HTTP客户端.html#错误处理)错误处理

与Guzzle的默认行为不同，Laravel的HTTP客户端包装程序不会在客户端或服务器错误（`400`以及`500`服务器的级别响应）上引发异常。您可能会确定这些错误之一是使用返回`successful`，`clientError`或`serverError`方法：

```php
// Determine if the status code was >= 200 and < 300...
$response->successful();

// Determine if the status code was >= 400...
$response->failed();

// Determine if the response has a 400 level status code...
$response->clientError();

// Determine if the response has a 500 level status code...
$response->serverError();
```

#### [#](https://houdunren.gitee.io/note/手册/laravel 8.x/6 综合知识/10 HTTP客户端.html#抛出异常)抛出异常

如果您有一个响应实例，并且想抛出一个实例（`Illuminate\Http\Client\RequestException`如果响应是客户端错误或服务器错误），则可以使用以下`throw`方法：

```php
$response = Http::post(...);

// Throw an exception if a client or server error occurred...
$response->throw();

return $response['user']['id'];
```

该`Illuminate\Http\Client\RequestException`实例具有公共`$response`属性，可让您检查返回的响应。

`throw`如果没有发生错误，该方法将返回响应实例，从而允许您将其他操作链接到该`throw`方法：

```php
return Http::post(...)->throw()->json();
```

### [#](https://houdunren.gitee.io/note/手册/laravel 8.x/6 综合知识/10 HTTP客户端.html#guzzle选项)Guzzle选项

您可以使用方法指定其他[Guzzle请求选项 (opens new window)](http://docs.guzzlephp.org/en/stable/request-options.html)`withOptions`。该`withOptions`方法接受键/值对的数组：

```php
$response = Http::withOptions([
    'debug' => true,
])->get('http://test.com/users');
```

## [#](https://houdunren.gitee.io/note/手册/laravel 8.x/6 综合知识/10 HTTP客户端.html#测试)测试

许多Laravel服务提供的功能可帮助您轻松而富有表现力地编写测试，Laravel的HTTP包装器也不例外。使用`Http`Facade的`fake`方法，您可以指示HTTP客户端在发出请求时返回存根/虚拟响应。

### [#](https://houdunren.gitee.io/note/手册/laravel 8.x/6 综合知识/10 HTTP客户端.html#模拟响应)模拟响应

例如，要指示HTTP客户端`200`为每个请求返回空的状态码响应，可以`fake`不带任何参数的方法调用该方法：

```php
use Illuminate\Support\Facades\Http;

Http::fake();

$response = Http::post(...);
```

#### [#](https://houdunren.gitee.io/note/手册/laravel 8.x/6 综合知识/10 HTTP客户端.html#模拟特定网址)模拟特定网址

或者，您可以将数组传递给该`fake`方法。数组的键应代表您希望模拟的URL模式及其相关响应。该`*`字符可以用作通配符。对未模拟的URL的任何请求将实际执行。您可以使用该`response`方法为这些端点构造存根/假响应：

```php
Http::fake([
    // Stub a JSON response for GitHub endpoints...
    'github.com/*' => Http::response(['foo' => 'bar'], 200, ['Headers']),

    // Stub a string response for Google endpoints...
    'google.com/*' => Http::response('Hello World', 200, ['Headers']),
]);
```

如果您想指定一个后备URL模式，该模式将对所有不匹配的URL进行存根，则可以使用单个`*`字符：

```php
Http::fake([
    // Stub a JSON response for GitHub endpoints...
    'github.com/*' => Http::response(['foo' => 'bar'], 200, ['Headers']),

    // Stub a string response for all other endpoints...
    '*' => Http::response('Hello World', 200, ['Headers']),
]);
```

#### [#](https://houdunren.gitee.io/note/手册/laravel 8.x/6 综合知识/10 HTTP客户端.html#模拟响应序列)模拟响应序列

有时，您可能需要指定单个URL应该以特定顺序返回一系列假响应。您可以使用以下`Http::sequence`方法来构建响应：

```php
Http::fake([
    // Stub a series of responses for GitHub endpoints...
    'github.com/*' => Http::sequence()
                            ->push('Hello World', 200)
                            ->push(['foo' => 'bar'], 200)
                            ->pushStatus(404),
]);
```

当响应序列中的所有响应都已耗尽时，任何其他请求都将导致响应序列引发异常。如果要指定默认响应，则在序列为空时应返回该默认响应，可以使用以下`whenEmpty`方法：

```php
Http::fake([
    // Stub a series of responses for GitHub endpoints...
    'github.com/*' => Http::sequence()
                            ->push('Hello World', 200)
                            ->push(['foo' => 'bar'], 200)
                            ->whenEmpty(Http::response()),
]);
```

如果您想模拟一系列响应，但是不需要指定应该模拟的特定URL模式，则可以使用以下`Http::fakeSequence`方法：

```php
Http::fakeSequence()
        ->push('Hello World', 200)
        ->whenEmpty(Http::response());
```

#### [#](https://houdunren.gitee.io/note/手册/laravel 8.x/6 综合知识/10 HTTP客户端.html#虚拟回调)虚拟回调

如果您需要更复杂的逻辑来确定针对某些端点返回的响应，则可以将回调传递给该`fake`方法。此回调将接收的实例，`Illuminate\Http\Client\Request`并应返回响应实例：

```php
Http::fake(function ($request) {
    return Http::response('Hello World', 200);
});
```

### [#](https://houdunren.gitee.io/note/手册/laravel 8.x/6 综合知识/10 HTTP客户端.html#检查请求)检查请求

在模拟响应时，您有时可能希望检查客户端收到的请求，以确保您的应用程序发送了正确的数据或标头。您可以通过`Http::assertSent`在调用后调用方法来完成此操作`Http::fake`。

该`assertSent`方法接受一个回调，该回调将被提供一个`Illuminate\Http\Client\Request`实例，并应返回一个布尔值，指示请求是否符合您的期望。为了使测试通过，必须至少发出一个与给定期望相匹配的请求：

```php
Http::fake();

Http::withHeaders([
    'X-First' => 'foo',
])->post('http://test.com/users', [
    'name' => 'Taylor',
    'role' => 'Developer',
]);

Http::assertSent(function ($request) {
    return $request->hasHeader('X-First', 'foo') &&
           $request->url() == 'http://test.com/users' &&
           $request['name'] == 'Taylor' &&
           $request['role'] == 'Developer';
});
```

如果需要，您可以断言未使用以下`assertNotSent`方法发送特定请求：

```php
Http::fake();

Http::post('http://test.com/users', [
    'name' => 'Taylor',
    'role' => 'Developer',
]);

Http::assertNotSent(function (Request $request) {
    return $request->url() === 'http://test.com/posts';
});
```

或者，如果您想断言没有发送请求，则可以使用以下`assertNothingSent`方法：

```php
Http::fake();

Http::assertNothingSent();
```