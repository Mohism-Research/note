# intro

## 调试
PHP ini_set用来设置php.ini的值,在函数执行的时候生效, 脚本结束后,设置失效.
无需打开php.ini文件,就能修改配置,对于虚拟空间来说,很方便. 

`string ini_set(string $varname, string $newvalue);`

通过下面的设置, 可以打开错误提示: `ini_set('display_errors', 'On');`

## php 与 html 的结合
在HTML代码中有php代码
```
<?php
	$a = 'string';
?>
<html>
	<p>你好:<?php echo $a;?></p>
</html>
```

在php代码中有HTML
```
<?php
	$a = "string";
	echo "<form action='' ><input type='text' value='".$a."'></form>";
?>
```

# php script for shell
excute external program

- `string system(string $command, int $ret)` 输出并返回最后一行shell结果, 命令的执行状态保存在 $ret 中
- `string exec(string $command, array $res, int $ret)` 返回最后一行shell结果,所有结果保存到一个返回的数组$res, 状态保存在 $ret 中
- `void passthru(string $command, int $ret)` 只调用命令,把命令的运行结果原样地直接输出到标准输出设备上

```
#!/usr/bin/env php
<?php
// $v = system('ls -l', $ret); // 输出会被立刻显示出来
$v = exec('ls -l', $res, $ret);
var_dump($res);
echo $ret;
echo "\n";
echo $v;
?>
```
通过上面的示例可以看到我们可以在php中写shell 脚本, 将php与shell结合起来使用

## 命令传递参数
```
#!/usr/bin/env php
<?php
$first_name = $argv[1]; 
$last_name = $argv[2]; 
printf("Hello, %s %s! How are you today?\n", $first_name, $last_name);
?>
```
运行与输出
```
./arg.php Eric Wang
Hello, Eric Wang! How are you today?
```

**php安装好之后**  
现在可以在浏览器中打开 http://localhost 或 http://127.0.0.1, 看到 Apache 2 Test Page 页面

新建`/var/www/html/test.php`文件, 然后输入如下内容:

	<?php
		phpinfo();
	?>

再用浏览器中打开 http://localhost/test.php 或 http://127.0.0.1/test.php  你会看到很多模块都可以在 PHP5 中使用了

**服务与目录**

- #service httpd restart      /重启apache服务
- /etc/httpd/conf/httpd.conf    /apache的配置文件
- /var/log/httpd               /日志文件目录,这里的文件很容易变的很大,需要提供足够的空间
- /var/www/html                /这里是 CentOS 默认的"首页"目录

# 习惯
为了避免关于编码的不必要的麻烦, 将所有文件设置为utf-8编码, 文件名不要含有中文.\\
[编码转换工具](../../bash/gbkTOutf8.sh)

	header('Content-type: text/html; charset=utf-8');

inc 通常指的是include的简写,表示这个文件被其他(多个)文件引用,当然最好写成 a.inc.php,
这样的好处一是含义依然明确,而是避免将代码直接被访客下载(这种情况有时很严重,比如密码或者密码加密算法)  
类似的,还有这样一些常用后缀名:  
.class.php   类文件  
.ini.php     配置文件

PHP文件最后空一行的原因是什么?  
PHP FIG中提及: All PHP files MUST end with a single blank line. -- 来源
主要有两个理由:

1. 某些工具(特别是比较古老的),如果文件的末尾没有\n或\r,就会忽略最后一行.最后有一个空行,便于判断这个文件传输完整(而不是只传了一半)
1. 文件的以空行结尾是Unix的惯例

注释方式和C 语言一样, 单行用`//`, 多行用`/* .... */`

# IO
路径
```
echo getcwd()."<br/>";
echo dirname(__FILE__);
```

## 输出
在PHP中,有四种方法输出字符串:

- echo 结构可以一次输出多个值
- print() 只可以输出一个值
- printf(), sprintf()可以格式化输出, 与C 的格式一样
- `print_r()`, `var_dump()`可以输出数组,对调试很有好处

# data type
## map
A map is a type that associates values to keys. 
This type is optimized for several different uses; 
it can be treated as an **array, list (vector), hash table (an implementation of a map), dictionary, collection, stack, queue**, and probably more. 

### array
An array in PHP is actually an ordered map. So the key can either be an integer or a string. The value can be of any type.

As array values can be other arrays, trees and multidimensional arrays are also possible.
```
$array = array(
    "foo" => "bar",
    "bar" => "foo",
);

$array = [
    "foo" => "bar",
    "bar" => "foo",
];
var_dump($array);
```
[more examples of array](../demo/php/array.php)

数组遍历
```
foreach (array_expression as $value)
    statement
foreach (array_expression as $key => $value)
    statement
```
当 foreach 开始执行时,数组内部的指针会自动指向第一个单元.这意味着不需要在 foreach 循环之前调用reset().

而使用each 循环时, 在执行 each() 之后,数组指针将停留在数组中的下一个单元或者当碰到数组结尾时停留在最后一个单元.
如果要再用 each 遍历数组,必须使用 reset() 将指针重新指向第一个元素

## 字符串
- `.`: 字符串的拼接
- `chr()`: 从指定的 ASCII 值返回字符.
- `explode()`: 把字符串打散为数组.
- `str_ireplace()`: 替换字符串中的一些字符.(对大小写不敏感)
- `str_word_count()`: 计算字符串中的单词数.
- `strip_tags()`: HTML,XML 以及 PHP 的标签.
- `stripos()`: 返回字符串在另一字符串中第一次出现的位置(大小写不敏感)
- `strlen()`: 返回字符串的长度.

# Form 
```
<?php 
	if(!empty($_POST['name'])){
		echo $_POST['name'];
	}
?>
<form action="<?php $PHP_SELF; ?>" method="post">
	Enter your name: <input type="text" name="name" />
	<input type="submit" />
</form>
```

# [require and include](http://www.jb51.net/article/22467.htm)
- require: 通常放在 PHP 程序的最前面,PHP 程序在执行前,就会先读入 require 所指定引入的文件,使它变成 PHP 程序网页的一部份.常用的函数,亦可以这个方法将它引入网页中.
- include: 一般是放在流程控制的处理部分中.PHP 程序网页在读到 include 的文件时,才将它读进来.这种方式,可以把程序执行时的流程简单化.

他们两个的用途是完全一样的,不一定非得哪个放在最前面哪个放在中间.
他们最根本的区别在于错误处理的方式不一样. 

- require一个文件存在错误的话,那么程序就会中断执行了,并显示致命错误 
- include一个文件存在错误的话,那么程序不会中端,而是继续执行,并显示一个警告错误.

## upload files
[upload file demo](../demo/php/upload-file.php)

参数含义:

	$_FILES["file"]["name"] - the name of the uploaded file
	$_FILES["file"]["type"] - the type of the uploaded file
	$_FILES["file"]["size"] - the size in bytes of the uploaded file
	$_FILES["file"]["tmp_name"] - the name of the temporary copy of the file stored on the server
	$_FILES["file"]["error"] - the error code resulting from the file upload

# Database
php 使用mysql有两个方式, 一种是面向过程式的, 一种是面向对象式的, 官方推荐使用面向对象式的.

[面向对象式的demo](../demo/php/db/mysql_object-oriented.php)

[面向过程式的demo](../demo/php/db/mysql_procedure-oriented.php)

[微软的sqlserver, access, excel 连接](../demo/php/db/ms.php)

**`$count = $result->fetch_row()[0]` 错误**:   
PHP Fatal error:  Call to a member function query() on a non-object in xxx.php. 

必须使用:

	$row = $result->fetch_row();
	$count = $row[0];
不知道为什么
