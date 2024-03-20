---
title: python入门语法速查手册
date: 2023-3-10 10:31:18
categories: 
- AID学习笔记
tags: 
- python
- 语言学习

---
---
#### 语法速查手册
##### 数据基本运算

* 变量
        变量名 = 数据

* 人机交互
        变量名 = input(提示信息)
        print(结果)

* 数据类型
        整数int
        小数float
        字符串str
        转换
        变量名 = 目标类型(待转数据)

* 数学运算

    1. 算数运算符

            + - *
            幂运算**
            小数商/
            整数商//
            余数%
    1. 增强运算符

            +=  -=  *=
            幂运算**=
            小数商/=
            整数//=
            余数%=
    * 布尔运算

            * bool类型
                True条件满足
                False条件不满足
            * 比较运算符
                \> < >= <=
                等于==
                不等于!=
            * 逻辑运算符
                并且and
                或者or
                取反not

##### 流程控制语句

* 选择语句

        if 条件1:
            满足条件1执行的代码
        elif 条件2:
            不满足条件1,但满足条件2执行的代码
        else:
            不满足条件执行的代码

* 循环语句

        while 循环
        while True:
              循环体
              if 退出条件:
                    break
        计时器 = 开始
        while 计数器 < 结束值:
               循环体
               计时器 += 间隔

* for 计数器 in range(开始,结束,间隔):

* 跳转语句

        break
        continue

##### 容器

* 列表list

        适用性:存储多个有顺序的数据

* 基础操作

        创建
        列表名 = [数据1,数据2]
        添加
        列表名.append(数据)
        列表名.insert(索引,数据)
        定位
        列表名[整数]
        列表名[开始:结束:间隔]
        删除
        del 列表名[整数]
        del 列表名[开始:结束:间隔]
        列表名.remove(元素)
        遍历
        for item in 列表名:
        for i in range(len(列表名)):

* 深浅拷贝

        深拷贝:复制所有层
        import copy
        新列表 = copy.deepcopy(旧列表)
        浅拷贝:只复制一层,共享深层
        新列表 = 旧列表[:]

* 列表推导式

        新列表 = []
        for item in 可迭代对象:
              if 条件:
                    新列表.append(item)
        新列表 = [item for item in 可迭代对象 if 条件]

* 元组tuple

        适用性:存储无需改变的有序数据,比列表节省内存空间
        基础操作
        创建
        元组名 = (数据1,数据2)
        元组名 = 数据1,数据2
        元组名 = (数据1,)
        定位
        元组名[整数]
        元组名[开始:结束:间隔]
        遍历
        for item in 元组名:

* 字符串str

        转义符:换行\n  \"  \'    \\
        格式化字符串
        语法:"格式"%(变量)
        占位符
        %s原样输出
        %.2f四舍五入
        %.2d整数位数

* 字典dict

        适用性:存储有多个维度的数据
        基础操作
        创建
        字典名 = {键1:值,键2:值}
        添加
        字典名[键] = 值
        定位
        字典名[键]
        删除
        del 字典名[键]
        遍历
        for key in 字典名:
        for value in 字典名.values():
        for key,value in 字典名.items():
        
        字典推导式
        
        新字典= {}
        for 变量 in 可迭代对象:
              if 条件:
                    新字典[键] = 值
        新字典 = {键:值 for 变量 in 可迭代对象 if 条件}

* 容器嵌套

        列表内嵌字典
        列表名 = [
              {键1:值,键2:值},
              {键1:值,键2:值},
        ]
        列表名[索引][键]
        列表内嵌列表
        列表名 = [
              [元素1,元素2],
              [元素1,元素2],
        ]
        列表名[索引][索引]]

##### 函数

* 作用:将功能的做法与用法分离
* 语法

        def 函数名(参数):
              某个功能的做法
              return 结果
        变量名 = 函数名(数据)

* 函数将结果存储在传入的可变对象中

        而不使用返回值return
        def 函数名(参数):
             参数[0] = 结果
        可变对象 = []
        函数名(可变对象)
        print(可变对象[0])

