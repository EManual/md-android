首先就看一下KeepAlive出现的原因吧：
当一个客户端向服务器发送http请求时，两者之间会建立一个tcp连接，然后服务器发回响应信息同时关闭连接。如果请求的的页面中含有别的资源连接，比如图片、flsah等，就会再次创建连接。KeepAlive的作用就是在第一次创建连接时，服务器会把这个tcp连接保持一段时间（服务器端会有一个keepaliveTime的最大时间，超过时间就断开连接）。这样就不会频繁的去建立tcp连接，同一次请求中的信息传递都可以使用同一个tcp连接。
#### KeepAlive的工作原理：
在HTTP1.0和HTTP1.1协议中都有对KeepAlive的支持。其中HTTP1.0需要在request中增加“Connection： keep-alive” header才能够支持，而HTTP1.1默认支持。（大家可以利用抓包工具看一下）
HTTP1.0 KeepAlive支持的数据交互流程如下：
a)Client发出request，其中该request的HTTP版本号为1.0。同是在request中包含一个header：“Connection： keep-alive”。
b)Web Server收到request中的HTTP协议为1.0及“Connection： keep-alive”就认为是一个长连接请求，其将在response的header中也增加“Connection： keep-alive”。同是不会关闭已建立的tcp连接。
c)Client收到Web Server的response中包含“Connection： keep-alive”，就认为是一个长连接，不close tcp连接。并用该tcp连接再发送request。（跳转到a)）
HTTP1.1 KeepAlive支持的数据交互流程如下：
a)Client发出request，其中该request的HTTP版本号为1.1。
b)Web Server收到request中的HTTP协议为1.1就认为是一个长连接请求，其将在response的header中也增加“Connection：keep-alive”。同是不会关闭已建立的tcp连接。
c)Client收到Web Server的response中包含“Connection： keep-alive”，就认为是一个长连接，不close tcp连接。并用该tcp连接再发送request。（跳转到a)）
#### 关于KeepAlive的分析：
现在的一些服务器都可以设置KeepAlive是否开启，以及KeepAlive的超时时间，服务器支持的KeepAlive数量（数量一般不会很大，否则会对服务器产生很大的压力）
那么我们考虑3种情况：
1、用户浏览一个网页时，除了网页本身外，还引用了多个 javascript 文件，多个 css 文件，多个图片文件，并且这些文件都在同一个 HTTP 服务器上。
2、用户浏览一个网页时，除了网页本身外，还引用一个 javascript 文件，一个图片文件。
3、用户浏览的是一个动态网页，由程序即时生成内容，并且不引用其他内容。
对于上面3中情况，1 最适合打开 KeepAlive ，2 随意，3 最适合关闭 KeepAlive
打开KeepAlive后，意味着每次用户完成全部访问后，都要保持一定时间后才关闭会关闭TCP连接，那么在关闭连接之前，必然会有一个服务器进程对应于该用户而不能处理其他用户，假设 KeepAlive 的超时时间为 10 秒种，服务器每秒处理 50 个独立用户访问，那么系统中 Apache 的总进程数就是 10 * 50 ＝ 500 个，如果一个进程占用 4M 内存，那么总共会消耗 2G 内存，所以可以看出，在这种配置中，相当消耗内存，但好处是系统只处理了 50次 TCP 的握手和关闭操作。
如果关闭 KeepAlive，如果还是每秒50个用户访问，如果用户每次连续的请求数为3个，那么 Apache 的总进程数就是 50 * 3 = 150 个，如果还是每个进程占用 4M 内存，那么总的内存消耗为 600M，这种配置能节省大量内存，但是，系统处理了 150 次 TCP 的握手和关闭的操作，因此又会多消耗一些 CPU 资源。