---
title: xmpp深入学习
date: 2016-11-08 23:08:34
tags: xmpp
---

>xmpp是一个可扩展消息与存在协议，主要用于im
>xmpp的主词汇表有3个节点
>1、<message> 用于在实体间交换“发后不管”消息
>2、<presence> 用于传达出席状态变化并用来操纵出席订阅
>3、<iq> 提供类似get和post操作的请求相应语义
<!-- more -->

## xmpp节点

1、<stream> xmpp用来标记开始
2、<presence> 用来表明用户状态
3、<iq> 一种请求响应机制
4、<message> 用于两个jabber用户之间发送消息
    其中<body>为消息内容
    
    
```
<stream:stream>    创建一个xmpp流
<iq type=’get’>    请求elizabeth的花名册，也就是她的所有已存储的联系人列表。
<query xmlns=’jabber:iq:roster’/>
</iq>
<presence/>             通知服务器她已经在线并可以访问。  当他注意到darcy在线的时候，就会发个message节，
<message to=’darcy@pemberley.lib’
from=’ellizabaeth@longbourn.lit/ballroom’
type=’chat’>
<body>sdf</body>
</message>
<presence type=’unavailable’/>  告诉服务器，这是不可访问的要关闭
</stream:stream>
```

## 节点属性

- from
识别此节的起始jid。这里不建议在输出的节上手工设置from属性，服务器会在这些节通过时添加正确的from属性，而如果错误地设置from属性会导致服务器拒绝整个节。
客户端-服务器中，接收到的节上没有from属性，意味着该节来源于服务器自身，而在服务器-服务器中，缺少from被视为是错误。
- to
把xml节发送到to属性指定的jid。他和from相似。
客户端-服务器流中没有to属性，那么服务器将假设它是有意发给服务器自身的消息，建议在向服务器自身发消息时忽略to。
如果to属性中指定的jid是一个用户，那么服务器有可能代表用户来处理该节。
如果目的地是一个裸jid，那么服务器将处理这个节。
如果目的地是一个完整的jid，那么服务器将直接把该节路由到该用户。
- type
这个属性指定了三个节的具体类型。
三个节都可以把type设置为error，表示这个节是对已接收到的同一类型的节的错误影响，不要响应类型为error的节，避免在网络上出线反馈环节
- id
给节指定id来辅助识别响应。对于iq节，id是必须的，但是对于其他的节id是可选的。如果某个节是为了响应一个携带id属性的节而产生的，那么这个应答必须包含携带相同值得id值。
id必须具有唯一性，这样节的发送者就可以使用它来甄别响应了。最简单的做法就是让id属性值在给定的流中保持唯一性，以免歧义。


## 节

### presence

他控制并报告实体的可访问性。(包括：在线，离线；  离开，请勿打扰等。 ) 他还可以用来建立和终止向其他实体发布出席订阅。
有了出席订阅通知，我们在即时通信系统中就可以在发送消息之前知道接收者是否在线
- 普通的presence
    ```
    <presence type=‘unavailable’/>  //离线
    <presence> //在线
    <show>away</show> //只能用在presence中，值away离开，chat聊天，end不希望被打扰，xa长期离开，传达用户可访问性的性质，它请求接收者的客户端使用这个消息来更新发送者出息状态的可视化指示器
    ```
- 扩展的presence
- 出席订阅
server会自动将出席信息广播给联系人，用户也会从所有他已经进行出席订阅的联系人那里接收到出席的状态更新。
- 定向出席
    
### message
- 一端向另一端发送消息，这个节发出后就不过问了，可靠性得不到保证
- 单人聊天例子

```
<message from=‘a@netherfield.lit/frawing_room’ to=‘b@pemberley.lit’ type=‘chat’>
<body>adf</body>
</message>
```
- 多人聊天例子

```
<message from=’bennets@chat.merython.lit/mrs.bennet’ to=‘mr.bennet@longbourn.lit/study’ type=‘groupchat’>
<body>df</body>
</message>
```

- 消息类型
chat(一对一聊天)，error，normal，group chat(多人聊天)，headline(不支持或者不方便应答的自动化服务使用)。
type是可以省略的，默认为normal，但是我们应该提供一个type值
- 消息内容
<body>元素包含着该消息中人们的可读的内容。只要有不同的xml:lang属性就可以使用多个<body>。
<thread>元素用来创建线索，他的内容是一个用来区分不同线索的唯一标识符。应答节应该包含与所应答的节相同的<thread>元素。 （线索：向电子邮件一样）
在消息中，可以使用XHTML-IM来给消息提供格式化，超链接，以及富媒体。也可以使用Chat State Notifications来允许用户通告对方自己正在撰写消息或有空。（qq的正在输入）

### iq