* 作用域

        局部变量:函数内部创建,只能函数内部使用
        def 函数名(参数):
              局部变量名 = 数据
        全局变量:在文件中创建,整个文件都可使用
        全局变量名1 = 数据
        def 函数名():
              print(全局变量名1)
              global 全局变量名2
              全局变量名2 = 新数据
        全局变量名2 = 数据

* 参数

        形式参数:限制实参
        def 函数名(参数1,参数2)
        def 函数名(参数1=数据,参数2=数据)
        def 函数名(*args):
        def 函数名(**kwargs):
        def 函数名(*args,参数):
        def 函数名(*,参数):
        实际参数:如何与形参进行对应
        函数名(数据1,数据2)
        函数名(参数2=数据2)
        函数名(*序列)
        函数名(**字典)

##### 面向对象

* 类和对象

        类:由变量和函数组成的新数据类型
        class 类名:
             def __init__(self,参数):
                   self.实例变量 = 参数
             def 函数名(self,参数):
                   语句组
        对象名 = 类名(数据)
        对象名.实例变量
        对象名.函数名(数据)
        跨类调用
        class A:
             def a(self):
                 变量名 = B()
                 变量名.b()

        class B:
             def b(self):
                  pass
        class A:
             def __init__(self):
                  self.变量名 = B()
             def a(self):
                 self.变量名.b()

        class B:
             def b(self):
                  pass
        class A:
             def a(self,参数):
                 参数.b()

        class B:
             def b(self):
                  pass
        变量 = A()
        变量.a(B())
        继承
        class 爸爸:
             def __init__(self,爸爸参数):
                    self.爸爸的实例变量 = 爸爸参数
             def 爸爸的函数(self):
        class 儿子(爸爸):
             def __init__(self,爸爸参数,儿子参数):
                    super().__init__(爸爸参数)
                    self.儿子的实例变量 =  儿子参数
             def 儿子的函数(self):
                 self.爸爸的函数()
        多态
        class Model:
             # print时自动调用
             def __str__():
             # remove时自动调用
             def __eq__():
             # sort时自动调用
             def __gt__():
             
##### 高级语法

* 导入模块

        import 包.模块名
        包.模块名.成员
        from 包.模块名 import 成员名
        直接使用成员名
* time模块
        
        时间
        时间戳 = time.time()
        时间元组 = time.localtime()
        转换
        时间元组 = time.localtime(时间戳)
        时间戳 = time.mktime(时间元组)
        格式化
        字符串 = time.strftime(格式, 时间元组)
        时间元组 = time.strptime(字符串,格式)
* 异常处理

        处理
        try:
            可能出错的语句
        except 异常类型:
            处理逻辑
        except Exception:
            处理逻辑
        finally:
            一定执行的逻辑
        发送
        raise 异常类型()

* 生成器

        函数
        定义
        def 函数名():
             函数体
             yield 数据
        调用
        生成器对象 = 函数名()    # 调用函数不执行  
        for item in 生成器对象:
        生成器对象 =函数名()
        容器类型(生成器对象)
        内置生成器
        for i,item in enumerate(容器):
        for item in zip(容器1,容器2)
        表达式
        生成器对象 = (变量 for 变量 in 可迭代对象)

* 函数式编程

        函数作为参数
        高阶函数
        def 通用函数(容器,条件):
             for item in 容器:
                 if 条件(item):
                     return item
        lambda 表达式
        结果 = 通用函数(容器,lambda 参数:函数体)
        函数作为返回值
        装饰器
        def 装饰器名称(func):
            def wrapper(*args,**kwargs):
                新功能"
                result = func(*args,**kwargs)
                return result
            return wrapper
        @装饰器名称
        def 旧功能():

##### 文件操作

