---
title: 细说 PHP
categories:
  - PHP
tags:
  - PHP
---

## php语言标记

可以进出"PHP模式"

共4种，其中标准类型和脚本类型总是可用的，简短类型和Asp类型需要在php配置文件中打开，因此移植性差，不推荐使用。

### 标准类型

```php 
<?php
    echo "123" 
?>
```
推荐使用的php标准标记
XML风格，如果php程序要嵌入XML或XHTML文档中，则使用此风格可保持符合标准
如果?>之后就是整个脚本文件的结束，则?>可以不加，在文件被包含时，可以避免全局函数前面有空格而导致出错

### 脚本类型

```php 
<script language="php">
    echo "123"
</script>
```
当使用的编辑器不支持标准风格标记时，可以使用此种风格

### 简短类型

```php 
<?="1234"?>
<? echo "123" ?>
```
需要更改php的配置文件php.ini：short_open_tag=Off，改为On，修改后需重启Apache
或者在编译时，加入--enable-short-tags
SGML风格，会干扰XML文档的声明，不推荐使用

### Asp类型

```php 
<% echo "123" %>
<%="123" %>
```
需要更改php的配置文件php.ini：asp_tags=Off，改为On，修改后需重启Apache
不推荐使用

## 指令分隔符：分号

语句分为两种：结构定义语句和功能执行语句，功能执行语句后面一定要加分号.

php代码的结束标记隐含表示了一个分号，因此一段php代码的最后一行可以不加分号
```php
    <?php echo "111" ?>
```

文件末尾的php代码结束标记可以不要，在使用include()和require()时更方便：
    1、在输出响应头信息时，就不会由于包含文件生成的不期望的空格而导致错误
    2、在使用输出缓冲时，就不会看到由包含文件生成的不期望的空格

## 程序注释

支持c语言`/**/`、c++的`//`、unix shell的`#`风格的注释

`#和//` 单行注释

    单行注释仅仅注释到行尾，或者，注释到php结束标记 ?>，无论先遇到哪个，都会跳出php模块，因此当注释的字符串中包含?>时注意，可能会得不到期望的结果
        <?php
        #echo "<?php echo time() ?>";?>aaa
    因此单行注释并不会注释掉?>，但却可以注释掉另一个php结束标记</script>
        <?php
        #echo "111";?>aaa<!--正常输出aaa，因注释到?>结束-->

        <?php//echo "111";?>bbb<!--正常输出bbb，因注释到?>结束-->

        <script language="php">//echo "111";</script>dd<!--无法输出dd，被注释掉了-->

`/*  */` 多行注释，遇到 `*/` 则结束，因此其中不能再包含多行注释

`/** */` 文档注释

## 在程序中使用空白

空格、tab、换行符

PHP在输出时会自动删除结束符?>后的一个换行符，此设计主要是针对在一个页面中嵌入多段PHP代码或者包含了无实质性输出的PHP文件，也因此要在?>后输出换行的话，可以加一个空格，或者在最后的一个echo/print语句中加入一个换行。

## 语言结构和函数

语言结构：
    是php的语言关键词，是php语法的一部分，就像if while一样
    可以有或没有参数
    可以有或没有返回值
    调用时可以有或没有括号

php的语言结构比函数快：
    php中，函数执行时首先要被语言解析器分解为语言结构，因此函数比语言结构多了一层解析器解析。

语言结构和函数的区别：
    速度比对应功能的函数快
    因语言结构只是语法的基本组成，不是函数，所以不能做回调函数、可变函数，但函数可以
    有些儿语言结构调用时可不使用括号，但函数必须有括号
    不能在php.ini中禁用，函数有些儿是内置，有些是扩展，则可以
    在错误处理上比较鲁棒，由于是语言关键词，所以不具备再处理的环节
        如使用isset()判断不存在的变量时并不会产生Notice错误，但函数直接使用未声明变量则会产生
            但传给函数引用变量时不会产生Notice错误
            ```php
            <?php
                function test($param){
                    echo $param;
                }

                test($a);  //notice错误
                test(&$b)  //没有
            ```

下列都是语言结构而不是函数?个：

