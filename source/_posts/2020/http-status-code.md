---
title: HTTP状态码
permalink: http-status-code
date: 2020-03-24
tags: HTTP
---

## 1XX

以 1 开头的状态码代表请求已被接受，需要继续处理。

| Code | Describe            | Note                                                         |
| ---- | ------------------- | ------------------------------------------------------------ |
| 100  | Continue            | 服务器已经接收到请求头，并且客户端应继续发送请求主体         |
| 101  | Switching Protocols | 服务器已经理解了客户端的请求，并将通过Upgrade消息头通知客户端采<br>用不同的协议来完成这个请求。 |
| 102  | Processing          | 请求可能包含许多涉及文件操作的子请求，需要很长时间才能完成请求。<br>该代码表示服务器已经收到并正在处理请求，但无响应可用。<br>这样可以防止客户端超时，并假设请求丢失 |

<!--more-->

## 2XX

以 2 开头的状态码代表请求已被服务器接受并处理

| Code | Describe                      | Note                                                         |
| ---- | ----------------------------- | ------------------------------------------------------------ |
| 200  | OK                            | 请求成功，请求所希望的响应头或数据体将随此响应返回           |
| 201  | Created                       | 请求已经被实现，而且有一个新的资源已经依据请求的需要而创建，且其URI已经随Location头信息返回 |
| 202  | Accepted                      | 服务器已接受请求，但尚未处理。最终该请求可能会也可能不会被执行，并且可能在处理发生时被禁止 |
| 203  | Non-Authoritative Information | 服务器是一个转换代理服务器，以`200 OK`状态码为起源，但回应了原始响应的修改版本。 |
| 204  | No Content                    | 服务器成功处理了请求，没有返回任何内容                       |
| 205  | Reset Content                 | 服务器成功处理了请求，但没有返回任何内容。与204响应不同，此响应要求请求者重置文档视图 |
| 206  | Partial Conten                | 服务器已经成功处理了部分GET请求                              |
| 207  | Multi-Status                  | 代表之后的消息体将是一个XML消息，并且可能依照之前子请求数量的不同，包含一系列独立的响应代码 |
| 208  | Already Reported              | DAV绑定的成员已经在（多状态）响应之前的部分被列举，且未被再次包含 |
| 226  | IM Used                       | 服务器已经满足了对资源的请求，对实体请求的一个或多个实体操作的结果表示 |

## 3XX

以 3 为开头的状态码代表重定向

| Code | Describe         | Note                                                         |
| ---- | ---------------- | ------------------------------------------------------------ |
| 300  | Multiple Choices | 代表请求的资源有多个可选择的信息，返回一个列表用户或浏览器自行选择一个进行重定向 |

有些浏览器实现 301 时没有按照协议（可以修改请求方法），因此定义了 308

| Code | Describe           | Note                                                         |
| ---- | ------------------ | ------------------------------------------------------------ |
| 301  | Moved Permanently  | 永久重定向，后续请求应该使用响应的地址<br>理论上除了GET与HEAD之外的请求方法，浏览器应禁止自动重定向<br>但是有的浏览器的实现中会改变请求方法（原请求为POST，重定向请求为GET） |
| 308  | Permanent Redirect | 与301基本相同，区别在于不允许修改请求方法                    |

类似 301 的实现一样，有些浏览器实现 302 没有按照协议，由 303 和 307 进行限制。

但是实际实现中还是以 302 和 307 为主

| Code | Describe           | Note                                                         |
| ---- | ------------------ | ------------------------------------------------------------ |
| 302  | Found              | 临时重定向，后续请求应该使用原来的URI<br>并且除非设置Cache-contrl和expries等头，否则不进行缓存<br>理论上除了GET与HEAD之外的请求方法，浏览器应禁止自动重定向<br/>但是有的浏览器的实现中会改变请求方法（原请求为POST，重定向请求为GET） |
| 303  | See Other          | 与302基本相同，允许修改请求方法（原为POST，重定向为GET）     |
| 307  | Temporary Redirect | 与302基本相同，不允许修改请求方法（原为POST，重定向仍为POST） |