这个节表示的时info/query（信息与查询），他给xmpp提供请求和响应机制。和http的基本工作原理相似，允许获取和设置查询。（get 和 post）
每个节都需要有一个响应，但是这个节的必须的id将用来把响应与导致该响应的请求关联起来
type：get，set请求节； result，error为响应节。

``` 
<iq from=a@longbourn.lit/garden’ type=‘get’ id=‘roster1’>
<query cmlns=‘jabber:iq:roster’/>
</iq>
<iq to=‘a@longbourn.lit/garden’ type=‘error’ id=‘roster1’>
<query xmlns=‘jabber:iq:roster’/>
<error type=‘cancel’>
<feature-not-implemented xmlns=‘urn:ietf:params:xml:ns:xmpp-stanzas’/>
</error>
</iq>
```

``` 
<iq from=a@longbourn.lit/garden’ type=‘get’ id=‘roster2’>
<query cmlns=‘jabber:iq:roster’/>
</iq>
<iq to=‘a@longbourn.lit/garden’ type=‘result’ id=‘roster2’>
<query xmlns=‘jabber:iq:roster’>
<item jid=‘b@longbourn.lit’ name=‘b’/>
<item jid=‘c@netherfield.lit’ name=‘c’/>
</query>
</iq>
```

### error
在error中，他的type类型为：cancel（不应该重试），continue（警告信息），modify（发送的数据需要一些修改才可以被接受），auth（通知实体在以某种方式进行身份验证之后重试），wait（报告server临时遇到问题，稍后重发）。


## xmpp寻址

## xmpp连接

- 连接
发送节之间需要建立xmpp连接的流
- 建立流
向服务器发送起始元素<stream:stream>，就可以打开xmpp流。服务器通过发送响应流的起始标记<stream:stream>进行响应。
服务器先发送<stream:features>元素，详细列举xmpp流中支持的所有功能。这些功能大多数与可用的加密和身份验证选项有关。
客户端发给服务器
```
<?xml version=’1.0′?>
<stream:stream xmlns=’jabber:client’
xmlns:straem=’http://ethrx.jabber.org/streams’
version=’1.0′
to=’pemberley.lit’>
```
根据数据交换信息指导pemberley.org服务器支持TLS，流压缩zlib等

- 身份验证
xmpp可以进行tls传输层安全加密，并且大多数客户端默认使用。服务器支持tls加密后，客户端就会启动tls链接，并将当前套接字升级为一个加密套接字。创建一对新的xmpp流。
xmpp身份验证使用ssl 简单身份验证与安全层协议，并支持多种身份验证机制。服务器通提供明文验证和基于md5摘要的验证，但是某些服务器还支持通过kerberos或特殊令牌来验证。
完成身份验证后，客户端必选为该链接绑定一个资源并启动一个会话。如果用户正在线路上查看xmpp流量，就会看到<bind><session>元素被发送出去。（在<iq>中）  如果客户端没有提供绑定的资源，那么服务器将为其选定一个。
当两台服务器相互连接的时候，服务器交换并验证tls证书，或者接受服务器使用某种回拨协议通过dns来验证发送者身份。
- 断开连接
当用户结束xmpp会话的时候，他们终止会话并断开连接。最有礼貌的终止会话的方式是：先发送无效出席信息，然后关闭
```
<stream:stream>元素。
通过发送最后的无效出席信息，用户的联系人能够得知该用户离开的原因。
<presence type=’unavailable’/>
</stream:stream>
```
然后，服务器终止发往客户端的流。


### node-xmpp

``` javascript
var XMPP = require('node-xmpp');  
  
var xmpp = new XMPP.Client({  
    jid : 'jid',  
    password : '123456',  
    host:'xxxx',  
    port : 5222  
});  

xmpp.on('error', function(err) {  
    console.error(err);  
});  

xmpp.on('online', function() {  
    console.log('Yes, I\'m connected!');  
    console.log('-------------------------');  
    xmpp.send('<iq id="vcard00" type="get"><vCard xmlns="vcard-temp"></vCard></iq>');  
});  

xmpp.on('stanza', function(stanza) {  
    console.log(stanza.toString());  
    if(stanza.is('iq') && stanza.attrs.type !=='error' ) {  
        ...............  
    } else if(stanza.attrs.type =='error'){  
        callback('upd vcard error!')  
    }  
})  

var data = new XMPP.Element('iq',{id:'change00',type:'set',to:name[1]})
                        .c('query',{xmlns:'jabber:iq:register'})  //创建新结点
                        .c('username').t(name[0])   //创建新结点并赋值
                        .up()   //回到上层结点
                        .tree();   //获取根节点

var tel = data.getChild('vCard').getChild('TEL');

tel.text('test');   //给已有结点赋值

var str = new XMPP.Element('iq',{type:'set', id:'vcard01'})
        .cnode(data.getChild('vCard'))  //将data的vCard结点作为子结点
        .tree();
```

