<h1 align="center">flysystem-oss </h1>

<p align="center">:floppy_disk:  Flysystem adapter for the oss storage.</p>


## 扩展包要求

-   PHP >= 8.0.2

## 安装命令

```shell
$ composer require "apixx/flysystem-oss"
```

## 使用

```php
use League\Flysystem\Filesystem;
use Iidestiny\Flysystem\Oss\OssAdapter;
use Iidestiny\Flysystem\Oss\Plugins\FileUrl;

$prefix = ''; // 前缀，非必填
$accessKeyId = 'xxxxxx';
$accessKeySecret = 'xxxxxx';
$endpoint= 'oss.iidestiny.com'; // ssl：https://iidestiny.com
$bucket = 'bucket';
$isCName = true; // 如果 isCname 为 false，endpoint 应配置 oss 提供的域名如：`oss-cn-beijing.aliyuncs.com`，cname 或 cdn 请自行到阿里 oss 后台配置并绑定 bucket

$adapter = new OssAdapter($accessKeyId, $accessKeySecret, $endpoint, $bucket, $isCName, $prefix);

// $adapter->setCdnUrl('https://cdn.iidestiny.com'); // 设置cdn url

$flysystem = new Filesystem($adapter);

```

## 常用方法

```php
void $flysystem->write('file.md', 'contents');

void $flysystem->write('file.md', 'http://httpbin.org/robots.txt', ['options' => ['xxxxx' => 'application/redirect302']]);

void $flysystem->writeStream('file.md', fopen('path/to/your/local/file.jpg', 'r'));

void $flysystem->move('foo.md', 'bar.md');

void $flysystem->copy('foo.md', 'foo2.md');

void $flysystem->delete('file.md');

void $flysystem->deleteDirectory('file.md');

bool $flysystem->fileExists('file.md');

 $flysystem->read('file.md');

array $flysystem->listContents();

array $flysystem->getMetadata('file.md');

int $flysystem->fileSize('file.md');

string $adapter->getUrl('file.md'); // 

string $flysystem->mimeType('file.md');

int $flysystem->lastModified('file.md');
```

## 插件扩展

从 `filesystem v2` 版本开始插件功能被移除，所以要使用这些功能需要引用创建 `Adapter` 适配器，以下所有案例都是以前插件功能

```php
$adapter = new OssAdapter($accessKeyId, $accessKeySecret, $endpoint, $bucket, $isCName, $prefix);

// 获取 oss 资源访问链接
string $adapter->getUrl('file.md');

// url 访问有效期 & 图片处理「$timeout 为多少秒过期」
// 默认GET
string $adapter->getTemporaryUrl('file.md', $timeout, ['x-oss-process' => 'image/circle,r_100']);
// PUT
string $adapter->getTemporaryUrl('file.md', $timeout, ['x-oss-process' => 'image/circle,r_100'],'PUT');

// 多个bucket切换
$adapter->bucket('test')->has('file.md');
```

## 获取官方完整 OSS 处理能力

阿里官方 SDK 可能处理了更多的事情，如果你想获取完整的功能可通过此插件获取，
然后你将拥有完整的 oss 处理能力

```php
$kernel = $adapter->ossKernel();

// 例如：防盗链功能
$refererConfig = new RefererConfig();
// 设置允许空Referer。
$refererConfig->setAllowEmptyReferer(true);
// 添加Referer白名单。Referer参数支持通配符星号（*）和问号（？）。
$refererConfig->addReferer("www.aliiyun.com");
$refererConfig->addReferer("www.aliiyuncs.com");

$kernel->putBucketReferer($bucket, $refererConfig);
```

> 更多功能请查看官方 SDK 手册：https://help.aliyun.com/document_detail/32100.html?spm=a2c4g.11186623.6.1055.66b64a49hkcTHv


## 参考

-   [overtrue/flysystem-qiniu](https://github.com/overtrue/flysystem-qiniu)
