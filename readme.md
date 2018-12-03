### ENPHP Framework是一个轻量级，开包即用的PHP框架。

特别适合中小型网站的开发建设，自带数据表验证，多数据库分离支持，常用的库文件。
以简化那些80%重复功能为目标打造出此框架，如果您厌烦那些重量级框架，请不妨试试ENPHP Framework。

### 版本依赖

> 版本 PHP7+

> mb_string扩展

> GD2扩展

### 文档目录索引

&nbsp;|&nbsp;|&nbsp;|&nbsp;
-----|-----|-----|-----
[入口文件配置](#入口文件配置)       | [常量设置](#常量设置)       | [数据库配置](#数据库配置)  | [自定义配置数据字典](#自定义配置数据字典)
[全局变量数组](#全局变量数组)      | [数据库基本操作](#数据库基本操作)     | [Model数据模型](#Model数据模型)      | [Model数据验证](#Model数据验证)
[Controller控制器](#Controller控制器) | [View视图](#View视图)   | [Helper帮助函数](#Helper帮助函数)    | [Input输入](#Input输入)
[Output输出](#Output输出)       | [Session会话](#Session会话)   | [Cookie管理](#Cookie管理)       |[Lang多语言配置](#Lang多语言配置)
[Redis缓存](#Redis缓存)        | [Security安全](#Security安全)  | [Upload上传文件](#Upload上传文件)  |[Html标签生成](#Html标签生成)
[Grid表格生成](#Grid表格生成)  |[Image图片修饰](#Image图片修饰)  |[Smtp发送邮件](#Smtp发送邮件)   |[Captcha验证码生成](#Captcha验证码生成)
[应用程序目录布局说明](#应用程序目录布局说明) | | | 


文档内容
====

### 入口文件配置

> 入口文件一般是网站的根目录index.php文件，有几个重要的常量配置。

 设置运行环境变量，三个值分别为测试环境，产品环境，开发环境。
```php
// test,production,development
define('ENVIRONMENT', 'development');
```

您开发的应用程序目录常量APP_DIR设置。
```php
define('APP_DIR', realpath('app_dir') . DIRECTORY_SEPARATOR);
```

框架系统文件目录常量，可以存放到其他地方，以便共用和升级。
```php
define('SYS_DIR', realpath('system_dir') . DIRECTORY_SEPARATOR);
```

设置controller模块常量，模块必须是APP_DIR目录下module文件夹的子目录。
```php
define('MODULE', 'www');

```
设置模板目录常量，模板必须是APP_DIR目录下template文件夹的子目录。
```php
define('TEMPLATE', 'www');
```

### 常量设置

> 常量文件位置在APP_DIR/config/下面三个子目录test,production,development中的constans.php文件分别按环境设置。

地址路由配置,以/index.php?c=main&a=index为例子。

c代表控制器类名字，默认控制器为Main。

a代表action方法名称，默认action为index。

你可以自定义设置这些值。

```php
define('DEFAULT_CONTROLLER', 'main');
define('DEFAULT_ACTION', 'index');
define('CONTROLLER_KEY_NAME', 'c');
define('ACTION_KEY_NAME', 'a');
```

输出字符编码设置，以便$this->output->view()和$this->output->json()输出
```php
define('CHARSET', 'utf-8');
```

Cookie相关设置
```php
define('COOKIE_DOMAIN', '');
define('COOKIE_SECURE', false);
define('COOKIE_PATH', '/');
define('COOKIE_HTTPONLY', false);
define('COOKIE_EXPIRE', 0);
```

Session设置
```php

//自定义session cookie名
define('SESSION_COOKIE_NAME', 'SE');

//session保存时间，0为关闭浏览器即失效，秒为单位
define('SESSION_EXPIRE', 0);
```

安全配置
```php
//加密安全混淆值
define('ENCRYPTION_KEY', 'weryi9878sdfddtgtbsdfh');

//表单提交token session 名称
define('TOKEN_SESSION_NAME', '34efddddre');

//表单token字段名
define('TOKEN_INPUT_NAME', 'fh40dfk9dd8dkfje');

//token过期时间，秒为单位
define('TOKEN_EXPIRE', 3600);
```

多语言应用
```php
//默认语言环境
define('LANG', 'zh_cn');
```

URL重写转换输出模版，和路由无关，以配合$this->helper->url()使用
```php
//url 重写
define('URL', ['mod_name'=>['controller_name/action_name'=>'/{controller_key}/{action_key}']]);


//例子 

//注意$this->helper->url()参数和数组key的顺序
define('URL', [
    'www' => [ 
    //www表示模块名称
    
        'main/index' => '/',  
        //echo $this->helper->url(['c'=>'main','a'=>'index'])
        //输出 /
        
        'main/lists/type' => '/list/{type}.html',
         //echo $this->helper->url(['c'=>'main','a'=>'lists','type'=>'2'])
         //输出 /list/2.html
        
        'main/lists/type/page' => '/list/{type}_{page}.html'
         //echo $this->helper->url(['c'=>'main','a'=>'lists','type'=>'2','page'=>'34']) 
         //输出 /list/2_34.html
                 
    ]
]);

```

### 数据库配置

> 数据库文件位置在APP_DIR/config/下面三个子目录test,production,development中的database.php文件分别按环境设置。

> 暂时只支持mysqli

default为默认数据库，可以直接$this->db访问默认数据库

$this->db('read)访问read数据库
```php

//例子

return [
    //默认数据库
    'default' => 
    [
        'driver' => 'mysqli',
        'host' => 'set.database.to.hosts.file',
        'username' => 'root', 
        'password' => '123456',
        'database' => 'dataname',
        'port' => 3306,
        'charset' => 'utf8'
    ],
    //读数据库
    'read'=>
    [
        'driver' => 'mysqli',
        'host' => 'set.database.to.hosts.file',
        'username' => 'root', 
        'password' => '123456',
        'database' => 'dataname',
        'port' => 3306,
        'charset' => 'utf8'
    ]
];

```

### 自定义配置数据字典

> 自定义配置数据字典，主要为了应对某些应用较多的元数据存储访问

> 保存于APP_DIR/config目录下面PHP文件内容为数组

以APP_DIR/config/test.php为范例,配合$this->config使用

```php
//test.php内容
return ['key2'=>'val2','key'=>['a','b','c'];

//var_dump $this->config->test
//输出 ['key2'=>'val2','key'=>['a','b','c']

//echo $this->config->test['key'][0]
//输出 a

```

### 全局变量数组

全局变量数组有两个，$var和$instances。

```php

//全局实例初始化数组，包含所有已实例化的核心类
$instances = [];

//全局变量数组，
//$this->vars 可以直接访问，
//默认已包含$this->vars['controller']当前控制器值
//默认已包含$this->vars['action']当前action值
$vars = [];

```


### 数据库基本操作

> 暂时只支持mysqli

> 配置好数据库以后，我们可以 $this->db 调用默认数据库。

> 或者可以$this->db('read')调用一个已配置为'read'的数据库。


#### $this->db->query(sql) 方法

原始SQL语句执行，如是select返回数据集，delete，insert，update返回布尔值。

```php

//返回一个结果集对象句柄
$result=$this->db->query('select * from table1 where f=2;');

//返回数据的条数，int类型
$result->num_rows;

//返回结果集其中一条数据，默认第一条以对象形式返回字段
$result->row();

//以数组形式返回第3条数据
//$row['f'];
$row=$result->row(2,'array');


//以对象形式返回第4条数据
//$row->f;
$row=$result->row(3,'object')


//返回数据集，默认对象形式
$recordset=$result->result();
foreach($recordset as $rs){
    echo $rs->f;
}

//数组形式返回数据集
$recordset=$result->result('array');
foreach($recordset as $rs){
    echo $rs['f'];
}

```

#### $this->db->select(table,condition) 方法

查询数据库表返回数据集对象。
```php

//查询条件
$condition= [
     'where' => ['f1'=>'2','f3>'=>'3','f4!='=>'8'], //where条件,支持运算符>,<,<>,!=,=,in,like,>=,<=
     'fields' => ['f1','f2','f3'],//返回字段
     'orderby' => ['f1'=>'desc','f2'=>'asc'], //排序
     'limit' => [0,20] //返回数据条数 ，也可以是一个int值，如：limit=>10
  ];
 
//返回数据句对象
$recordset=$this->db->select('table1',$condition);
foreach($recordset->result() as $rs){
    echo $rs->f1;
}

```

#### $this->db->insert(table,data) 方法

插入数据到数据库表，返回布尔值。
```php

//需要插入的数据
$data=['f1'=>'1','f2'=>'2'];


$rs=$this->db->insert('table1',$data)；
if($rs){
   //插入成功，返回最后一条插入语句产生的自增ID
   $this->db->insert_id;
}

```

#### $this->db->delete(table,where) 方法

删除数据集，返回布尔值
```php
//删除条件
$data=['f1'=>'1','f2'=>'2'];


$rs=$this->db->delete('table1',$data)；
if($rs){
   //删除成功，返回影响数据行数
   $this->db->affected_rows;
}

```

#### $this->db->escape(str) 方法

SQL语句中的特殊字符进行转义，返回转义后字符串。
```php
//参见 http://php.net/manual/zh/mysqli.real-escape-string.php
$this->db->escape('str');
```

#### $this->db->replace(table,data) 方法

数据集主键如果存在就替换不然插入新数据，返回布尔值。
```php
//需要插入或替换的数据，如果主键primary=1已存在，即替换本条数据，不然插入新数据。
$data=['primary'=>1,'f1'=>'1','f2'=>'2'];

$rs=$this->db->replace('table1',$data)；

```

#### $this->db->update(table,data,where) 方法

更新数据，返回布尔值。
```php

$data=['f1'=>'3','f3'=>'1'];
$where=['id'=>2];

$rs=$this->db->update('table1',$data,$where);

if($rs){
   //更新成功，返回影响数据行数
   $this->db->affected_rows;
 
}


```

#### $this->db->close() 方法

关闭数据库链接，返回布尔值。

正常情况下，框架在执行完到最后自动关闭链接，也可以提前手动关闭。
```php

//关闭默认数据链接
$this->db->close()；

//关闭read数据链接
$this->db('read')->close()；

```


### Model数据模型

> 每个model必须于数据库某个表对应。

> model文件必须放置在APP_DIR/model/目录下，文件名与类名一致，区分大小写。




通过继承\system\Model，我们可以使用框架自带的功能便捷操作数据。

创建一个典型model。

```php
//Tablemodel.php

//命名空间
namespace model;

//类必须继承一个自定义\inherit\Model类或是系统\system\Model类
class Tablemodel extends \inherit\Model
{

    //构造函数必须有
    public function __construct()
    {
    
        //运行上级构造函数
        parent::__construct();
        
        //必须设置一个数据表
        $this->table = 'test';
        
        //必须设置一个主键
        $this->primary = 'id';

        //设置一个表的结构，以便验证过滤
        $this->schema = [
            'id' => [
                'validate' => ['regex' => '/^\d+$/', 'message' => 'ID 不能为空'],
                'literal' => 'ID',
                'default' => null,
                'required' => false
            ],
            'name' => [
                'validate' => ['regex' => '/^\S+$/', 'message' => '名称不能为空'],
                'literal' => '名称',
                'default' => '',
                'required' => true
            ]];
    }

}


//可以这样调用model
$this->model('Tablemodel')->one(1);

```


#### $this->RDB 属性

设置读数据库，默认为default数据库
```php

$this->RDB='read_database';

```

#### $this->WDB 属性

设置写数据库，默认为default数据库
```php

$this->RDB='write_database';

```

#### $this->table 属性

设置model对应数据表
```php

$this->table='table1';

```

#### $this->primary 属性

设置数据表主键字段
```php
$this->primary='id';
```

#### $this->schema 属性

设置数据表结构，以便验证过滤

> validate['regex'] 正则验证字段数据合法性

> validate['message'] 提示信息

> filter 过滤数据，blank|tag|entity 三个值组合使用，
>> blank把连续多个空白字符转换成一个,
>> tag过滤html标签,
>> entity把html标签转换成实体字符。

> literal 字段的字面名字

> default 字段默认值

> required 是否必须填写字段

```php

      $this->schema = [
            'id' => [
                'validate' => ['regex' => '/^\d+$/', 'message' => 'ID 不能为空'],
                'literal' => 'ID',
                'default' => null,
                'required' => false
            ],
            'name' => [
                'validate' => ['regex' => '/^\S+$/', 'message' => '名称不能为空'],
                'filter'=>'blank|tag|entity'
                'literal' => '名称',
                'default' => '',
                'required' => true
            ]
      ];

```

#### $this->all(fields) 方法

获取数据表全部数据集,大表谨慎使用
```php

$recordset=$this->all(['fname1','fname2']);

//注意直接返回数据集，而不是result对象
foreach($recordset as $rs){
    echo $rs->fname1;
}

```

#### $this->belongs(model, relation_model, relation_foreign_name, where, condition) 方法

多对多获取表数据,返回对象数据集

> $model 需要关联的model名称

> $relation_model 关系表model名称
     
> $relation_foreign_name 关联表主键名在关系表中的字段名
     
> $where=['local_relation_filed_name' => 'local_primary_value']
>> $local_relation_filed_name 本表在关系表字段名
>> $local_primary_value 本表主键值

> $condition 参见$this->select()参数
     
     
```php

$this->belongs($model, $relation_model, $relation_foreign_name, $where, $condition);

```

#### $this->count(where) 方法

获取数据表数据条数,适合myisam表
```php

//带条件的计算
$this->count(['field'=>'val']);

//获取表总条数
$this->count();

```

#### $this->delete(where) 方法

删除表数据，成功返回影响数不然返回false
```php
$rs=$this->delete(['f1'=>'2']);

if($rs){
    //返回影响数
    echo $rs;
}

```


$this->hasMany()

$this->hasOne()

$this->insert()

$this->lastid()

$this->one()

$this->query()

$this->select()

$this->update()

$this->>where()



### Model数据验证

### Controller控制器

### View视图

### Helper帮助函数



### Input输入


获取地址查询字符串
```php
//如果var_name为null，就返回默认值default_str
$this->input->get('var_name','default_str');
```
获取表单数据
```php
//字段不存在返回null
$this->input->post('field_name');
```

获取v4 IP地址
```php
//如果没有获取成功返回0.0.0.0
$this->input->ip();
```

判断是否ajax请求
```php
//$_SERVER['HTTP_X_REQUESTED_WITH'] == 'XMLHttpRequest'
//返回布尔值
$this->input->isAjax();
```

获取原始请求数据
```php
$this->input->body();
```

获取上一个来源地址url
```php
//如果没有为空
$this->input->referer();
```

获取当前请求方法
```php
//返回 POST，GET，OPTION等
$this->input->method();
```


### Output输出

### Session会话

### Cookie管理

### Lang多语言配置

### Redis缓存

### Security安全

### Upload上传文件

### Html标签生成

### Grid表格生成

### Image图片修饰

### Smtp发送邮件

### Captcha验证码生成

### 应用程序目录布局说明


|——system    系统框架程序目录 

|——application  应用程序目录  
|————helper   自定义工具帮助库  
|————library  自定义公共类库  
|————language  语言包目录  
|————font 字体目录  
|————document 开发文档目录  
|————module   程序模块  
|——————www  模块名  
|———————-main.php 具体controller业务逻辑文件  

|————model    model文件目录  
|——————test.php 具体数据model 
  
|————template  视图模板文件  
|——————www  模块名   
|————————main.php 具体模板文件  
  
|————config    配置文件目录  
|——————development 开发环境配置  
|————————database.php 数据库配置文件  
|————————constans.php 常量配置文件  
|————————redis.php redis配置文件  
|——————test 开发环境配置  
|————————database.php   
|————————constans.php   
|————————redis.php   
|——————production 产品环境配置  
|————————database.php   
|————————constans.php   
|————————redis.php   

|————inherit    model和controller重写继承目录  
|————————controller.php   
|————————model.php   
  
|——public    应用程序入口目录  
|————static    静态文件资源  
|————www   此目录绑域名用  
|——————index.php    入口文件  
 