* 文件管理

        创建路径
        对象名 = Path(目录1,目录2)
        对象名 = Path.cwd().joinpath("目录1","目录2")
        路径信息
        对象名.absolute()  # 绝对路径(路径类型)
        对象名.name  # 带后缀的完整文件名(str类型)
        对象名.stem  # 文件名不带后缀(str类型)
        对象名.suffix # 文件后缀(str类型)
        对象名.parent  # 上一级路径(路径类型)
        对象名.parts  # 分割路径(tuple类型)
        对象名.exists()  # 路径是否存在(bool类型)
        对象名.is_file()  # 是否文件(bool类型)
        对象名.is_dir()  # 是否目录(bool类型)
        对象名.is_absolute() # 是否绝对路径(bool类型)
        对象名.stat().st_ctime  # 创建时间(时间戳)
        对象名.stat().st_atime  # 访问时间(时间戳)
        对象名.stat().st_mtime  # 修改的时间(时间戳)
        对象名.stat().st_size  # 文件大小(字节Bete)
        搜索目录
        # 搜索当前目录所有路径(一层)
        生成器 = 对象名.iterdir():
        # 根据通配符搜索当前目录所有路径(一层)
        生成器 = path.glob("通配符"):
        # 根据通配符递归搜索当前目录所有路径(多层)
        生成器 = 对象名.rglob("通配符")
        新建路径
        # 新建文件
        对象名.touch()
        # 新建目录
        对象名.mkdir()
        # 忽略目录存在时的报错
        对象名.mkdir(exist_ok=True)
        重命名
        对象名.rename(新路径对象)
        删除路径
        # 删除文件(永久删除，回收站不存在)
        对象名.unlink()
        # 删除目录(目录必须为空)
        对象名.rmdir()

* 文件读写

        文本文件
        打开
        with open(文件路径,"操作模式",encoding="编码方式") as 对象名:
            通过对象名操作文件
        操作
        # 读取指定数量字符
        字符串 = 对象名.read(字符数)
        列表 = 对象名.readlines()
        # 读取文件每行字符
        for 行 in 对象名:
        # 将字符串写入到文件
        字符数 = 对象名.write(字符串)
        二进制文件
        打开
        with open(文件路径,"操作模式") as 对象名:
            通过对象名操作文件
        操作
        # 读取文件中指定数量字节
        字节串 = 对象名.read(字节数)
        # 将字节串写入到文件
        字节数 = 对象名.write(字节串)

##### 数据库基础操作

* DDL

        create database 数据库名称;
        use 数据库名称;
        create table 表名 (
            列名 数据类型 约束 comment '注释',
            列名 数据类型 约束 comment '注释'
        ) comment='注释';
        数据类型
        int
        float
        char(字符数)
        varchar(字符数)
        约束
        primay key
        auto_increment
        not null
        check(条件)
        default(默认值)

* DML

        insert into 表名(列名) values(数据);
        update 表名 set 列名 = 值,列名 = 值 where 条件;
        delete from 表名 where 条件;

* DQL(单条语句)

        select 列名1,列名2 from 表名;
        select 列名1,列名2 from 表名 where 条件;
        select 聚合函数(列名) from 表名 where 条件;
        select 聚合函数(列名) from 表名 where 条件 group by 列名;
        select
        case
              when 条件 then 结果
              else 结果
        end
        from 表名;
        select
        聚合函数(列名),
        case
              when 条件 then 结果
              else 结果
        end as 别名
        from 表名
        group by 别名;
        select 聚合函数(列名) from 表名 where 条件 group by 列名
        having 对聚合结果处理的条件;
        select 列名 from 表名 where 条件 group by 列名
        having 对聚合结果处理的条件
        order by 列名 desc;
        select 列名 from 表名 where 条件
        group by 列名 having 对聚合结果处理的条件
        order by 列名 desc
        limit 查询的行数 offset 跳过的行数;
        select 列名 from 表名 ...
        union all
        select 列名 from 表名 ...

* 多表查询

        连接查询
        select 列名 from 表1 join 表2 on 表1.共同列 = 表2.共同列...;
        select 列名 from 表1 left join 表2 on 表1.共同列 = 表2.共同列...;
        select 列名 from 表1 right join 表2 on 表1.共同列 = 表2.共同列...;
        子查询
        # 第一步:
        select ....
        # 第二步:
        select ....
        # 第三步:
        select .... (
            select ....
        )

