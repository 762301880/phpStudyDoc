# faker

**参考资料**

| name              | url                                                          |
| ----------------- | ------------------------------------------------------------ |
| 后盾人文档-faker  | [link](https://doc.houdunren.com/%E6%8F%92%E4%BB%B6%E6%89%A9%E5%B1%95/Laravel/5%20faker.html#faker) |
| laravel-china文档 | [link](https://learnku.com/docs/laravel/8.x/database-testing/9416) |

##  基本使用

**中文支持**

> 在 `config/app.php' 定义 facker 使用的语言，不过只支持少量属性中文，比如 name 属性

```php
'faker_locale' => 'zh_CN',
```

**模型工厂**

> 下面是在模型工厂中使用 fake 生成基本数据

```php
<?php

use App\Models\User;
use Faker\Generator as Faker;
use Modules\Article\Entities\ArticleCategory;
use Modules\Article\Entities\ArticleContent;

$factory->define(ArticleContent::class, function (Faker $faker) {
  return [
    'site_id' => 1,
    'user_id' => User::orderBy(DB::raw('RAND()'))->first()->id,
    'category_id' => ArticleCategory::orderBy(DB::raw('RAND()'))->first()->id,
    'title' => $faker->sentence(),
    'thumb' => $faker->imageUrl(300, 300),
    'description' => $faker->sentence(10),
    'content' => $faker->paragraphs(3),
    'source' => '后盾人',
    'author' => '向军'
  ];
});
```

## 可用方法

> 下面是[faker (opens new window)](https://github.com/fzaninotto/Faker)可以生成数据的方法

### #基本

```php
$randomDigit = $faker->randomDigit;//生成0-9之间的随机数
$randomDigitNotNull = $faker->randomDigitNotNull;//生成1-9之间的随机数
$randomNumber = $faker->randomNumber(5, true);//生成5位整数，true表示严格模式，即只能5位
$randomFloat = $faker->randomFloat(2, 0, 10);//生成浮点数，两位小数点，范围是0-10之间
$numberBetween = $faker->numberBetween(0, 100);//生成随机整数，范围是0-100之间
$randomLetter = $faker->randomLetter;//返回a-z之间任意的一个小写字符
$randomElements = $faker->randomElements(['a', 'b', 'c', 'd'], 2);//返回数组中的随机两个元素
$randomElement = $faker->randomElement(['aa', 'bb', 'cc', 'dd']);//随机返回数组中的一个元素
$suffle = $faker->shuffle('hello, world'); //将字串中的字符打乱返回
$suffle = $faker->shuffle(['aa', 'bb', 'cc', 'dd']); //将数组中的元素打乱返回
$numerify = $faker->numerify('Hello #####');//#####替换为随机数字，输出类似：Hello 03501
$lexify = $faker->lexify('Hello ???');//???替换为3个随机小写字符，输出类似：Hello krg
$bothify = $faker->bothify('hello ##??');//#替换为随机数字,?替换为随机小写字符.输出类似：hello 15cr
$asciify = $faker->asciify('hello *****');//*替换为随机字符，输出类似：hello 5Ynt[
$regexify = $faker->regexify('[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,4}');//根据正则表达式返回字串
```

### [#](https://doc.houdunren.com/插件扩展/Laravel/5 faker.html#文本)文本

```php
$word = $faker->word;//返回一个单词
$words = $faker->words(3, false);//返回3个单词，false表示返回一个数组；true表示返回一个字符串，单词之间用空格分开
$sentence = $faker->sentence(5, true);//返回一个句子，false表示只能含有5个单词，true表示可以在5个单词左右
$sentences = $faker->sentences(3, false);//返回3条句子，false表示返回一个数组，true表示将三条句子拼成一条返回
$paragraph = $faker->paragraph(3, true);//返回一个段落，由3条句子组成。false表示只能有3条句子，true表示可以在3条句子左右
$paragraphs = $faker->paragraphs(4, false);//返回4个段落。false表示返回一个数组，true表示将段落拼接在一起，并且用换行符分割
$text = $faker->text(200);//返回一段文本，最多只能含有200个字符
$randomHtml = $faker->randomHtml(2, 3);//生成不超过2级深度的HTML文档，并且在任何级别上都不超过3个元素。
$realText = $faker->realText();//一段叙事文本
```

### [#](https://doc.houdunren.com/插件扩展/Laravel/5 faker.html#人物)人物

```php
$title = $faker->title('female');//参数：title($gender = null|'male'|'female') .返回称呼。例如：Mrs.|Prof.|Dr.
$titleMale = $faker->titleMale;//返回男性称呼
$titleFemale = $faker->titleFemale;//返回女性称呼
$name = $faker->name('female');//参数：name($gender = null|'male'|'female') .返回姓名
$firstName = $faker->firstName('female');//参数：firstName($gender = null|'male'|'female') .返回名
$firstNameMale = $faker->firstNameMale;//男性名字
$firstNameFemale = $faker->firstNameFemale;//女性名字
$lastName = $faker->lastName;//姓
```

### [#](https://doc.houdunren.com/插件扩展/Laravel/5 faker.html#地址)地址

```php
$cityPrefix = $faker->cityPrefix;//城市前缀.如：Lake
$secondaryAddress = $faker->secondaryAddress;//二级地址.如：Suite 061
$state = $faker->state;//州、省（如：Colorado、四川省）
$stateAbbr = $faker->stateAbbr;//省份简称.如：晋、蒙、浙、冀
$citySuffix = $faker->citySuffix;//城市后缀.如：side、land、port、Ville
$streetSuffix = $faker->streetSuffix;//街道后缀.如：Ramp、Plains
$buildingNumber = $faker->buildingNumber;//建筑物编号
$city = $faker->city;//城市
$streetName = $faker->streetName;//街道名称
$streetAddress = $faker->streetAddress;//街道地址
$postcode = $faker->postcode;//邮政编码
$address = $faker->address;//地址（城市+区）
$country = $faker->country;//国家
$latitude = $faker->latitude;//纬度 latitude($min = -90, $max = 90)
$longitude = $faker->longitude;//经度 longitude($min = -180, $max = 180)
```

### [#](https://doc.houdunren.com/插件扩展/Laravel/5 faker.html#电话号码)电话号码

```php
$phoneNumber = $faker->phoneNumber;//手机号码
$tollFreePhoneNumber = $faker->tollFreePhoneNumber;
$e164PhoneNumber = $faker->e164PhoneNumber;
```

### [#](https://doc.houdunren.com/插件扩展/Laravel/5 faker.html#公司)公司

```php
$catchPhrase = $faker->catchPhrase;//口号
$bs = $faker->bs;
$company = $faker->company;//公司名称
$companySuffix = $faker->companySuffix;//公司名称后缀
$jobTitle = $faker->jobTitle;//职称
```

### [#](https://doc.houdunren.com/插件扩展/Laravel/5 faker.html#日期时间)日期时间

```php
$unixTime = $faker->unixTime;//返回随机时间戳
$unixTime = $faker->unixTime('now');//返回随机时间戳 可选最后截止时间
$dateTime = $faker->dateTime;//返回一个随机的DateTime对象
$dateTime = $faker->dateTime('now', 'PRC');//返回一个随机的DateTime对象，可选择最后截止时间和时区
$dateTimeAD = $faker->dateTimeAD;//返回一个随机的DateTime对象
$dateTimeAD = $faker->dateTimeAD('now', 'PRC');//返回一个随机的DateTime对象，可选择最后截止时间和时区
$iso8601 = $faker->iso8601;//返回一个随机的字符串形式的时间
$iso8601 = $faker->iso8601('now');//返回一个随机的字符串形式的时间,可选择最后截止时间
$date = $faker->date("Y-m-d H:i:s", 'now');//指定格式返回时间,可选择最后截止时间
$time = $faker->time('Y-m-d H:i:s', 'now');//(同上)指定格式返回时间,可选择最后截止时间
$dateTimeBetween = $faker->dateTimeBetween('2019-01-01', 'now', 'PRC');//返回指定时间区间的DateTime对象，可选时区
$dateTimeInInterval = $faker->dateTimeInInterval('-5 years', '+5 days', 'PRC');//返回指定时间区间的DateTime对象，可选时区(第一个参数：开始时间，第二个参数：时间范围)
$dateTimeThisCentury = $faker->dateTimeThisCentury;//返回一个本世纪内的DateTime对象
$dateTimeThisCentury = $faker->dateTimeThisCentury('1950-01-01', 'PRC');//返回一个本世纪内的DateTime对象.指定截止时间和时区
$dateTimeThisDecade = $faker->dateTimeThisDecade;//返回一个前十年内的DateTime对象
$dateTimeThisDecade = $faker->dateTimeThisDecade('2015-01-01', 'PRC');//返回一个前十年内的DateTime对象.指定允许的最后时间和时区
$dateTimeThisYear = $faker->dateTimeThisYear;//返回一个前一年内的DateTime对象
$dateTimeThisYear = $faker->dateTimeThisYear('now', 'PRC');//返回一个前一年内的DateTime对象.指定允许的最后时间和时区
$dateTimeThisMonth = $faker->dateTimeThisMonth;//返回一个前一个月内的DateTime对象
$dateTimeThisMonth = $faker->dateTimeThisMonth('-15 days', 'PRC');//返回一个前一个月内的DateTime对象.指定允许的最后时间和时区
$amPm = $faker->amPm;//上午/下午
$dayOfMonth = $faker->dayOfMonth;//返回几号
$dayOfWeek = $faker->dayOfWeek;//返回星期几
$month = $faker->month;//返回月份
$monthName = $faker->monthName;//返回月份名称
$year = $faker->year;//返回年份
$year = $faker->year('2000-01-01');//返回年份.可指定最后截止日期
$timezone = $faker->timezone;//返回时区
```

### [#](https://doc.houdunren.com/插件扩展/Laravel/5 faker.html#互联网)互联网

```php
$email = $faker->email;//返回一个随机邮箱
$safeEmail = $faker->safeEmail;//返回一个以@example.com结尾的安全邮箱
$freeEmail = $faker->freeEmail;//返回一个随机邮箱
$companyEmail = $faker->companyEmail;//返回企业邮箱（中文语言包下不可用）
$freeEmailDomain = $faker->freeEmailDomain;//返回一个邮件域名
$safeEmailDomain = $faker->safeEmailDomain;//返回安全的邮件域名
$userName = $faker->userName;//用户名
$password = $faker->password;//密码
$domainName = $faker->domainName;//域名（中文语言包下不可用）
$domainWord = $faker->domainWord;//不带后缀的域名（中文语言包下不可用）
$tld = $faker->tld;//域名后缀：如com、org（中文语言包下不可用）
$url = $faker->url;//返回一个随机url（中文语言包下不可用）
$slug = $faker->slug;
$ipv4 = $faker->ipv4;//返回一个ipv4地址
$ipv6 = $faker->ipv6;//返回一个ipv6地址
$localIpv4 = $faker->localIpv4;
$macAddress = $faker->macAddress;//mac地址
```

### [#](https://doc.houdunren.com/插件扩展/Laravel/5 faker.html#用户代理)用户代理

```php
$userAgent = $faker->userAgent;//返回一个随机的用户代理信息
$chrome = $faker->chrome;//返回一个chrome浏览器的用户代理信息
$firefox = $faker->firefox;//返回一个firefox浏览器的用户代理信息
$safari = $faker->safari;//返回一个safari浏览器的用户代理信息
$opera = $faker->opera;//返回一个opera浏览器的用户代理信息
$internetExplorer = $faker->internetExplorer;//返回一个internetExplorer浏览器的用户代理信息
```

### [#](https://doc.houdunren.com/插件扩展/Laravel/5 faker.html#支付)支付

```php
$creditCardType = $faker->creditCardType;//信用卡类型
$creditCardNumber = $faker->creditCardNumber;//信用卡号
$creditCardExpirationDate = $faker->creditCardExpirationDate;//信用卡到期日 (DateTime对象)
$creditCardExpirationDateString = $faker->creditCardExpirationDateString;//信用卡到期日期字符串
$creditCardDetails = $faker->creditCardDetails;//信用卡详情（数组）
$swiftBicNumber = $faker->swiftBicNumber;
$iban = $faker->iban;//国际银行账户
```

### [#](https://doc.houdunren.com/插件扩展/Laravel/5 faker.html#颜色)颜色

```php
$hexcolor = $faker->hexcolor;//十六进制的随机色：'#fa3cc2'
$rgbcolor = $faker->rgbcolor;//RGB格式的随机色（字串形式）：'0,255,122'
$rgbColorAsArray = $faker->rgbColorAsArray;//RGB格式的随机色（数组形式）：array(0,255,122)
$rgbCssColor = $faker->rgbCssColor;//RGB格式的随机色的css表示：'rgb(0,255,122)'
$safeColorName = $faker->safeColorName;//一个安全的随机色名称
$colorName = $faker->colorName;//随机色名称
```

### [#](https://doc.houdunren.com/插件扩展/Laravel/5 faker.html#文件)文件

```php
$fileExtension = $faker->fileExtension;//文件后缀
$mimeType = $faker->mimeType;//mime类型
// 将一个随机文件从源文件复制到目标目录，并返回fullpath
$file = $faker->file($sourceDir = './up1', $targetDir = './up2');
// 将一个随机文件从源文件复制到目标目录，并返回basename
$file = $faker->file($sourceDir = './up1', $targetDir = './up2', false);
```

### [#](https://doc.houdunren.com/插件扩展/Laravel/5 faker.html#图片)图片

```php
$imageUrl = $faker->imageUrl();//https://lorempixel.com/640/480/?93028
$imageUrl = $faker->imageUrl(320, 320, 'cats');//https://lorempixel.com/320/320/cats/?68416
$imageUrl = $faker->imageUrl(320, 320, 'cats', true, 'Faker');//https://lorempixel.com/320/320/cats/Faker/?68118
$imageUrl = $faker->imageUrl(320, 320, 'cats', true, 'Faker', true);//https://lorempixel.com/gray/320/320/cats/Faker/?28732

//生成图片并保存到本地 '/tmp/13b73edae8443990be1aa8f1a483bc27.jpg'
$image = $faker->image('./tmp', 320, 320);
//设置类别并生成图片保存到本地  'tmp/13b73edae8443990be1aa8f1a483bc27.jpg'
$image = $faker->image('./tmp', 320, 320, 'cats');
//设置类别并生成图片保存到本地，只返回文件名 '13b73edae8443990be1aa8f1a483bc27.jpg'
$image = $faker->image('./tmp', 320, 320, 'cats', false);
// 生成没有随机化的图像
$image = $faker->image('./tmp', 320, 320, 'cats', true, false);
//生成图片并加上水印字体  'tmp/13b73edae8443990be1aa8f1a483bc27.jpg'
$image = $faker->image('./tmp', 320, 320, 'cats', true, true, 'Faker');
```

### [#](https://doc.houdunren.com/插件扩展/Laravel/5 faker.html#uuid)UUID

```php
$uuid = $faker->uuid;//生成一个uuid
```

### [#](https://doc.houdunren.com/插件扩展/Laravel/5 faker.html#条形码)条形码

```php
Faker\Provider\Barcode 条形码
```

### [#](https://doc.houdunren.com/插件扩展/Laravel/5 faker.html#杂项)杂项

```php
$boolean = $faker->boolean;//生成一个布尔值
$md5 = $faker->md5;//生成一个md5码
$sha1 = $faker->sha1;//生成一个sha1
$sha256 = $faker->sha256;//生成一个sha256
$locale = $faker->locale;//生成一个地区编码：如zh_CN
$countryCode = $faker->countryCode;//生成一个国家编码。如：UK
$languageCode = $faker->languageCode;//生成一个语言编码.如：en
$currencyCode = $faker->currencyCode;//生成一个货币代码.如：EUR
$emoji = $faker->emoji;//生成一个emoji表情
$biasedNumberBetween = $faker->biasedNumberBetween($min = 10, $max = 20, $function = 'sqrt');//得到10到20之间的随机数，并且更多机会接近20
```

