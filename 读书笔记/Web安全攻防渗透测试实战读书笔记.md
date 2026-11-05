# Web安全攻防渗透测试实战读书笔记

## 第1章   渗透测试の信息收集

**进行渗透测试之前，最重要的一步是进行信息收集 ！**

我们要在这个阶段尽可能收集目标组织的信息，**越了解测试目标，测试工作越容易**

最主要的收集工作是：

- 服务器配置信息
- 网站敏感信息

其中包括了：

- 域名和子域名信息
- 目标网站系统
- CMS指纹
- 目标网站真实IP
- 开放的端口
- ..........

> CMS 是 Content Management System 的缩写，中文叫作「内容管理系统」
>
> 简单来说，它是一套帮助用户快速搭建和管理网站的软件
>
> 例如，你想搭建一个博客网站，不想从零开始编写 HTML、CSS、JavaScript、数据库和后台管理功能，就可以使用现成的 CMS
>
> 常见CMS有：
>
> - WordPress，非常流行的开源内容管理系统，常用于博客、企业网站和个人网站
> - Drupal，功能强大，常用于复杂的网站和内容管理平台
> - Joomla，开源 CMS，支持扩展和多种网站管理功能
> - Discuz！，常见的论坛社区程序
> - DedeCMS，国内曾经使用较广泛的网站内容管理系统
>
> CMS 指纹识别，就是通过网站暴露出来的特征，判断这个网站使用了什么 CMS，以及可能使用的版本、插件和主题
>
> now，假设你正在访问一个网站，发现它的源代码里存在：
>
> <link rel="stylesheet"
> href="/wp-content/themes/example/style.css">
>
> 你注意到：
>
> /wp-content/
>
> 这是 WordPress 常见的目录特征
>
> 因此，你可以初步判断：
>
> 目标网站
>    ↓
> 发现 /wp-content/ 路径
>    ↓
> 推测使用 WordPress
>    ↓
> 进一步验证 CMS 版本
>    ↓
> 收集主题、插件等信息
>
> 注意：单凭一个特征**不能百分之百确定** CMS，因为路径可能被修改，也可能被伪造

CSM指纹识别主要看什么？

|  识别特征  |          具体内容          |
| :--------: | :------------------------: |
| 页面源代码 | HTML注释，CSS，JS引用路径  |
| HTTP响应头 | Server，X-Powered-By等字段 |
|  网站目录  |    特定 CMS 的默认目录     |
|   Cookie   | 特定程序生成的 Cookie 名称 |
|  网站文件  | README、安装文件、版本文件 |
|  网站图标  |   Favicon 特征及其哈希值   |

常见工具：

WhatWeb，CMSeek

如果在信息收集阶段，你发现：

```
网站：example.com
CMS：WordPress
版本：某个已知版本
插件：Plugin A
```

那么接下来就可以开展针对性的安全研究：

1. 检查该 CMS 版本是否存在已公开的安全漏洞
2. 检查插件是否存在已知漏洞
3. 检查主题是否存在安全问题
4. 分析网站配置是否存在安全隐患
5. 在授权范围内验证漏洞是否真实存在

### 1.1   收集域名信息

获得目标域名之后，我们要做的第一件事就是**收集域名的注册信息**，包括**该域名的DNS服务器信息**和**注册人的联系信息**

#### 1.1.1   Whois查询

Whois是一个标准的互联网协议，是一个用于查询域名是否已经被注册以及注册域名的详细信息的数据库（域名所有人，域名注册商），在这个过程中得到注册人的姓名和邮箱信息通常对测试个人站点非常有用，通过搜索引擎和社交网络挖掘出所有人的很多信息，对中小站点而言，域名所有人往往就是管理员

在kali中，Whois已默认安装，只要输入查询的域名即可

在线的Whois查询常用工具有：

https://whois.aizhan.com/

https://whois.chinaz.com/

https://www.virustotal.com/

#### 1.1.2   Whois查询实例

首先根据GPT的建议，我们将信息搜集分为6大类

- 目标基本信息

域名，注册商，注册时间，组织信息，域名归属

- DNS与网络资产

