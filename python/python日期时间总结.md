##python的日期与时间处理
python标准库中含有三个模块用于管理日期与时间值  
 
 - time 底层接口，提供了获取时钟时间和处理器运行时间的功能以及基本解析和字符串格式化工具
 - datetime 为时间以及日期提供了更高层的接口，支持算术，比较与时区设置
 - calendar

###1.  time
**时间戳与处理器时间**  

* time.time()  获得从纪元开始到现在的秒数,即timestamps  
* time.ctime([timestamps]) 当前时刻的字符串表示，可以使用一个timestamps参数
* time.clock() 返回该线程处理器消耗时间  
* time.sleep(n) 程序暂停n秒  

**时间组成——struct_time对象及其与时间戳的转换**  

* time.localtime([seconds]) 无参调用返回一个表示当前时区时间的time.struct\_time对象。  
time.struct\_time的各个属性tm\_year, tm\_mon, tm\_mday,tm\_wday(星期几),tm\_yday(一年中的天数)……表示时间的各个组成部分    
* time.gmtime([seconds])无参时返回当前的UTC时间（比东八区时间早了8小时）  
* time.mktime(tuple) 将time.struct_time对象转换为时间戳  

**解析格式化——struct_time与时间字符串表示之间的转换**

* strptime(string, format) -> struct_time
* strftime(format[, struct_time]) -> string  
	
	具体格式字符串参见官方文档,而且在linux中可以查看man date，python 时间与日期的format与其相通
	

###2. datetime
datetime包是基于time包的一个高级包，datetime可以理解为由两部分组成——date与time。date指年月日星期等信息，相当于***日历***；time指时分秒毫秒等信息，相当于***手表***。两者可以分别使用**datetime.date**类和**datetime.time**类来使用，也可以使用**datetime.datetime**类将两者结合在一起  
  
  

1. time([hour[, minute[, second[, microsecond[, tzinfo]]]]]) --> a time object  
	- 时间值使用time类表示，time实例包含hour,minute,second,microsecond属性。
	- 实例方法isoformat()可以返回time对象的'xx:xx:xx'字符串表示
	- 实例方法replace()可以替换time对象的某些属性以返回一个新time对象
2. date(year, month, day) --> date object
	- 日期使用date类表示，date实例包含year、month、day属性
	- 实例方法ctime()可以返回一个struct_time对象
	- 类方法today()可以创建一个表示当前日期的date实例，
	- 类方法fromtimestamp()可以由给定的timestamp参数创建date实例
	- 实例方法replace()可以替换date对象的某些属性以返回一个新date对象
3. datetime(year, month, day[, hour[, minute[, second[, microsecond[,tzinfo]]]]])  
	- 这是重点，基本囊括了上述两个对象的功能及其方法函数
	- 创建datetime实例：  
			datetime.now()  
			datetime.today()  
			datetime.replace()  
	- 格式化与解析：  
			实例方法strftime() datetime object-->str    
			类方法strptime()	str-->datetime object
			
	- datetime实例支持直接进行比较：dt1 > dt2
	- 时间运算需要借助timedelta类
4. timedelta
	* 两个datetime对象加减可以获得一个timedelta对象，一个datetime对象加减一个timedelta对象可以获得一个新的datetime对象。
	* 使用关键字参数进行初始化：
		datetime.timedelta(microseconds=1)  
		datetime.timedelta(millseconds=1)  
		datetime.timedelta(second=1)  
		datetime.timedelta(minutes=1)  
		datetime.timedelta(hours=1)
		datetime.timedelta(days=1)
		datetime.timedelta(weeks=1)  

