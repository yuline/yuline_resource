##python re模块学习总结
python将与正则表达式相关的功能都封装到了re模块中

**1.基本应用**  
 
re.search(pattern, text)&ensp;&ensp;# 在test中搜索匹配pattern的段，返回一个match对象挥着None。
 
从match对象中能够取得有关匹配性质的信息，包括原输入字符串，使用的正则表达式，以及模式在原字符串中的位置。  
match.start()  
match.end()  
match.re.pattern  
match.string  

re.compile()编译正则表达式，返回一个RegexObject,可以直接调用re模块的函数，函数参数中可以忽略正则表达式字符串。  

re.search()  只会返回第一个匹配项，如果想返回所有匹配项，则需要re.findall() 与 re.finditer()  
re.findall() &nbsp; 返回所有匹配的字符串，当pattern中含有子表达式时会返回子表达式的匹配值，但整体匹配值丢失  
re.finditer() &nbsp; 返回的是match 对象列表，这个很有用  

re.match() &nbsp; 使用match()默认在pattern的最开始出增加了^,即模式必须出现在字符串的最前面。 

**2.子表达式**  

当在pattern中包含子表达式时，如果想从match对象中获得子表达式匹配的内容，可以使用match.groups() 与 match.group(n)

可以对子表达式命名，又称命名组，这样可以更容易的修改模式，且不必同时修改使用了匹配结果的代码:  

- 正则表达式的命名组语法: (?P<name>subpattern)  
- 命名组相关的函数:  match.groupdict() &nbsp; #groups()包含了groupdict()的结果

当pattern包含嵌套子表达式时，即子表达式中含有其他子表达式时，groups()会根据子表达式中'('出现的顺序依次获得子表达式的匹配值。

**3.搜索选项**

re.search()、re.match()、 re.compile()、re.findall()等函数都接受一个flag参数，用于指定搜索选项。搜索选项的指定通过re模块常量的位或'|'运算。  

- re.IGNORECASE: 忽略大小写  
- re.MULTILINE: 多行模式，可以'^'与'$'匹配文本每一行的行首与行尾。默认情况下，python把被匹配的字符串作为一个整体，如果模式中含有^/$，则匹配整个字符串的开始与结束。使用多行模式后，则表示行首与行尾。  
- re.DOTALL: 使'.'可以匹配换行符。
>注意：在利用re.compile()时，只能在compile()中使用这些标志。已编译正则表达式的相关方法没有flag参数。  
  
除了上面使用模块常量的方法外，Python还可以将标志加入正则表达式字符串中。方法：将(?x)加到pattern的开头处  
- x的值：i, m, s 分别对应上述常量，可以将多个结合在一起使用：(?im)  

**4.自引用表达式**  

也称作反向引用。  

- 在含有子表达式的pattern中 可以在子表达式后引用前面子表达式的结果。方法： 在pattern中使用 '\num'，表示引用第num个子表达式的匹配值。 
- 当之前的子表达式是命名组并且想利用命名时： '(?P=name)'  
- 反向引用还有另外一种作用：根据前一组是否匹配来选择不同的模式,(?(id)yes-expression|no-expression)，其中id是组名或编号，yes-expression是组有值时使用的模式，no-expression是组没有值时使用的模式。感觉这种方式隐含了前面的子表达可以没有匹配内容，这通过通配符 * 与 ? 实现。

**5.替换与拆分**

- re.sub(pattern, repl, string, count=0, flag=0)   &nbsp;#
repl表示替换后的字符串，可以使用向后引用以利用匹配的子表达式内容，在python中使用 \num 与 \g\<name\> 的方式。 count表示替换的次数，默认全部替换。 
- re.subn() #与 re.sub()类似，但会返回完成的替换次数。  
- re.split(pattern,string,maxsplit=0,flag=0) &nbsp;&nbsp;#补充通过将pattern整体包括在小括号里，re.split()可以取得分割字符串，非常类似于str.partition()
