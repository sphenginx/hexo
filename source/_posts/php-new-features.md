title: PHP5.3、5.4、5.5、5.6新特性
date: 2015-12-01 20:03:01
categories: 日薪越亿
tags: [php, feature]
---
## 说明

最近公司要用lumen框架开发应用APP的接口，用到了PHP新版本，一些新特性必须要了解，且有些可以在开发时就使用，如果不使用，那么何必升级PHP版本呢，显得有些得不偿失了！
所以整理了一下 一些特性，有可能不全，待添加。

## PHP5.3 new feature

### 支持命名空间 （Namespace）

毫无疑问，命名空间是PHP5.3所带来的最重要的新特性。`在PHP5.3中，则只需要指定不同的命名空间即可，命名空间的分隔符为反斜杆\。`
Demo:
在select.php 中定义命名空间
```php
<?php    
namespace Zend\Db\Table;    
class Select {}   
```
这样即使其它命名空间下存在名为Select的类，程序在调用时也不会产生冲突。代码的可读性也有所增加。 
调用方法 
call.php
```php
<?php    
//namespace Zend\Db;    
include('select.php');    
$s = new Zend\Db\Table\Select();
$s->test();
```
### 支持延迟静态绑定（Late Static Binding）
在PHP5中，我们可以在类中通过self关键字或者__CLASS__来判断或调用当前类。但有一个问题，如果我们是在子类中调用，得到的结果将是父类。因为在继承父类的时候，静态成员就已经被绑定了。 例如： 
```php
<?php    
class A {    
    public static function who() {    
        echo __CLASS__;    
    }    
    public static function test() {    
        self::who();    
    }    
}    
class B extends A {    
    public static function who() {    
         echo __CLASS__;    
    }    
}    
B::test();    
```
以上代码输出的结果是： 
A 

这和我们的预期不同，我们原来想得到子类的相应结果。 
`PHP 5.3.0中增加了一个static关键字来引用当前类，即实现了延迟静态绑定：`
```php
<?php    
class A {    
    public static function who() {    
        echo __CLASS__;    
    }    
    public static function test() {    
        static::who(); // 这里实现了延迟的静态绑定    
    }    
}    
class B extends A {    
    public static function who() {    
         echo __CLASS__;    
    }    
}    
    
B::test();    
```
以上代码输出的结果是： 
B 

### 支持goto语句
多数计算机程序设计语言中都支持无条件转向语句goto，当程序执行到goto语句时，即转向由goto语句中的标号指出的程序位置继续执行。尽管goto语句有可能会导致程序流程不清晰，可读性减弱，但在某些情况下具有其独特的方便之处，例如中断深度嵌套的循环和 if 语句。 
```php
<?php    
goto a;    
echo 'Foo';    
a:    
echo 'Bar';    
for($i=0,$j=50; $i<100; $i++) {    
  while($j--) {    
    if($j==17) goto end;    
  }     
}    
echo "i = $i";    
end:    
echo 'j hit 17';  
```
### 支持闭包、Lambda/Anonymous函数
闭包（Closure）函数和Lambda函数的概念来自于函数编程领域。例如JavaScript 是支持闭包和 lambda 函数的最常见语言之一。 
在PHP中，我们也可以通过create_function()在代码运行时创建函数。但有一个问题：创建的函数仅在运行时才被编译，而不与其它代码同时被编译成执行码，因此我们无法使用类似APC这样的执行码缓存来提高代码执行效率。
在PHP5.3中，我们可以使用Lambda/匿名函数来定义一些临时使用（即用即弃型）的函数，以作为array_map()/array_walk()等函数的回调函数。 
```php
<?php    
echo preg_replace_callback('~-([a-z])~', function ($match) {    
    return strtoupper($match[1]);    
}, 'hello-world');    
// 输出 helloWorld    
$greet = function($name)    
{    
    printf("Hello %s\r\n", $name);    
};    
$greet('World');    
$greet('PHP');    
//...在某个类中    
$callback =      function ($quantity, $product) use ($tax, &$total)         {    
   $pricePerItem = constant(__CLASS__ . "::PRICE_" .  strtoupper($product));    
   $total += ($pricePerItem * $quantity) * ($tax + 1.0);    
 };    
array_walk($products, $callback);  
```
### 新增两个魔术方法`__callStatic()`和`__invoke()` 
PHP中原本有一个魔术方法`__call()`，当代码调用对象的某个不存在的方法时该魔术方法会被自动调用。新增的`__callStatic()`方法则只用于静态类方法。当尝试调用类中不存在的静态方法时，`__callStatic()`魔术方法将被自动调用。 
```php
<?php    
class MethodTest {    
    public function __call($name, $arguments) {    
        // 参数 $name 大小写敏感    
        echo "调用对象方法 '$name' "    
             . implode(' -- ', $arguments). "\n";    
    }    
    /**  PHP 5.3.0 以上版本中本类方法有效  */    
    public static function __callStatic($name, $arguments) {    
        // 参数 $name 大小写敏感    
        echo "调用静态方法 '$name' "    
             . implode(' -- ', $arguments). "\n";    
    }    
}    
  
$obj = new MethodTest;    
$obj->runTest('通过对象调用');    
MethodTest::runTest('静态调用');  // As of PHP 5.3.0    
```
以上代码执行后输出如下： 
```
调用对象方法'runTest' –- 通过对象调用
调用静态方法'runTest' –- 静态调用 
```
以函数形式来调用对象时，`__invoke()`方法将被自动调用。 

