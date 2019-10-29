# x-graphql


--------

## 介绍


此项目旨在遍历graphql可用的对象和字段，通过html展示出来，再收集信息或人工判断其是否存在敏感信息泄露。

参考项目https://github.com/doyensec/graph-ql 移植的轮子，复制其生成html功能。

与原项目相比，区别在于请求的发送通过读取req.txt这个文本进行实现。造轮子的初衷在于去掉复杂的配置请求头，cookies等麻烦过程，以及当返回包与脚本所配置的data不匹配时，省去了了修改python源码的功夫。

使用方法：
通过复制burpsuite的已经带有payload的内容到req.txt，再运行命令行即可。详情见下面使用实例。

payload:
`query IntrospectionQuery{__schema{queryType{name}mutationType{name}subscriptionType{name}types{...FullType}directives{name description locations args{...InputValue}}}}fragment FullType on __Type{kind name description fields(includeDeprecated:true){name description args{...InputValue}type{...TypeRef}isDeprecated deprecationReason}inputFields{...InputValue}interfaces{...TypeRef}enumValues(includeDeprecated:true){name description isDeprecated deprecationReason}possibleTypes{...TypeRef}}fragment InputValue on __InputValue{name description type{...TypeRef}defaultValue}fragment TypeRef on __Type{kind name ofType{kind name ofType{kind name ofType{kind name ofType{kind name ofType{kind name ofType{kind name ofType{kind name}}}}}}}}`


## 使用实例

以腾讯某站点(gamer.qq.com)为例，将带有payload请求数据包复制进req.txt，如下：

```
POST /graphql/query HTTP/1.1
Host: gamer.qq.com
Connection: close
Content-Length: 787
Sec-Fetch-Mode: cors
Origin: https://gamer.qq.com
x-csrf-token: x
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/77.0.3865.120 Safari/537.36
Content-Type: application/json;charset=UTF-8
gamer-request-id: 8729407_4910291
Accept: application/json, text/plain, */*
gamer-device-id: x
Sec-Fetch-Site: same-origin
Referer: https://gamer.qq.com/
Accept-Language: zh-CN,zh;q=0.9,und;q=0.8,en;q=0.7,nl;q=0.6,zh-TW;q=0.5

{"szQuery":"query IntrospectionQuery{__schema{queryType{name}mutationType{name}subscriptionType{name}types{...FullType}directives{name description locations args{...InputValue}}}}fragment FullType on __Type{kind name description fields(includeDeprecated:true){name description args{...InputValue}type{...TypeRef}isDeprecated deprecationReason}inputFields{...InputValue}interfaces{...TypeRef}enumValues(includeDeprecated:true){name description isDeprecated deprecationReason}possibleTypes{...TypeRef}}fragment InputValue on __InputValue{name description type{...TypeRef}defaultValue}fragment TypeRef on __Type{kind name ofType{kind name ofType{kind name ofType{kind name ofType{kind name ofType{kind name ofType{kind name ofType{kind name}}}}}}}}","tk":"ba55bdb71fa516278d2229b2920137c4"}
```

再命令行执行`python main.py -u https://gamer.qq.com/graphql/query -d 'result'`
结果如下：
![image.png](https://ws1.sinaimg.cn/large/005IUN3mly1g8fjhnur6zj311i043js0.jpg)

![image.png](https://ws1.sinaimg.cn/large/005IUN3mly1g8fjiaadehj30nj1gbgqh.jpg)


## 问题
当出现如下错误时，是因为脚本硬编码写死结果取的response的data参数
![image.png](https://ws1.sinaimg.cn/large/005IUN3mly1g8fjlnjnt3j30va07ugmn.jpg)

如gamer.qq.com，取的时response的result参数，所以需要添加-d参数。

![image.png](https://ws1.sinaimg.cn/large/005IUN3mly1g8fjoelm67j31760damzh.jpg)