* 数据库优化

    1. 数据库设计三大范式

                第一范式:表中每列数据，不可再被拆分，不允许多个数据存储在一列中
                第二范式:每行必须可以被唯一区分，所有列依赖于主键列
                第三范式:非主键列不能依赖其他非主键列

    1. 索引

                定义:加快数据库查询速度的一种数据结构

    1. B+树

    1. 分类

                create index 索引名 on 表名(列名);
                create unique index 索引名 on 表名(列名);
                create index 索引名 on 表名(列名1,列名2);
                create unique index 索引名 on 表名(列名1,列名2);

    1. SQL语句优化

                何时用索引
                1.为经常用于查询的列创建索引
                2.尽量选择区分度高的列作为索引，区分度越高索引效率越高
                3. 尽量避免使用select *，用具体列名代替*，可减少回表查询
                4. 建立外键会自动建立索引，在关联查询时建议使用外键作为关联条件
                会导致索引失效的操作
                1.在索引列上使用like模糊匹配，如果以通配符开头，会导致索引失效
                2. 在索引列上进行运算操作，会导致索引失效
                3.or两边的列有一个没索引，就会导致另一个索引失效
                联合索引注意事项
                1. 联合索引要遵守最左前缀法则，即查询从索引的最左列开始
                2. 联合索引,范围查询时（> < !=），右侧引索列失效
                按效率：count(*) ≈ count(1) > count(主键) > count(列名)

    1. 事务四大特征

                原子性 + 一致性
                原理
                - 回滚日志undolog：
                  - 记录数据被修改前的信息，供回滚到事务开始前
                - 每行的2个隐藏列：
                  - 事务ID：最近操作当前行的事务编号
                  - 回滚指针：指向undolog中该行修改前的信息
                隔离性
                持久性
                原理
                重做日志redolog：
                - 记录对数据的修改，供系统故障后能够恢复
    1. 隔离级别

                可重复读repeatable read
                原理：第一次select时获取一个视图快照，在事务执行期间，其他事务对数据的修改不影响当前事务的视图快照，保持了事务开始时的一致性。
                串行化serializable
                多个事务排队执行，每个事务在执行完之前，下一个事务无法执行
                读已提交read committed
                能够读取其他事务已提交的数据，不读取未提交的数据
                读未提交read uncommitted
                可以读取其他事务未提交的数据

##### pymysql

1. 原生写法

        导入
        from pymysql import Connection
        创建连接
        conn = Connection(
                    host=地址, port=端口号,
                    user=用户名, password=密码,
                    database=数据库名, autocommit=True
                )
        执行增删改
        try:
                with conn.cursor() as cursor:
                        cursor.execute(sql语句, 参数)
                        return cursor.lastrowid
        except Exception as e:
            print(f"发生错误: {e}")
            return -1
        查询
        try:
              with conn.cursor() as cursor:
                        cursor.execute(sql, params)
                        return cursor.fetchall() # 或者 fetchone()
        except Exception as e:
                    print(f"发生错误: {e}")
        事务
        try:
                    conn.begin()
                    with conn.cursor() as cursor:
                        count = 0
                        for sql, params in sql_params:
                            count += cursor.execute(sql, params)
                        conn.commit()
                        return count
        except Exception as e:
                    conn.rollback()
                    print(f"发生错误: {e}")
                    return -1
        关闭
        conn.close()

* 自定义MySqlHelper

        导入
        from common.sql_tools import MySqlHelper
        创建对象
        helper = MySqlHelper(数据库名)
        执行增删改
        结果 = helper.execute(SQL语句,参数)
        执行查询
        结果 = helper.fetch_one(SQL语句,参数)
        结果 = helper.fetch_all(SQL语句,参数)
        执行事务
        结果 = helper.execute_transaction(
              (SQL语句,元组类型的参数),
              (SQL语句,元组类型的参数)
        )
        关闭
        helper.close()

* 存储过程

        创建
        delimiter $$
        create procedure 存储过程名(参数名 数据类型)
        begin
            语句组
            select 结果;
        end$$
        调用
        call 存储过程名(参数);