```php
echo()
print()

eval()
list()
array()

isset()
unset()	
empty()

require()
?require_once()
include()
?include_once()

die()
exit()
return()
```

运算符

	算术运算符	+ - * / % ++ --
		%和/的除数不能为0
		%  	会将运算符两边转换为整型后再进行运算
			使用目的：整除运算 和 控制一个数的范围
		++ --	j=i++，运算符在后，表示先使用变量赋值再进行加运算
			j=++i，运算符在前，表示先进行加运算，再将结果赋值

	字符串运算符	.
		可以和4种标量进行连接

	赋值运算符	= += -= *= /= %= .=
		$a.="abc";即$a=$a."abc"; 用于字符串拼接操作

	比较运算符	> < >= <= == === !=或<> !==
		也称为条件运算符或关系运算符，结果只有一种就是布尔值
		=== 比较时不仅要求内容相同，也要求类型相同
		!== 内容或类型不同时都返回真

	逻辑运算符	& | &&或and ||或or !或not
		&和|，无论前面的条件真假，后面的条件判断都会执行
		&&和||，则会根据前面的判断结果，来确定是否需判断后面的条件

	位运算符	& | ^ ~ << >> >>>
		可以和赋值运算符=结合使用，如$a &= $b; 即 $a = $a & $b;
		^ 按位异或，相同为0，不同位1
			可以使用异或交换两个数字的值
				<?PHP
				$a=5;
				$b=3;

				//Please mind the order of these, as it's important for the outcome.

				$a^=$b;
				$b^=$a;
				$a^=$b;

				echo $a.PHP_EOL.$b;

			上面的方法也可以用来交换两个字符串的值，但字符串的长度最好相等
			不能用于数组和对象，但可以通过串行化函数serialize后再使用，然后再反串行化unserialize还原

		~ 按位取反，$c =~$a;
		>> << 右移 左移 $c = $c >> 2;
		>>> 无符号右移，无论最高的符号位是0还是1，都补0

	其他运算符	?:  ``  @ => -> :: & $
		`` 命令执行符，将其中的字符串作为系统命令执行，并返回执行结果，如$str=`ipconfig -all`;echo $str;
		@  错误控制符，用于屏蔽产生的错误和警告，如@getType();


