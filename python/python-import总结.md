##python project中的import module问题
本次要探讨的是一个有关Python Package下的Module import的问题，这是我在进行一个Python工程源码组织设计时遇到的。一般来说，我们的工程代码组织形式如下：  
    
        py-proj/  
	         main.py  
            pkg1/  
                __init__.py
                mod1.py
            pkg2/
                __init__.py
                mod2.py
            test/
                __init__.py
                testmod1.py
                testmod2.py

 工程的dev需求如下：
   
  1. 执行main.py(其中import了各个pkg的module)
  2. 能够单独执行pkg下的某个module
  3. 兄弟pkg间可以相互import module
  4. 能够单独执行test下的某个module的test用例
  5. 能够一次执行test下的所有module的test用例  
        
基于工程的这些dev需求，我们来看一下module import方式的选择。  
Python自2.5版本之后支持两种package import方式：`absolute import`和`relative import`。不过Guido van Rossum在PEP 8中明确建议采用`absolute import`，理由是：more portable和more readable。经过试验，我个人觉得Guido van Rossum的建议是十分中肯的。relative import在不同版本间的支持语义有差别，且在理解方面显得有些复杂。虽然relative import能解决一些问题，但感觉投入产出比不高。我们来看看package absolute import能否满足我们的所有工程dev需求。
        
**1. 执行main.py**     

无论当前工作目录（current working directory)是哪个目录，**一旦执行main.py，Python就会自动将main.py所在的目录添加到sys.path中去，作为一个 module search path的entry**。这样只要工程下的**文件都**采用了absolute import，Python就可以正确找到并import正确的module。

**2. 单独执行某pkg下的某个module**  

我们在dev时有这样的需求：单独执行某个正在编写的module的代码以获得一些执行结果的反馈。不过，以上面例子中的代码结构为例，如果我们进入到 pkg1目录下执行python mod1.py，一旦mod1.py引用了pkg2.mod2，你就会收到如下错误（前提是你使用了absolute import,**所以说project中所有文件的absolute import都是以project的入口python文件为标准，即只有一个absolute**）：

```     
	$ python mod1.py  
		Traceback (most recent call last):  
			File "mod1.py", line 2, in <module>  
				import pkg2.mod2  
		ImportError: No module named pkg2.mod2  
```

  因为Python只是将pkg1这个路径加入到module search path中了，这个路径下显然没有pkg2/mod2.py。不过我们可以通过在工程top-level路径下执行"python -m pkg1.mod1"来单独执行mod1的代码，这样absolute import依然生效，不会导致import error。             

**3. 兄弟pkg间可以相互import module**  

这个与上面的执行方法类似，只要在top-level下通过python -m执行，那么无论pkg层次多深，无论有多少兄弟package，Python总是可以找到正确的module并导入。  

**4. 单独执行test下的某个module的test用例**  

这有些类似于引用兄弟package的情况。我们通过在顶层路径下执行python -m test.testmod1即可达到此目的。    

**5. 一次执行test下的所有module的test用例**

较新的Python版本已经可以自动发现测试用例并执行。我们通过在top-level目录执行python -m unittest discover test即可执行test目录下所有符合unittest包约定要求的单元测试用例文件。在执行这个命令时，Python会将top-level路径以及 test路径都加入到module search path中。  
综上，Absolute import可以满足所有需求。虽然有时候absolute import从代码上会看起来有些冗长(通过from … import …能有所缓解)，但在语义理解的简单性和可读性上的优势让我更加倾向于这种方式。另外通常情况下我们是无需重新设置PYTHONPATH，也用不 到.pth文件，更不需在代码里修改sys.path来改变Python的module search path的。  
注：以上测试均在Ubuntu 12.04 LTS Python 2.7.3版本下测试通过。


###禹霖总结：  
在python project中有一个top-level位置，即project目录，比如
	
	project/
   	 ﻿main.py
    ﻿mod/
    ﻿    sub﻿mod1.py
    ﻿    ﻿……
    ﻿test/
    ﻿    ﻿test1.py
    ﻿    ﻿……
则project/ 即是top level

在project中的所有python文件都可以 以top level为基准进行import 。 不如在test1.py中想要导入submod1.py模块： from mod import submod1

在实际编程中可能会对编写的submod1.py进行测试，此时需要回到top-level位置，然后执行 python -m submod1.py