```php
<?php    
class MethodTest {    
    public function __call($name, $arguments) {    
        // 参数 $name 大小写敏感    
        echo "Calling object method '$name' "    
             . implode(', ', $arguments). "\n";    
    }    
    
    /**  PHP 5.3.0 以上版本中本类方法有效  */    
    public static function __callStatic($name, $arguments) {    
        // 参数 $name 大小写敏感    
        echo "Calling static method '$name' "    
             . implode(', ', $arguments). "\n";    
    }    
}    
$obj = new MethodTest;    
$obj->runTest('in object context');    
MethodTest::runTest('in static context');  // As of PHP 5.3.0  
```
### 三元运算符增加了一个快捷书写方式
原本格式为是(expr1) ? (expr2) : (expr3) 
如果expr1结果为True，则返回expr2的结果。
PHP5.3新增一种书写方式，可以省略中间部分，书写为expr1 ?: expr3 
如果expr1结果为True,则返回expr1的结果 
```php
//原格式  
$expr = $expr1 ? $expr1  :$expr2  
//新格式  
$expr = $expr1 ? : $expr2  
```
### PHP5.3中其它值得注意的改变
1.1.1.修复了大量bug 
1.1.2. PHP性能提高 
1.1.3. php.ini中可使用变量 
1.1.4. mysqlnd进入核心扩展 理论上说该扩展访问mysql速度会较之前的MySQL 和 MySQLi 扩展快（参见http://dev.mysql.com/downloads/connector/php-mysqlnd/） 
1.1.5. ext/phar、ext/intl、ext/fileinfo、ext/sqlite3和ext/enchant等扩展默认随PHP绑定发布。其中Phar可用于打包PHP程序，类似于Java中的jar机制。 
1.1.6. ereg 正则表达式函数 不再默认可用，请使用速度更快的PCRE 正则表达式函数

### 弃用功能
PHP 5.3.0 新增了两个错误等级: E_DEPRECATED 和 E_USER_DEPRECATED. 错误等级 E_DEPRECATED 被用来说明一个函数或者功能已经被弃用. E_USER_DEPRECATED 等级目的在于表明用户代码中的弃用功能, 类似于 E_USER_ERROR 和 E_USER_WARNING 等级.
下面是被弃用的 INI 指令列表. 使用下面任何指令都将导致 E_DEPRECATED 错误.
```php
define_syslog_variables
register_globals
register_long_arrays
safe_mode
magic_quotes_gpc
magic_quotes_runtime
magic_quotes_sybase
```
弃用 INI 文件中以 '#' 开头的注释.
弃用函数:
```php
call_user_method() (使用 call_user_func() 替代)
call_user_method_array() (使用 call_user_func_array() 替代)
define_syslog_variables()
dl()
ereg() (使用 preg_match() 替代)
ereg_replace() (使用 preg_replace() 替代)
eregi() (使用 preg_match() 配合 'i' 修正符替代)
eregi_replace() (使用 preg_replace() 配合 'i' 修正符替代)
set_magic_quotes_runtime() 以及它的别名函数 magic_quotes_runtime()
session_register() (使用 $_SESSION 超全部变量替代)
session_unregister() (使用 $_SESSION 超全部变量替代)
session_is_registered() (使用 $_SESSION 超全部变量替代)
set_socket_blocking() (使用 stream_set_blocking() 替代)
split() (使用 preg_split() 替代)
spliti() (使用 preg_split() 配合 'i' 修正符替代)
sql_regcase()
mysql_db_query() (使用 mysql_select_db() 和 mysql_query() 替代)
mysql_escape_string() (使用 mysql_real_escape_string() 替代)
```
废弃以字符串传递区域设置名称. 使用 LC_* 系列常量替代.
mktime() 的 is_dst 参数. 使用新的时区处理函数替代.

