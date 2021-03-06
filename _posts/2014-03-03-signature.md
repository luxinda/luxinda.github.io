---
layout: post
title: 数字签名
tags: 数字签名 computer
categories: common
published: false
---

`1`.鲍勃有两把钥匙，一把是公钥，另一把是私钥

![](http://image.beekka.com/blog/201108/bg2011080901.png)

`2`.鲍勃把公钥送给他的朋友们：帕蒂、道格、苏珊每人一把

![](http://image.beekka.com/blog/201108/bg2011080902.png)

`3`.苏珊要给鲍勃写一封保密的信。她写完后用鲍勃的`公钥加密`，就可以达到`保密`的效果

![](http://image.beekka.com/blog/201108/bg2011080903.png)

`4`.鲍勃收信后，用`私钥解密`，就看到了信件内容。只要鲍勃的私钥不泄露，这封信就是安全的。（公钥私钥成对生成，用一个加密后必须用另一个才能解密）

![](http://image.beekka.com/blog/201108/bg2011080904.png)

`5`.鲍勃给苏珊回信，他写完后先用Hash函数，生成信件的摘要（digest）

![](http://image.beekka.com/blog/201108/bg2011080905.png)

`6`.然后，鲍勃使用私钥，对这个摘要加密，生成"`数字签名`"（signature）

![](http://image.beekka.com/blog/201108/bg2011080906.png)

`7`.鲍勃将这个签名，附在信件下面，一起发给苏珊。

![](http://image.beekka.com/blog/201108/bg2011080907.png)

`8`.苏珊收信后，取下数字签名，用鲍勃的公钥解密，得到信件的摘要

![](http://image.beekka.com/blog/201108/bg2011080908.png)

`9`.苏珊再对信件本身使用Hash函数，将得到的结果，与上一步得到的摘要进行对比。如果两者一致，就证明这封信确实是鲍勃发出的，且未被篡改过

![](http://image.beekka.com/blog/201108/bg2011080909.png)

`10`.道格想`欺骗`苏珊，他偷偷使用了苏珊的电脑，用自己的公钥换走了鲍勃的公钥。此时，苏珊实际拥有的是道格的公钥，但是还以为这是鲍勃的公钥。因此，道格就可以用自己的私钥做成"数字签名"，写信给苏珊。苏珊用公钥验证信件后，就会以为信件是鲍勃发过来的。

![](http://image.beekka.com/blog/201108/bg2011080910.png)

`11`.苏珊发现自己无法确定公钥是否真的属于鲍勃。她想到了一个办法，要求鲍勃去找"证书中心"（certificate authority，简称CA），为公钥做认证。证书中心用自己的私钥，对鲍勃的公钥和一些相关信息一起加密，生成"数字证书"（Digital Certificate）。

![](http://image.beekka.com/blog/201108/bg2011080911.png)

`12`.鲍勃拿到数字证书以后，再给苏珊写信，只要在签名的同时，再附上数字证书就行了。

![](http://image.beekka.com/blog/201108/bg2011080912.png)

>此时有了第三方信用机构的介入，经CA认证的证书中会包含证书申请人的详细信息(包括公钥)。该证书必须用CA的公钥（官网公开）解开，因此是无法伪造的，证书解开后将会得知证书的申请人并获得用于验证签名的真实公钥。如果信件、签名、证书任一个被篡改都会验证失败，极端情况下，三者被同时篡改，苏珊也可以通过阅读数字证书中的申请人信息来判断真伪。

![](http://upload.wikimedia.org/wikipedia/commons/thumb/2/2b/Digital_Signature_diagram.svg/1024px-Digital_Signature_diagram.svg.png)

<br>

#HTTPS

`1`.首先，客户端向服务器发出加密请求

![](http://image.beekka.com/blog/201108/bg2011080915.png)

`2`.服务器用自己的`私钥加密(签名)`网页以后，连同本身的数字证书，一起发送给客户端。

![](http://image.beekka.com/blog/201108/bg2011080916.png)

`3`.客户端（浏览器）的"证书管理器"，有"受信任的根证书颁发机构"列表，里面保存着所有受信任的第三方信用机构(与上文中的CA作用相同)，客户端会根据这张列表，查看解开数字证书的公钥是否在列表之内。

![](http://image.beekka.com/blog/201108/bg2011080917.png)

`4`.如果数字证书记载的网址，与你正在浏览的网址不一致，就说明这张证书可能被冒用，或者浏览器的信任列表中不存在能够解开数字证书的公钥，浏览器会对这两种情况出警告。

![](http://image.beekka.com/blog/201108/bg2011080918.png)

`5`.如果这张数字证书不是由受信任的机构颁发的，浏览器会发出另一种警告。

![](http://image.beekka.com/blog/201108/bg2011080919.jpg)

`6`.如果数字证书是可靠的，客户端就可以使用证书中的服务器公钥，对信息进行加密，然后与服务器交换加密信息。

![](http://image.beekka.com/blog/201108/bg2011080920.png)