子域名，IP地址，DNS记录，IP所属组织，网络服务

- 端口和服务信息

开放端口，运行服务，服务版本，网络架构

- 网站技术指纹

CMS，Web服务器，编程语言，开发框架，插件和组件

- Web应用结构

网站目录，接口API，参数，登录入口，文件上传，业务功能

- 安全风险信息

已知漏洞，错误配置，敏感信息暴露，身份认证和权限控制问题

下面这张表建议你收藏起来，以后学习每个工具时可以回来对照。

| 收集内容 | 重点关注什么               | 常用工具                 |
| -------- | -------------------------- | ------------------------ |
| 域名信息 | 注册商、注册时间、注册组织 | WHOIS                    |
| DNS 信息 | A、AAAA、NS、MX、TXT 记录  | dig、nslookup            |
| 子域名   | 主域名下的其他站点         | Subfinder、Amass         |
| IP 信息  | IP 归属、ASN、网络范围     | WHOIS、IP 查询服务       |
| 端口服务 | 开放端口、运行服务         | Nmap                     |
| Web 指纹 | CMS、框架、服务器          | WhatWeb、Wappalyzer      |
| 网站目录 | 页面路径、文件、目录       | 授权环境下的目录枚举工具 |
| Web 请求 | 请求方法、参数、Cookie     | Burp Suite               |
| 漏洞信息 | 公开漏洞、版本风险         | NVD、CVE、Nuclei         |

那么，我进行了一项实验，我用kali自带的Whois查询了chinaz.com这个网站

我获得了两组Whois信息，分别来自

- 第一组，.com 域名注册局 VeriSign
- 第二组，域名注册商 eName（易名）

逐项分析返回的结果：

> 隐藏部分真实信息，规避风险

```
Registry Domain ID: xxxxxxxx_DOMAIN_COM-VRSN
```

Registry Domain ID：注册局分配的域名标识

```
Registrar: eName Technology Co., Ltd.
Registrar IANA ID: 1331
Registrar URL: http://www.ename.net
```

Registrar：域名注册商

Registrar IANA ID：注册商的识别编号

Registrar URL：注册商官方网站

**收集注册商信息能帮助我们了解域名的注册管理关系，以及后续查询域名注册、转移和续费等相关信息**

**但注册商不一定是网站的实际运营商，也不一定提供网站服务器托管服务**

```
Creation Date: 2002-12-19T21:41:44Z

Registry Expiry Date: 2027-12-19T21:41:44Z

Updated Date: 2026-05-12T13:56:30Z
```

Creation Date：域名最初注册时间

Registry Expiry Date：注册局记录的到期时间

Updated Date：注册局记录的更新时间

**域名历史有时可以帮助研究目标资产的变更情况，但单纯注册时间长，并不代表网站更安全**

```
Domain Status: clientDeleteProhibited
Domain Status: clientTransferProhibited
```

Domain Status：域名状态

clientDeleteProhibited：禁止通过注册商客户端删除域名

clientTransferProhibited：禁止通过注册商客户端转移域名

```
Registrant State/Province: FuJian
Registrant Country: CN

Registrant Email:
https://whois.ename.net/contact/chinaz.com
```

Registrant State/Province：福建（注册省份）

Registrant Country：中国（注册人所在国家）

Registrant Email：通过注册商联系页面转发或联系

> 从返回结果看，域名注册人并未公开真实邮箱，提供了间接的联系渠道，这是Whois的隐私保护

```
Name Server: V1.xxxxx.COM
Name Server: V2.xxxxx.COM
```

**这是本次 WHOIS 查询中非常值得继续研究的内容**

Name Server：域名服务器

它负责帮助互联网系统查询域名对应的 DNS 记录

返回了两个DNS服务器：V1.xxxxx.COM 和 V2.xxxxx.COM

```
DNSSEC: unsigned
```

DNSSEC： DNS 安全扩展

它主要用于通过数字签名验证 DNS 数据的真实性和完整性

unsigned表示WHOIS 数据库中该域名没有标记为启用 DNSSEC 签名

**这是一个值得记录的安全配置观察项，而不是已经确认的漏洞**