__更多新特性，参加官网：[http://php.net/manual/en/migration53.new-features.php](http://php.net/manual/en/migration53.new-features.php)__

## PHP 5.4 new feature
### Traits
Traits提供了一种灵活的代码重用机制，即不像interface一样只能定义方法但不能实现，又不能像class一样只能单继承。至于在实践中怎样使用，还需要深入思考。
魔术常量为`__TRAIT__`
官网的一个例子：  
```php
trait SayWorld {  
        public function sayHello() {  
                parent::sayHello();  
                echo "World!\n";  
                echo 'ID:' . $this->id . "\n";  
        }  
}  
  
class Base {  
        public function sayHello() {  
                echo 'Hello ';  
        }  
}  
  
class MyHelloWorld extends Base {  
        private $id;  
  
        public function __construct() {  
                $this->id = 123456;  
        }  
  
        use SayWorld;  
}  
  
$o = new MyHelloWorld();  
$o->sayHello();  
  
/*will output: 
Hello World! 
ID:123456 
 */  
```
### Short array syntax 数组简短语法
```php
$arr = [1,'james', 'james@fwso.cn'];  
$array = [  
　　"foo" => "bar",  
　　"bar" => "foo"  
　　];  
```
### Array dereferencing 数组值
```php
function myfunc() {  
    return array(1,'james', 'james@fwso.cn');  
}  
#以前我们需要这样：
$arr = myfunc();  
echo $arr[1];  
#在PHP5.4中这样就行了：
echo myfunc()[1]; 
```
本例要注意一个要点 [http://www.laruence.com/2011/12/19/2409.html](http://www.laruence.com/2011/12/19/2409.html)

### 实例化类
```php
class test{  
    function show(){  
		return 'test';  
    }  
}  
echo (new test())->show();  
```
### 支持 Class::{expr}() 语法
```php
foreach ([new Human("Gonzalo"), new Human("Peter")] as $human) {  
    echo $human->{'hello'}();  
}  
```

### Callable typehint
```php
function foo(callable $callback) {  
}  

foo("false"); //错误，因为false不是callable类型  
foo("printf"); //正确  
foo(function(){}); //正确  

class A {  
　　static function show() {  
    }  
}  
foo(array("A", "show")); //正确  
```
### 函数类型提示的增强
由于php是弱类型的语言，因此在php 5.0后，引入了函数类型提示的功能，其含义为对于传入函数中的参数都进行类型检查，举个例子，有如下的类:
```php
class bar {  
    function foo(bar $foo) {  
    }  
}  
//其中函数foo中的参数规定了传入的参数必须为bar类的实例，否则系统会判断出错。同样对于数组来说，也可以进行判断，比如：  
function foo(array $foo) {  
}  

foo(array(1, 2, 3)); // 正确，因为传入的是数组  
foo(123); // 不正确，传入的不是数组  
```

### PHP 5.4.0 性能大幅提升， 修复超过100个bug. 
废除了register_globals, magic_quotes以及安全模式。 
另外值得一提的是多字节支持已经默认启用了，
default_charset从ISO-8859-1已经变为UTF-8. 
默认发送“Content-Type: text/html; charset=utf-8”，
你再也不需要在HTML里写meta tag，也无需为UTF-8兼容而传送额外的header了。

删除的特性
最后，我们集中整理了几年来标记为已弃用的多个特性。这些特性包括 allow_call_time_pass_reference、define_syslog_variables、highlight.bg、register_globals、register_long_arrays、magic_quotes、safe_mode、zend.ze1_compatibility_mode、session.bug_compat42、session.bug_compat_warn 以及 y2k_compliance。
除了这些特性之外，magic_quotes 可能是最大的危险。在早期版本中，未考虑因 magic_quotes 出错导致的后果，简单编写且未采取任何举措使自身免受 SQL 注入攻击的应用程序都通过 magic_quotes 来保护。如果在升级到 PHP 5.4 时未验证已采取正确的 SQLi 保护措施，则可能导致安全漏洞。

其他改动和特性
有一种新的“可调用的”类型提示，用于某方法采用回调作为参数的情况。
`htmlspecialchars()` 和 `htmlentities()` 现在可更好地支持亚洲字符，如果未在 php.ini 文件中显式设置 PHP default_charset，这两个函数默认使用 UTF-8 而不是 ISO-8859-1。
`<?=`（精简回显语法）现在始终可用，无论 short_tags ini 设置的值为何。这应该使模板化系统创建者感到满意。
会话 ID 现在默认通过 /dev/urandom（或等效文件）中的熵生成，而不是与早期版本一样成为必须显式启用的一个选项。
mysqlnd 这一捆绑的 MySQL 原生驱动程序库现在默认用于与 MySQL 通信的各种扩展，除非在编译时通过 ./configure 被显式覆盖。
可能还有 100 个小的改动和特性。从 PHP 5.3 升级到 5.4 应该极为顺畅，但请阅读迁移指南加以确保。如果您从早期版本升级，执行的操作可能稍多一些。请查看以前的迁移指南再开始升级。

__更多新特性，参照官网：[http://php.net/manual/en/migration54.new-features.php](http://php.net/manual/en/migration54.new-features.php)__

## PHP5.5 new feature
### 放弃对Windows XP和2003 的支持
### 弃用e修饰符
e修饰符是指示preg_replace函数用来评估替换字符串作为PHP代码，而不只是仅仅做一个简单的字符串替换。不出所料，这种行为会源源不断的出现安全问题。这就是为什么在PHP5.5 中使用这个修饰符将抛出一个弃用警告。作为替代，你应该使用preg_replace_callback函数。你可以从RFC找到更多关于这个变化相应的信息。

### 新增函数和类
__boolval()__
PHP已经实现了strval、intval和floatval的函数。为了达到一致性将添加boolval函数。它完全可以作为一个布尔值计算，也可以作为一个回调函数。 
__hash_pbkdf2()__
PBKDF2全称“Password-Based Key Derivation Function 2”，正如它的名字一样，是一种从密码派生出加密密钥的算法。这就需要加密算法，也可以用于对密码哈希。更广泛的说明和用法示例
__array_column()__
```php
//从数据库获取一列，但返回是数组。  
$userNames = [];  
foreach ($users as $user) {  
    $userNames[] = $user['name'];  
}  
//以前获取数组某列值，现在如下  
$userNames = array_column($users, 'name');  
```
__intl 扩展 __
将有许多改进 intl的扩展。例如，将会有新的IntlCalendar,IntlGregorianCalendar,IntlTimeZone,IntlBreakIterator,IntlRuleBasedBreakIterator,IntlCodePointBreakIterator类。之前，我竟然不知道有这么多关于intl扩展，如果你想知道更多，我建议你去最新公告里找 Calendar和 BreakIterator。 

### 一个简单的密码散列API
```php
$password = "foo";    
// creating the hash    
$hash = password_hash($password, PASSWORD_BCRYPT);    
// verifying a password    
if (password_verify($password, $hash)) {    
    // password correct!    
} else {    
    // password wrong!    
}   
```

### 新的语言特性和增强功能。
__常量引用__
“常量引用”意味着数组可以直接操作字符串和数组字面值。举两个例子:
```php
function randomHexString($length) {    
    $str = '';    
    for ($i = 0; $i < $length; ++$i) {    
        $str .= "0123456789abcdef"[mt_rand(0, 15)]; // direct dereference of string    
    }    
}    
function randomBool() {    
    return [false, true][mt_rand(0, 1)]; // direct dereference of array    
}   
```
我不认为在实践中会使用此功能，但它使语言更加一致。请参阅 RFC。

### 调用empty()函数(和其他表达式)一起工作
目前，empty()语言构造只能用在变量，而不能在其他表达式。
在特定的代码像empty($this->getFriends())将会抛出一个错误。作为PHP5.5 这将成为有效的代码

### 获取完整类别名称
PHP5.3 中引入命名空间的别名类和命名空间短版本的功能。虽然这并不适用于字符串类名称
```php
use Some\Deeply\Nested\Namespace\FooBar;    
// does not work, because this will try to use the global `FooBar` class    
$reflection = new ReflectionClass('FooBar');   
echo FooBar::class;  
```
为了解决这个问题采用新的`FooBar::class`语法，它返回类的完整类别名称, 这里::class 等价于get_class（）

### 参数跳跃 
如果你有一个函数接受多个可选的参数，目前没有办法只改变最后一个参数，而让其他所有参数为默认值。 
RFC上的例子，如果你有一个函数如下： 
```php
function create_query($where, $order_by, $join_type='', $execute = false, $report_errors = true) { ... }  
```
那么有没有办法设置$report_errors=false，而其他两个为默认值。为了解决这个跳跃参数的问题而提出： 
```php
create_query("deleted=0", "name", default, default, false);  
```
我个人不是特别喜欢这个提议。在我的眼睛里，代码需要这个功能，只是设计不当。函数不应该有12个可选参数。 

### Getter 和 Setter 
如果你从不喜欢写这些getXYZ()和setXYZ($value)方法，那么这应该是你最受欢迎的改变。提议添加一个新的语法来定义一个属性的设置/读取: 
```php
<?php  
class TimePeriod {  
    public $seconds;  
    public $hours {  
        get { return $this->seconds / 3600; }  
        set { $this->seconds = $value * 3600; }  
    }  
}  
$timePeriod = new TimePeriod;  
$timePeriod->hours = 10;  
var_dump($timePeriod->seconds); // int(36000)  
var_dump($timePeriod->hours);   // int(10)  
```

### 生成器 yield
目前，自定义迭代器很少使用，因为它们的实现，需要大量的样板代码。生成器解决这个问题，并提供了一种简单的样板代码来创建迭代器。 
例如，你可以定义一个范围函数作为迭代器: 
```php
<?php  
function *xrange($start, $end, $step = 1) {  
    for ($i = $start; $i < $end; $i += $step) {  
        yield $i;  
    }  
}  
foreach (xrange(10, 20) as $i) {  
    // ...  
}  
```
上述xrange函数具有与内建函数相同的行为，但有一点区别：不是返回一个数组的所有值，而是返回一个迭代器动态生成的值。

### foreach 支持list()
对于“数组的数组”进行迭代，之前需要使用两个foreach，现在只需要使用foreach + list了，但是这个数组的数组中的每个数组的个数需要一样。看文档的例子一看就明白了。

```php
$array = [  
    [1, 2],  
    [3, 4],  
];  
foreach ($array as list($a, $b)) {  
    echo "A: $a; B: $b\n";  
}  
```

__更多新特性，参照官网：[http://php.net/manual/en/migration55.new-features.php](http://php.net/manual/en/migration55.new-features.php)__

## PHP5.6 new feature
### 命名空间 use 操作符开始支持函数和常量的导入
```php
namespace Name\Space {  
    const FOO = 42;  
    function f() { echo __FUNCTION__."\n"; }  
}  
namespace {  
    use const Name\Space\FOO;  
    use function Name\Space\f;  
  
    echo FOO."\n";  
    f();  
}  
```
输出
```php
42  
Name\Space\f  
```
### 使用**操作符计算乘方

### phpdbg
PHP自带了一个交互式调试器phpdbg，它是一个SAPI模块，更多信息参考 phpdbg文档 。

### php://input 可以被复用
php://input 开始支持多次打开和读取，这给处理POST数据的模块的内存占用带来了极大的改善。

### 大文件上传支持
可以上传超过2G的大文件。


__更多新特性，参照官网：[http://php.net/manual/en/migration56.new-features.php](http://php.net/manual/en/migration56.new-features.php)__