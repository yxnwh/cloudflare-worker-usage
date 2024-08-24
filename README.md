# Cloudflare Worker KV存储项目

## 项目简介

这个项目提供了一个Cloudflare Worker脚本,用于利用Cloudflare的KV(Key-Value)存储空间来存储和管理个人网站的各种数据。这个解决方案特别适合需要持久化存储但数据量不大的场景。
参考了 [cmliu@CF-Workers-TEXT2KV](https://github.com/cmliu/CF-Workers-TEXT2KV)

## 主要特性

- 利用Cloudflare KV存储各种类型的数据
- 支持存储文本数据、JSON数据
- 可存储小型Excel文件和图片，需要先转换为base64，再以文本text/plain形式存储
- 客户端采用AES加密，服务器端采用第三方AES解密API进行解密，保证了隐私
- 默认单个文件大小0.75MB，大于的话将会自动分割上传（服务端自动分割），但下载时需要自行合并分割的文件，使用nodejs代码的操作示例见后

## 使用方法

1. 在Cloudflare中创建一个Worker
2. 将本项目的JavaScript代码部署到您的Worker中
3. 在Cloudflare中创建一个KV命名空间,并将其绑定到您的Worker
4. 可选，settings--triggers--Add Custom Domain，绑定自己的域名，方便记忆 
5. 使用提供的API端点来存储和获取数据
6. settings--Variables需要设置两个变量（**注意都是大写**）```KEY```和```TOKEN```，KEY代表AES密钥，TOKEN作为访问文件的密钥，**注意这两个值应与你本地代码中的KEY和TOKEN相对应，有一个不对应都不会获取到正确的response**

### API端点

- `PUT /name?token=xxx`: 存储或者更新指定name数据
- `GET /name?token=xxx`: 获取指定name的数据
- `DELETE /name?token=xxx`: 删除指定name的数据
**注意：name为你绑定的KV存储空间的key名**

## 示例

存储数据:
```http
POST /api/data
Content-Type: application/json

{
  "key": "my-data",
  "value": "这是一些文本数据"
}
```
