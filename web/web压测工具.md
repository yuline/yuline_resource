##WEB压力测试小结
常见的web压力测试工具有ab、Webbench、http\_load、Siege，为了学以致用，本文只对常用的http_load进行详细总结与记录，同时也会捎带着介绍ab。  

###http_load
常言道：http_load 以并行复用的方式运行，用以测试web服务器的吞吐量与负载。但是它不同于大多数压力测试工具，它可以以一个单一的进程运行，一般不会把客户机搞死。可以可以测试HTTPS类的网站请求 。

####1. 安装  
从 [官方网站](http://www.acme.com/software/http_load/)下载源码包  
解压后， make && make install  
其实不需要make intall , make 后直接在本目录下使用即可。  

####2. 使用方法  
`usage:  ./http\_load [-checksum] [-throttle] [-proxy host:port] [-verbose] [-timeout secs] [-sip sip\_file]  
            -parallel N | -rate N [-jitter]  
            -fetches N | -seconds N  
            url_file  `  
            
 参数说明：  
`-parallel` 简写 `-p` ：含义是并发的用户进程数   
`-fetches` 简写 `-f` ：含义是总计的访问次数   
`-rate`    简写 `-r` ：含义是每秒的访问频率   
`-seconds` 简写 `-s` ：含义是总计的访问时间  
从help文档中可以看到 官方建议使用时 `-parallel` 与 `-rate` 二者用一， `-fetches` 与 `-seconds` 二者选一。 通常参数组合：-p –f；-r -s
  
 urls.txt保存要访问的url列表，url 是你要访问的网址名，参数可以是单个的网址也可以是包含网址的文件。 文件格式是每行一个URL，URL最好超过50－100个测试效果比较好. 文件格式如下   
http://iceskysl.1sters.com/?action=show&id=336   
http://iceskysl.1sters.com/?action=show&id=335   
http://iceskysl.1sters.com/?action=show&id=332       
 
 以下纯属个人推测，需事实验证：  
 `-sip`可以模拟源ip地址  
 `-rate` 与 `-jitter` 结合使用可以实现访问频率的抖动，但是其跳变的剧烈程度未知
 
####3. 实际使用方法示例与结果分析   
`http_load -rate 5 -seconds 10 urls.txt `  
命令解释: 执行一个持续时间为10秒的测试，每秒的访问频率为5次。 

 49 fetches, 2 max parallel, 289884 bytes, in 10.0148 seconds   
5916 mean bytes/connection   
4.89274 fetches/sec, 28945.5 bytes/sec (重要性能指标吞吐量)   
msecs/connect: 28.8932 mean, 44.243 max, 24.488 min(重要指标响应时间)   
msecs/first-response: 63.5362 mean, 81.624 max, 57.803 min   
HTTP response codes:   
code 200 — 49   

- 49 fetches, 2 max parallel, 289884 bytes, in 10.0148 seconds 
说明在上面的测试中运行了49个请求，最大的并发进程数是2，总计传输的数据是289884bytes，运行的时间是10.0148秒 

- 5916 mean bytes/connection 
说明每一连接平均传输的数据量289884/49=5916 

- 4.89274 fetches/sec, 28945.5 bytes/sec (吞吐量: 单位时间完成请求数) 
说明每秒的响应请求为4.89274，每秒传递的数据为28945.5 bytes/sec 
这个值得是根据 49 fetches / 10.0148 seconds 秒计算出来的 

- msecs/connect: 28.8932 mean, 44.243 max, 24.488 min (响应时间: 每次请求需要的时间, 平均, 最大, 最小) 
说明每连接的平均响应时间是28.8932 msecs，最大的响应时间44.243 msecs，最小的响应时间24.488 msecs 
- msecs/first-response: 63.5362 mean, 81.624 max, 57.803 min 

**关于 msecs/connect 与  msecs/connect 这两个参数的意义网上有两种不同的说法，以上是第一种，但是我不太赞同，还有另外一种：msecs/connect 表示每个链接的链接时间，msecs/first-response 才表示每次请求的响应时间。个人感觉第二种比较靠谱**  


- HTTP response codes: code 200 — 49 
说明打开响应页面的类型，如果403的类型过多，那可能要注意是否系统遇到了瓶颈。  
 
有资料显示http_load还会有其他两个显示项：

- 23088 timeouts//运行中有23088个请求超时
- 27503 bad byte counts//同一个http请求，不一样的结果的个数。
这两个结果项主要反映了发生错误的请求个数，http_load默认的超时时间为**10s**，这里的超时可以自己定义，但必须是int整数，不能是浮点数。

 
特殊说明：这里，我们一般会关注到的指标是fetches/sec、msecs/connect <**msecs/first-response**>他们分别对应的常用性能指标参数 Qps-每秒响应用户数和response time，每连接响应用户时间。测试的结果主要也是看这两个值。 


####4. 区分适用场合
在使用http_load做压测前需要理清自己的测试需求，然后再确定需要的参数组合。  


通常情况下，我们再搭建了一个新的环境后，需要测试其合适与最大的吞吐量（qps），并且分别对比相应地处理时间，这一般是我们做压测的基本目的。而我们的压测工具模拟的两个最重要的参数就是 **请求频率**（每秒钟发送多少个请求）与**持续时间**（如果持续时间短的话可能有时不能将问题暴露出来） ，所以最基本的参数就是**-rate** 与**-seconds**,在测试过程中可以不断增加rate参数值来看服务器响应吞吐量与响应时间的变化，可以预估出在可接受的响应时间里，服务器能够接受的最大请求频率。**这个参数组合重点在于看服务器能否抗住持续时间段内的平均请求数。**

还有一种用法是使用 -fetches 参数规定总的请求数量以取代-seconds参数，同时不指定-rate，相反使用-parallel 参数指定http_load开启的进程数，在指定大量的**fetches**后这样可以逐渐增大**parallel**以**测试网站每秒所能承受的平均访问量(吞吐量)** 


###ab
ab的全称是ApacheBench，是 Apache 附带的一个小工具，专门用于 HTTP Server 的benchmark testing，可以同时模拟多个并发请求，多见用于静态压力测试。ab工具虽然很小，但其功能总不算简单，其支持用户认证测试、cookie处理、代理测试；同样，其带的参数也并不算少，但经常用到的只有 -n和-c 两个参数。下面结合一个示例，介绍下其输出中每个参数的意思。

#####1.实例与结果分析
[root@gateway ~]#ab -n 10 -c 10 http://www.google.com/  
/\*ab -n 10 -c 10 http://www.google.com/。这个命令的意思是启动 ab ，向 www.google.com 发送10个请求(-n 10) ，并每次发送10个请求(-c 10)——也就是说一次都发过去了。**其中-n代表请求数，-c代表并发数** \*/
  
Benchmarking www.google.com (be patient).....done  
Server Software:        GWS/2.1  
Server Hostname:        www.google.com  
Server Port:            80  
Document Path:          /  
Document Length:        230 bytes  
Concurrency Level:      10  

/\*整个测试持续的时间\*/  
Time taken for tests:   3.234651 seconds 
 
/\*完成的请求数量\*/  
Complete requests:      10  

/\*失败的请求数量\*/  
Failed requests:        0  

Write errors:           0  
Non-2xx responses:      10  
Keep-Alive requests:    10  

/\*整个场景中的网络传输量\*/  
Total transferred:      6020 bytes 
 
/\*整个场景中的HTML内容传输量\*/   
HTML transferred:       2300 bytes 
 
/\*大家最关心的指标之一，相当于 LR 中的 每秒事务数 ，后面括号中的 mean 表示这是一个平均值 **吞吐量**\*/  
Requests per second:    3.09 [#/sec] (mean)  

/\*大家最关心的指标之二，相当于 LR 中的 平均事务响应时间 ，后面括号中的 mean 表示这是一个平均值\*/  
Time per request:       3234.651 [ms] (mean)  **客户端感觉到的平均处理时长** 
Time per request:       323.465 [ms] (mean, across all concurrent requests) **在服务器端计算的每个请求的平均处理时长，要除以并发数**  

/\*平均每秒网络上的流量，可以帮助排除是否存在网络流量过大导致响应时间延长的问题\*/  
Transfer rate:          1.55 [Kbytes/sec] received  

/\*网络上消耗的时间的分解\*/  
Connection Times (ms)  
              min  mean[+/-sd] median   max  
Connect:       20  318 926.1     30    2954  
Processing:    40 2160 1462.0   3034    3154  
Waiting:       40 2160 1462.0   3034    3154  
Total:         60 2479 1276.4   3064    3184  

/*下面的内容为整个场景中所有请求的响应情况。在场景中每个请求都有一个响应时间，其中 50％ 的用户响应时间小于 3064 毫秒，60 ％ 的用户响应时间小于 3094 毫秒，最大的响应时间小于 3184 毫秒\*/  
Percentage of the requests served within a certain time (ms)  
  50%   3064  
  66%   3094  
  75%   3124  
  80%   3154  
  90%   3184  
  95%   3184  
  98%   3184  
  99%   3184  
 100%   3184 (longest request)
 
 
#####2.ab的参数详细解释  
普通的测试，使用-c -n参数配合就可以完成任务  
格式： ./ab [options] [http://]hostname[:port]/path  
参数：  
>-n 测试的总请求数 。默认时，仅执行一个请求  
-c 一次并发请求个数。默认是一次一个。  
-H 添加请求头，例如 ‘Accept-Encoding: gzip’，以gzip方式请求。  
-t 测试所进行的最大秒数。其内部隐含值是-n 50000。它可以使对服务器的测试限制在一个固定的总时间以内。默认时，没有时间限制。  
-p 包含了需要POST的数据的文件.  
-T POST数据所使用的Content-type头信息。  
-v 设置显示信息的详细程度 – 4或更大值会显示头信息， 3或更大值可以显示响应代码(404, 200等), 2或更大值可以显示警告和其他信息。 -V 显示版本号并退出。  
-w 以HTML表的格式输出结果。默认时，它是白色背景的两列宽度的一张表。  
-i 执行HEAD请求，而不是GET。  
-C -C cookie-name=value 对请求附加一个Cookie:行。 其典型形式是name=value的一个参数对。此参数可以重复。
       
            