| Code | Describe     | Note                                                   |
| ---- | ------------ | ------------------------------------------------------ |
| 304  | Not Modified | 资源未变更，不需要给客户端响应数据，客户端可以使用缓存 |



| Code | Describe     | Note                                                         |
| ---- | ------------ | ------------------------------------------------------------ |
| 305  | Use Proxy    | 请求的资源必须使用指定的代理才能进行访问<br>Location中给出代理的URI信息，客户端重复发送一个请求，向代理请求资源 |
| 306  | Switch Proxy | 后续请求使用指定的代理                                       |

## 4XX

| Code | Describe                        | Note                                                         |
| ---- | ------------------------------- | ------------------------------------------------------------ |
| 400  | Bad Request                     | 明显的请求错误，比如请求语法错误，服务器看不懂不知道怎么处理 |
| 401  | Unauthorized                    | 未认证，需要登录                                             |
| 402  | Payment Required                | 预留的，很少用                                               |
| 403  | Forbidden                       | 服务器理解请求，但是拒绝执行                                 |
| 404  | Not Found                       | 未找到资源                                                   |
| 405  | Method Not Allowed              | 请求使用的方法不能被用于请求该资源                           |
| 406  | Not Acceptable                  | 请求的资源的内容特性无法满足请求头中的条件<br>因而无法生成响应实体，该请求不可接受 |
| 407  | Proxy Authentication Required   | 类似401，客户端必须在代理服务器上进行身份验证                |
| 408  | Request Timeout                 | 请求超时，客户端没有在服务器预备等待的时间内完成一个请求的发送 |
| 409  | Conflict                        | 表示因为请求存在冲突无法处理该请求<br>例如多个同步更新之间的编辑冲突。 |
| 410  | Gone                            | 表示所请求的资源不再可用，将不再可用。<br>当资源被有意地删除并且资源应被清除时，应该使用这个。<br>在收到410状态码后，用户应停止再次请求资源。<br>但大多数服务端不会使用此状态码，而是直接使用404状态码。 |
| 411  | Length Required                 | 服务器拒绝在没有定义Content-Length头的情况下接受请求         |
| 412  | Precondition Failed             | 服务器在验证在请求的头字段中给出先决条件时，没能满足其中的一个或多个。<br>这个状态码允许客户端在获取资源时在请求的元信息（请求头字段数据）<br>中设置先决条件，以此避免该请求方法被应用到其希望的内容以外的资源上 |
| 413  | Request Entity Too Large        | 表示服务器拒绝处理当前请求，<br>因为该请求提交的实体数据大小超过了服务器愿意或者能够处理的范围。<br>此种情况下，服务器可以关闭连接以免客户端继续发送此请求。 |
| 414  | Request-URI Too Long            | 如果这个状况是临时的，服务器应当返回一个Retry-After的响应头，<br>以告知客户端可以在多少时间以后重新尝试。<br>表示请求的URI长度超过了服务器能够解释的长度，因此服务器拒绝对该请求提供服务 |
| 415  | Unsupported Media Type          | 对于当前请求的方法和所请求的资源，<br>请求中提交的[互联网媒体类型](https://zh.wikipedia.org/wiki/互联网媒体类型)并不是服务器中所支持的格式，<br>因此请求被拒绝。例如，客户端将图像上传格式为svg，<br>但服务器要求图像使用上传格式为jpg。 |
| 416  | Requested Range Not Satisfiable | 客户端已经要求文件的一部分，但服务器不能提供该部分。例如，如果客户端要求文件的一部分超出文件尾端 |
| 417  | Expectation Failed              | 在请求头Expect中指定的预期内容无法被服务器满足，<br>或者这个服务器是一个代理服显的证据证明在当前路由的下一个节点上，<br>Expect的内容无法被满足。 |
| 418  | I'm a teapot                    | 本操作码是在1998年作为[IETF](https://zh.wikipedia.org/wiki/IETF)的传统[愚人节笑话](https://zh.wikipedia.org/wiki/惡搞RFC),<br> 在RFC 2324[超文本咖啡壶控制协议](https://zh.wikipedia.org/wiki/超文本咖啡壶控制协议)'中定义的，<br>并不需要在真实的HTTP服务器中定义。<br>当一个控制茶壶的[HTCPCP](https://zh.wikipedia.org/wiki/HTCPCP)收到BREW或POST指令要求其煮咖啡时应当回传此错误。 |
| 421  | Misdirected Request             | 该请求针对的是无法产生响应的服务器（例如因为连接重用）       |
| 422  | Unprocessable Entity            | 请求格式正确，但是由于含有[语义](https://zh.wikipedia.org/wiki/语义)错误，无法响应 |
| 423  | Locked                          | 当前资源被锁定                                               |
| 424  | Failed Dependency               | 由于之前的某个请求发生的错误，导致当前请求失败，例如PROPPATCH |
| 425  | Too Early                       | 服务器拒绝处理在Early Data中的请求，以规避可能的[重放攻击](https://zh.wikipedia.org/wiki/重放攻击) |
| 426  | Upgrade Required                | 客户端应切换到[Upgrade头字段](https://zh.wikipedia.org/w/index.php?title=Upgrade头字段&action=edit&redlink=1)中给出的不同协议，如[TLS/1.0](https://zh.wikipedia.org/wiki/传输层安全) |
| 428  | Precondition Required           | 原服务器要求该请求满足一定条件。<br>这是为了防止“未更新”问题，即客户端读取（GET）一个资源的状态，<br>更改它，并将它写（PUT）回服务器，但这期间第三方已经在服务器上<br>更改了该资源的状态，因此导致了冲突。 |
| 429  | Too Many Requests               | 用户在给定的时间内发送了太多的请求。旨在用于[网络限速](https://zh.wikipedia.org/w/index.php?title=网络限速&action=edit&redlink=1)。 |
| 431  | Request Header Fields Too Large | 服务器不愿处理请求，因为一个或多个头字段过大                 |
| 499  | client has closed connection    | 代表客户端主动断开了链接<br>一般是服务端处理时间太长了，客户端等的不耐烦了就断开了<br>还有可能是有人故意发起攻击消耗服务端资源 |
| 451  | Unavailable For Legal Reason    | 该访问因[法律](https://zh.wikipedia.org/wiki/法律)的要求而被拒绝 |

## 5XX

以 5 开头的状态码代表服务端的错误

实际工作中 500～504 比较常见

| Code | Describe                        | Note                                                         |
| ---- | ------------------------------- | ------------------------------------------------------------ |
| 500  | Internal Server Error           | 通常是源代码导致的错误                                       |
| 501  | Not Implemented                 | 服务器不支持当前请求所需的某个功能<br>无法识别请求的方法、无法支持对任何资源的请求 |
| 502  | Bad Gateway                     | 作为网关或代理服务器处理请求时，从上游服务器收到无效的响应   |
| 503  | Service Unavailable             | 临时维护或过载导致暂时不可用<br>这是临时性的<br>可以在相应头中通过Retry-After引导客户端重试 |
| 504  | Gateway Timeout                 | 作为网关或代理服务器处理请求时，未能及时从上游服务器收到响应<br>超时 |
| 505  | Http Version Not Supported      | 服务器不支持或拒绝请求中使用的HTTP版本                       |
| 506  | Variant Also Negotiates         | 代表服务器存在内部配置错误                                   |
| 507  | Insufficient Storage            | 服务器无法存储完成请求所必须的内容                           |
| 508  | Loop Detected                   | 服务器在处理请求时陷入死循环                                 |
| 510  | Not Extended                    | 获取资源所需要的策略并没有被满                               |
| 511  | Network Authentication Required | 客户端需要进行身份验证才能获得网络访问权限，旨在限制用户群访问特定网络 |

