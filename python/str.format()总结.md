###str.format()总结

本文参考python文档与《高质量python》，全面总结str.format()的使用，方便今后使用与查看。

###1. 语法
在使用str.format()格式化字符串时，被格式化的字符串包含了若干'被替换字段'，'被替换字段'由{}包裹起来，这些字段将根据定义的格式被format()中的参数替换，而没有被{}包裹的部分将原样输出。  

由此可见，在使用str.format()时需要了解'被替换字段'的语法与format()的参数格式。

####被替换字段语法

replacement\_field ::=  "{" [field_name] ["!" conversion] [":" format_spec] "}"  

field\_name        ::=  arg\_name ("." attribute\_name | "[" element\_index "]")*  
arg\_name          ::=  [identifier | integer]  
attribute\_name    ::=  identifier  
element\_index     ::=  integer | index_string  
index\_string      ::=  \<any source character except "]"> +  
conversion        ::=  "r" | "s"  

format_spec ::=  [[fill]align][sign][#][0][width][,][.precision][type]  
fill        ::=  <any character>  
align       ::=  "<" | ">" | "=" | "^"  
sign        ::=  "+" | "-" | " "  
width       ::=  integer  
precision   ::=  integer  
type        ::=  "b" | "c" | "d" | "e" | "E" | "f" | "F" | "g" | "G" | "n" | "o" | "s" | "x" | "X" | "%"

上面即为被替换字段的完整语法。
被替换字段主要有三部分组成：    

 - field_name   
 指示被该字段要被format()中的哪部分代替，可以为数字或者字符串标识符，也可以是value[0] 或者 value.attr 或者 value['key']形式。
 - conversion  
 表示在format进行强制的字符串转换，感觉不怎么使用
 - format_spec  
 指示格式化的样式其中的各部分：  
 	- fill 填充符
 	- align 对齐方式 
 	 
 		\< 左对齐，是大多数对象默认的对齐方式  
 		
 		\> 右对齐，数值默认的对齐方式  
 		
 		\= 仅对数值类型有效，如果有符号的话，在符号后数值前进行填充，如-00029  
 		
 		^ 居中对齐，用空格进行填充  
 		
   - sign 符号，仅对数值类型有效  
   		
   		\+ 正数前加+，负数前加 -  
   		
   		\- 正数前不加符号，负数前加 -， 数值类型的默认方式  
   		
   		空格 证书前加空格，负数前加 -
   		
   	- \# 仅对二进制、八进制、十六进制输出格式的整数有效，当使用了'#'时，会在输出中分别添加'0b' '0o' '0x'前缀。
   	- 0 只对数值类型有效，效果等同于fill指定为0同时align为=
   	- width 指定了被替换字段输出的最小宽度
   	- , 对数值类型有效，当数值超过1000时使用','分割
   	- .precision 表示精度，只对浮点型数值与字符串有效。如果是浮点数，则表示小数点后的位数；如果是字符串则表示最大字段宽度。
   	- type 转换类型


###2. 优点
在格式化字符串时要尽量使用str.format()方法，相比较'%',str.format()的优点为：
  
1. format方式在使用上比%更加灵活，参数顺序与格式化顺序不必完全相同：  
'The price of {1} is {0}'.format(134, 'apple')
2. format格式可以方便地作为参数传递  
weather=[('Mon','rain'),('Tue','sunny'),('Wed','sunny'),('Thur','snow')]  
formatter='Weather of {0[0]} is {0[1]}'.format  
for item in map(formatter, weather):  
	print items
3. format可以很方便地格式化元组  
students=('xiaoming', 'xiaogong', 'xiaomei')  
print 'The students are {}'.format(students) 