流程控制

	一、顺序结构
	二、分支结构--条件结构--选择结构
		1、单路分支
			if(条件)
				一条代码；

			if(条件){
				代码段；
				代码段；
			}
		2、双路分支
			if(条件)
				一条代码;
			else
				一条代码;

			if(条件){
				代码段；
				代码段；
			}else{
				代码段；
				代码段；
			}
		3、多路分支
			if(条件){
				代码段；
				代码段；
			}else if{
				代码段；
				代码段；
			}else if{
				代码段；
				代码段；
			}else{
				代码段；
				代码段；
			}

			switch(变量){		//这里应该只能用整型和字符串
				case 结果1:
					代码段;
					break;
				case 结果2222:
				case 结果222:
				case 结果22:
				case 结果2:
					代码段;
					break;	//break跳出了switch，也可以利用break匹配多个条件
				case 结果3:
					代码段;
					break;
				default:	//变量没有匹配，则执行default，可选
					代码段;
			}

			判断一段范围使用else if，判断准确值匹配使用switch

		4、嵌套分支
			if(条件){

				if(条件){
					代码段；
					代码段；
				}else{
					代码段；
					代码段；
			}else{
				代码段；
				代码段；
			}
			
	三、循环结构

		计数循环for

			for(初始化;条件表达式;增量){

			增量	循环体;

			}
		
			初始化和增量也可以用多个条件，如：

			for($i=0,$j=0;$i<100 && $j<100;$i++,$j++){

			}

		条件循环while	do while

			while(表达式){
				循环体;
			}


			do{

				循环体;

			}while(表达式);   --->注意这里的分号

	四、几个和循环有关的语句

		break;		跳出当前层循环
				
		break x;	跳出x层循环,x为数字

		continue;	结束本次循环，开始下一次循环

		continue x;	结束x层循环，开始新循环

		exit;

		return;

函数

	1、function 函数名(参数列表){

		函数体；
		返回值；

	   }

	参数列表和返回值可省略
	没有返回值的函数也称为过程
	函数执行到return语句即返回了函数调用处
	php无名称空间，所以函数不能重名，包括和系统函数

	2、函数定义时也可为参数设置默认值：
	function test($a,$b=10,$c=22){

	}
	有默认值的参数调用时不传值则会使用默认值，不会发生错误：
	test(1);
	test(1,2);
	test(1,2,3);
	?无默认值的参数可称为必填值，有默认值的称为可选值，是否必填值一定不能在可选择后面？测试证明？

	3、一个参数也没有的函数，也表示可以传任意多个参数
	function test(){
		$args=func_get_args();
		echo count($args);

		//另一种方法
		for($i=0;$i<func_num_args();$i++){
			echo func_get_arg($i);
		}
	}
	test(1,2,3,4,5);

	4、定义回调函数
	function test($x,$y){

	}

	function test2($a,$b,$fun){
		return $a+$b+$fun($a,$b);
	}

	test2(1,2,"test");

系统函数使用时的分类

	1、常规函数，如
		bool copy(string source,string dest)
	2、参数带有mixed的函数，mixed表示可以传任何类型的数据
		bool chown(string filename,mixed user)
	3、参数带有&符号的函数，表示引用赋值，这个参数不能传值，只能传变量的引用
		bool arsort(array &array[,int sort_flags])
	4、带有[]的函数，表示参数可选，不传值时使用默认值
		bool arsort(array &array[,int sort_flags])
	5、参数带有...的函数，...表示可以传任意个参数
		int array_unshift(array &array,mixed var [,mixed ...])
	6、参数带有callback表示回调函数，即在调用函数时我们需要再传一个函数进去（函数名或函数名字符串），这个传进去的函数会在调用的函数中被调用
		array array_filter(array input [,callback callback])

		示例：
		$a = array(1,2,-3,4,-5,6,-7,8,9);

		function test1($n){
			if($n%2==0)
				return true;
			else
				return false;
		}
		
		function test2($n){
			if($n>0)
				return true;
			else
				return false;
		}

		print_r(array_filter($a,test1));

		print_r(array_filter($a,test2));

变量范围

	局部变量
		在函数中声明，只能在函数中调用
		函数的形参是局部变量

	全局变量
		在函数外声明，在变量声明以后的所以代码中均可以使用(即使变量是在while循环中)，包括函数和{}中
		因为php变量的声明和使用都是用$后连上变量名，所以在php中使用全局变量要通过global关键字将全局变量包含到函数中
		$a = 10;
		function test(){
			echo $a;	//函数的局部变量
			global $a;	//使用global关键字后，后面的$a才是全局变量
			echo $a+=10;
		}
	
	静态变量
		静态变量只能声明在函数和类中，不能在全局声明
		使用static声明静态变量
		静态变量在函数多次调用时共用一个，只在第一次调用时声明到内存，以后再调用则直接使用
		静态变量即不在堆内存，也不再栈内存，保存在静态代码段中	
		function test(){
			static $a=0;
			$a++;
			echo $a;
		}	

		test();
		test();

变量函数
	有一个变量$var，其值为"hello"，即$var="hello"，如果在使用变量$var时，后面加上了括号，即$var()，则php将寻找与变量值同名的函数。
	function one($a,$b){
	}

	function two($a,$b){
	}

	if(true){
		$var="one"; 	//也可写成$var=one;
	}else{
		$var="two";
	}

	echo $var(3,4);


内部函数：php可以在函数内部再声明函数，目的就是在函数的内部调用，来帮助外部函数完成一些儿子功能。

	function demo(){
	
		function fun1(){

		}

		function fun2(){

		}
	}

递归函数：在自己内部调用自己的函数

	function demo($num){
		echo $num."<br>";

		if($num>0)
			demo($num-1);
		else
			echo "------------";

		echo $num."<br>";
	}

	demo(10);

	----------------------------------

	function total($dirname,&$dirnum,&$filenum){

		$dir = opendir($dirname);
		readdir($dir);
		readdir($dir);
		while($filename=readdir($dir)){
			$newfile=$dirname."/".$filename;
			if(is_dir($newfilename)){
				total($newfile,$dirnum,$filenum);
				$dirnum++;
			}else{
				$filenum++;
			}
		}
		closedir();
	}

	$dirnum=0;
	$filenum=0;
	total("c:/appserv/www/phpmyadmin",$dirnum,$filenum);


重用函数（使用自己定义的函数库）

	require:用于静态包含
	include:用于动态包含
	require_once:相对于require，可防止重复包含一个文件，但效率相对较低
	include_once

	require "header.inc.php";

	if($className=="Action")
		include "action/".$className.".php";
	else
		include "public/".$className.".php";
		
	require "footer.inc.php";


系统指令

	php的系统指令有两种写法，如：
		include "file.inc.php";
		include("file.inc.php");

		echo "haha";
		echo("haha");

字符串处理函数特点：

	特点1、其他类型的数据用在字符串处理函数中，会自动将其转为字符串处理，如
	echo substr('abcdef';,2,3);
	echo substr(1234567,2,3);

	特点2、可以将字符串视为数组，当做字符集合来看待
	$str = "abcdefg";
	echo $str[2];	//避免和数组字符串混淆，改为下面的写法
	echo $str{2};

	echo $str{0}.$str{1};

	特点3、在PHP中所有字符串处理函数，都不是在原字符串上修改，而是返回一个新格式化后的字符串。

PHP中内置的字符串处理函数

	1、常用的字符串输出函数
		echo
			不加括号也可多个参数：echo "111","222","333";
			加括号只能一个参数：echo "111";
		print
			成功返回1，失败返回0
		die

			exit的别名

			fopen("11.php","r") or die("文件打开失败");
			echo "文件打开成功";

			exit(0); 0到254
		printf
			把字符串格式化字符串

			%%
			%b
			%c
			%d
			%f
			%o
			%x
			%s

			$str="100.123456abc";
			printf("%.2f-------%'_-20s--------%'_20s",$str,$str,$str);
		sprintf
			返回格式化的字符串

	2、常用的字符串格式化函数

		ltrim();
			删除空白，不仅仅是空格
			$str="   abc   ";
			echo strlen($str)."<br>";
			echo strlen(rtrim($str))."<br>";

			第二个参数指定要过滤的字符，即默认是空白，但自己可指定
			$str="123this is a test ...";
			echo ltrim($str,"0..9");
			echo ltrim($str,"0..9 .");

		rtrim();
		trim();
		str_pad();
			补充字符串

			参数1：往哪个字符串上补充
			参数2：补充的字符个数
			参数3：补充的字符
			参数4：补充的位置，左、右、两边

			$str="AAAA";
			echo str_pad($str,10,"-+",STR_PAD_LEFT);
			echo str_pad($str,10,"-+",STR_PAD_RIGHT);
			echo str_pad($str,10,"-+",STR_PAD_BOTH);



		strtolower();
		strtoupper();
		ucfirst();
			每句话头字母转为大写
		ucword();
			每个单词头字母转为大写



		nl2br();
			将文本的换行转换为html的<br>
		htmlentities();
		htmlspecialchars();
			将特殊字符转为html的实体，字符包括双引号、单引号、&号、左右尖括号
		stripslashes();
			将转义字符\去除并返回字符串
		strip_tags();
			从字符串中去除HTML和PHP标记，第二个参数指定你想保留的标签 



		number_format();
			参数1：要格式化的数字
			参数2：保留的小数位数
			参数3：小数点样式
			参数4：千分位样式
		strrev();
			反转字符串
		md5();
		md5_file();
			对整个文件md5加密

	3、字符串比较函数

		按字节顺序比较，即ASCII码比较
		strcmp();
			第一个参数和第二个参数比较
			相等 返回0
			大于 返回1
			小于 返回-1
		strcasecmp();

		按自然数排序
		strnatcmp();
			"1","2","10","12"
			上面为自然数
			而ascii码为
			"1","10","12","2"

正则表达式概述

	1、正则表达式是描述字符串排列模式的一种自定义语法规则
	2、可以使用字符串函数完成的任务则不要用正则表达式，一些儿复杂的操作则适合使用正则。
	3、正则表达式也称为模式表达式
	4、正则表达式是使用一些儿特殊字符，通过构建具有特定规则的模式，与输入字符串比较，再进行分割、匹配、查找、替换等工作。
	5、正则表达式和函数一起使用才起作用，否则就是个普通字符串
	6、PHP中提供了两套正则表达式函数库，一种是POSIX扩展正则表达式（以ereg_开头的函数），一种是与Perl兼容的正则表达式函数(以preg_开头的函数），推荐使用后一种
	7、正则两方面需要学习：1、正则表达式的模式如何编写  2、正则表达式的处理函数


正则表达式语法
	1、定界符号
		开始和结束符号 /
		除了字母、数字和反斜线\以外的任何字符都可以为定界符
		||
		//
		{}
		!!
		但无特殊需要，都使用正斜线//
	2、原子
		img \s
		原子是正则表达式的最基本组成单位，一个正则表达式中必须至少要包含一个原子
		正则表达式可以单独使用的字符就是原子

		1、包含所有打印和非打印字符
		2、有意义的字符如元字符，想作为原子使用，则可以用转义字符\
		3、经转义后，一些代表范围的原子：
			\d	任意一个十进制数字
			\D	任意一个非数字之外的字符
			\s	任意一个空白字符 \n\r\t\f
			\S	任意一个非空白字符
			\w	任意一个字a-zA-Z0-9_
			\W	任意一个非字
		4、使用[]自定义原子表，可以匹配[]中的任意一个原子
			[a-f4-9]
			[^a-f] ^表示取反，就是除了原子表中的原子，^必须是[]中的第一个字符
		5、.
			默认情况下，表示换行符外任意一个字符

	3、元字符
		是一种特殊字符，是用来修饰原子的，不可以单独出现
		?	前面的原子可以出现0次、1次
		+	前面的原子可以出现1次或多次
		*	前面的原子可以出现0次、1次或多次
		{}	自定义前面原子出现次数
			{m}	//m表示一个正数，表示前面原子出现m次
			{m,n}	//m小于n，表示前面原子最少出现m次，最多出现n次，包括m和n次
			{m,}	//表示前面的原子最少出现m次，最多无限
			{,n}	//表示前面的原子最多出现n次
		^	作为正则表达式的第一个字符出现，则表示必须以这个正则表达式开始	
		$	作为正则表达式的最后一个字符出现，则表示必须以这个正则表达式结尾
		|	表示 或 的关系，它的优先级是最低的，最后考虑它的功能
		\b	表示一个边界
		\B	表示一个非边界
		()	重点：
				1、作为大原子使用
					"/abc+/"
					"/(abc)+/"
				2、改变优先级，加括号可以提高优先级
					"/cat|dog/"
					"/ca(t|d)og/"
				3、作为子模式使用
					全部匹配作为一个大模式，放到数组的第一个元素中
					每个()是一个子模式，按顺序放到数组的其他元素中
				4、可以取消子模式，就将()作为大原子或改变优先级使用
					(?:)
				5、反向引用，可以在模式中直接将子模式取出，再作为正则表达式的一部分
					\n 取第n个子模式
					使用注意，正则被单引号和双引号括起来时还不相同
					双引号 "\\n"
					单引号 '\n'
					或使用下列方式
					${n}
		元字符的优先级顺序
			第一优先级：\
			第二优先级：() (?:) []
			第三优先级：* + ? {}
			第四优先级：^ $ \b
			第五优先级：|

	4、模式修正符号
		"\\模式修正符"
		只有模式修正符放定界符之外

		i	表示匹配时不区分大小写
		m	默认情况，无论字符串多少行都视为一行；加m后则每行的开头和结尾都分别匹配^和$
		s	默认情况，元字符中的"."不能表示换行符号，加s后将字符串视为单行，"."则可匹配换行符号
		x	表示模式中的空白忽略不计	
		e	正则表达式必须使用在preg_replace替换函数中时才可以使用
		U	默认为贪婪模式，加U则取消贪婪模式
			但有的语言不支持模式修正符号，另一种使用?完成 .*?  .+?
			但上述两种方式同时使用，则开启了贪婪模式

	/原子和元字符/模式修正符号 

		/为定界符，一些儿语言不需要定界符


正则表达式函数
	
	1、字符串匹配和查找

	preg_match();
		将正则表达式和字符串进行匹配，遇到第一个匹配则停止，并返回1，没有遇到匹配则返回0，发生错误返回false，一般用于验证email等
	
		参数1：正则表达式
		参数2：字符串
		参数3：匹配到的结果，为 一 维数组
		参数4：
		参数5：

		只是检测字符串中是否包含另一个字符串，可以使用下面的字符串函数：

		strstr()
			从搜到的字符串到结尾的剩余字符串
			参数1：源字符串
			参数2：要搜索字符串
		stristr()	//不区分大小写

		strpos()
			一个字符串在另一个字符串中最先出现的位置
		stripos()
			一个字符串在另一个字符串中的最后出现的位置

		substr()
			返回子字符串

			参数1：源字符串
			参数2：开始位置，大于0则从头部算起，字符串的位置从0开始
					 小于0则从尾部算起，字符串的位置从1开始
					 大于等于字符串长度，则返回false
			参数3：字符长度，不提供则取到字符串末尾
                                         为0、false或null则返回空字符串
					 小于0则表示末尾要保留几位字符串

	preg_match_all();
		将正则表达式和字符串进行匹配，会完整检测每一个匹配，并返回匹配次数，没有匹配到则返回0，发生错误返回false

		参数1：正则表达式
		参数2：字符串
		参数3：匹配到的结果，为 多 维数组
		参数4：指定匹配结果的顺序，为PREG_PATTERN_ORDER PREG_SET_ORDER，默认为前者
		参数5：

	preg_grep();

	preg_quote();

	2、字符串中的替换函数
		
	str_replace
		参数1：查找的字符串
		参数2：替换为的字符串
		参数3：源字符串
		参数4：替换的次数

		因参数为mixed类型，参数不仅可以为字符串，总结有如下用法：
		str_replace(string,string,string)	
		str_replace(array,string,string) //数组为字符串数组	
		str_replace(array,array,string)//替换时两个数组元素一一对应	
	str_ireplace	//不区分大小写

	preg_replace
		参数1：正则表达式
		参数2：替换为的字符串
		参数3：源字符串
		参数4：限制替换几次

		因参数为mixed类型，参数不仅可以为字符串，总结有如下用法：
		1、正常使用 
			preg_replace(string,string,string);
		2、将正则中匹配到的子模式使用到第二个参数中
			preg_replace(string,'\1\2',string);
		3、在第二个参数中调用函数，需要第一个参数正则表达式中使用e模式修正符			
            preg_replace('\这里是正则注意后面的e，保证了调用函数的成功\e','myfun(\2)',string);
		4、在前两个参数中都使用数组，可以一起将多个正则模式同时替换为对应字符串
            preg_replace_callback

	3、字符串的分割函数

		explode();

		preg_split();

		implode();


日期和时间

	1、UNIX时间戳
		以32位整数表示格林威治标准时间
		这个整数是从1970年1月1日0时0分0秒到现在的秒数，1970年1月1日0时0分0秒被称为计算机元年
		最主要功能是方便计算使用
		因整数的范围，时间戳处理的时间范围为1907---2038年

	2、在PHP中获取日期和时间
		time();
			获取UNIX时间戳
		getDate();
			返回数组
		getTimeOfDay();
		date_sunrise();
		date_sunset();

	3、日期和时间的格式化输出
		date(string,[timestamp]);

		date("Y-m-d H:i:s",time());
		date("Y-m-d H:i:s");

	4、将日期和时间转换为UNIX时间戳
		mktime(时,分,秒,月,日,年);

	5、修改php默认市区
		修改php配置文件date.timezone=

		date_default_timezone_set("PRC");
		date_default_timezone_set("Asia/Shanghai");
		date_default_timezone_set("Gtc/GET-8");

	6、使用微秒计算PHP脚本执行时间

		microtime(true);


