# SqlAlchemy 使用

### 简介

### 安装
+ 使用 `pip install sqlalchemy`进行安装
+ 默认情况下，sqlalchemy是直接支持SQLite3的，不需要安装额外的驱动程序，但是想要支持其他的数据库需要安装相应的驱动程序
	- 若想要支持mysql，需要安装pymysql驱动

### 使用（以使用MySql为例）
+ 连接数据库
	- 要连接数据库，首先需要建立一个SqlAlchemy引擎，为数据库创建一个公共接口来执行SQL语句
	- ```python
        from sqlalchemy import create_engine

        # 引擎
        engine = create_engine(
            'mysql+pymysql://root:739230854@localhost:3306/test', pool_recycle=3600
        )
        collection = engine.connect()  
    ```
	- 注意，`create_engine`函数会返回一个引擎的实例。但是，在调用需要使用链接的操作（比如查询）之前，它实际上并不会打开连接。


# SQLAlchemy Core

### 模式和类型

+ SqlAlchemy提供了四种类型，通用类型、SQL类型、厂商自定义类型、用户定义类型
+ 定义了大量的通用类型，可以从sqlalchemy.type模块中导入，为了方便起见，在sqlalchemy中也可以直接使用
+ 除了通用类型，还可以使用SQL标准类型和厂商自定义类型，是为了方便在通用类型无法满足时调用的
	- SQL标准类型在sqlalchemy.type中可用，且为了和通用类型区分，都要大写
	- 厂商自定义类型只使用于特定的后端数据库类型，可以通过所选方言文档以及Sqlalchemy站点确定可用的类型，都在sqlalchemy.dialects模块中，且每个数据库方言都有若干子模块，如使用postgresql数据库中的json格式，可以使用`from sqlalchemy.dialects.postgresql import json`

+ 完整代码：
    ```python
    from sqlalchemy import create_engine
    from sqlalchemy import MetaData
    from sqlalchemy import Table, Column, Integer, Numeric, String, ForeignKey, Boolean

    # 引擎
    engine = create_engine(
        'mysql+pymysql://root:739230854@localhost:3306/test', pool_recycle=3600
    )
    collection = engine.connect()  # 打开数据库的链接，创建表的时候并不需要

    # 元数据
    metadata = MetaData()

    # 表
    cookies = Table('cookies', metadata,
        Column('cookie_id', Integer(), primary_key=True),  # 作为主键
        Column('cookie_name', String(50), index=True),  # 创建索引，加快查找的速度
        Column('cookie_recipe_url', String(50)),  
        Column('cookie_sku', String(55)),
        Column('quantity', Integer()),
        Column('unit_cost', Numeric(12, 2))  # 长度以及精度
    )

    # 带有更多选项的表
    from datetime import datetime
    from sqlalchemy import DateTime

    user = Table('users', metadata,
        Column('user_id', Integer(), primary_key=True),
        Column('username', String(15), nullable=False, unique=True),  # 必须有，且不能为空
        Column('email_address', String(255), nullable=False),
        Column('phone', String(20), nullable=False),
        Column('password', String(25), nullable=False),
        Column('created_on', DateTime(), default=datetime.now),
        Column('updated_on', DateTime(), default=datetime.now, onupdate=datetime.now)  # 设置onupdate使得每次更新的时候将当前时间设置给列
    )

    from sqlalchemy import ForeignKey

    orders = Table('orders', metadata,
        Column('order_id', Integer(), primary_key=True),
        Column('user_id', ForeignKey('users.user_id')),  # 使用的是字符串，而不是对列的实际引用
        Column('shipped', Boolean(), default=False),
    )

    line_items = Table('line_items', metadata,
        Column('line_items)id', Integer(), primary_key=True),
        Column('order_id', ForeignKey('orders.order_id')),
        Column('cookie_id', ForeignKey('cookies.cookie_id')),
        Column('quantity', Integer()),
        Column('extended_cost', Numeric(12, 2))
    )

    # 表的持久化-即在数据库中创建表
    metadata.create_all(engine)
    ```


## 插入数据

+ `insert`插入
    ```python 
    # 插入数据
    ins = cookies.insert().values(
        cookie_name = 'chocolate chip',
        cookie_recipe_url = "http://xxx",
        cookie_sku = 'CC01',
        quantity = '12',
        unit_cost = '0.50'
    )
    # print(str(ins))
    '''
    INSERT INTO cookies 
        (cookie_name, cookie_recipe_url, cookie_sku, quantity, unit_cost) 
    VALUES 
        (:cookie_name, :cookie_recipe_url, :cookie_sku, :quantity, :unit_cost)
    '''
    # print(ins.compile().params)  # 可以查询随查询一起发送的实际参数
    '''
    {'cookie_name': 'chocolate chip', 'cookie_recipe_url': 'http://xxx', 
    'cookie_sku': 'CC01', 'quantity': '12', 'unit_cost': '0.50'}
    '''
    ```
    在SQL语句中，我们提供的值被替换为`:列名`，ins对象的compile()方法返回一个SQLCompiler对象，该对象允许我们通过params属性访问隋查询一起发送的实际参数。

+ 执行插入语句，执行前需要先添加引擎并连接
    ```python 
    from sqlalchemy import create_engine

    from sqlalchemy_test import cookies

    # 引擎
    engine = create_engine(
        'mysql+pymysql://root:739230854@localhost:3306/test', pool_recycle=3600
    )
    connection = engine.connect()  # 打开数据库的链接

    result = connection.execute(ins) # 使用链接执行

    print(result)  # <sqlalchemy.engine.result.ResultProxy object at 0x7f1d30fe5f10>
    print(result.inserted_primary_key)  # 产看刚刚添加的数据的ID
    ```

+ `insert`除了可以作为表实例cookies的方法来使用外，还可以作为单独的函数来使用，此时表实例cookies作为函数的参数，事实上，这种方法更加贴近于数据库的insert写法，因此更加推荐这种用法
    ```python 
    from sqlalchemy import insert

    ins = insert(cookies).values(
        cookie_name = 'chocolate chip',
        cookie_recipe_url = "http://xxx",
        cookie_sku = 'CC01',
        quantity = '12',
        unit_cost = '0.50'
    )
    ```

+ 连接对象的`execute`方法不仅可以接受语句，还可以在语句之后接受关键字参数值，在编译语句时，它会向列列表添加每个关键字参数键，并将它们的每个值添加到SQL语句的VALUES部分，但是这种用法不常用，但是他很好的说明了语句在发送到数据库服务器之前是如何编译和组装的。可以通过使用一个字典列表一次插入很多条记录，字典里面包含我们要提交的数据。
    ```python 
    ins = cookies.insert()  # 此处的ins也可以这么写 ins = insert(cookies)
    result = connection.execute(
        ins,  # 插入语句任然是execute方法的第一个参数
        cookie_name = 'dack chocolate chip',
        cookie_recipe_url = "http://xxx/qqq",
        cookie_sku = 'CC02',
        quantity = '1',
        unit_cost = '0.75'
    )  # 这样在连接的同时，添加了数据
    ```

+ 进阶版，通过使用一个字典列表一次插入很多条记录，字典里面包含我们要提交的数据</br>
*注意创建字典列表的时候，字典必须拥有完全相同的键，sqlalchemy会根据列表中的第一个字典编译语句，如果后续的字典不同会出错*
    ```python
    inventory_list = [
        {
            'cookie_name': 'peanut butter',
            'cookie_recipe_url': 'http://xxx/1',
            'cookie_sku': 'PB01',
            'quantity': '24',
            'unit_cost': '0.25'
        },
        {
            'cookie_name': 'oatmeal raisin',
            'cookie_recipe_url': 'http://xxx/2',
            'cookie_sku': 'EWW01',
            'quantity': '100',
            'unit_cost': '1.00'
        }
    ]
    result = connection.execute(ins, inventory_list)
    ```

## 查找数据

+ `select`查询:</br>
select 需要一个列的列表来选择，为了方便，还可以直接接受一个Table实例（这里的cookies表），则此时选中该表中的所有的内容
    ```python
    from sqlalchemy import select

    s = select([cookies])  
    # print(str(s))  # 任然可以使用str(s)查看数据库看到的SQL语句，或者直接打印也行
    rp = connection.execute(s)  # rp是ResultProxy的缩写
    # print('rp:',type(rp))  # <sqlalchemy.engine.result.ResultProxy object at 0x7f2dc9f608d0>
    result = rp.fetchall()
    '''
    print(result)
    [
        (5, 'chocolate chip', 'http://xxx', 'CC01', 12, Decimal('0.50')), 
        (6, 'dack chocolate chip', 'http://xxx/qqq', 'CC02', 1, Decimal('0.75')), 
        (7, 'peanut butter', 'http://xxx/1', 'PB01', 24, Decimal('0.25')), 
        (8, 'oatmeal raisin', 'http://xxx/2', 'EWW01', 100, Decimal('1.00'))
    ]
    ```

+ 与`insert()`方法一致，Table实例对象也提供了select方法
    ```python
    s = cookies.select()  # 直接使用cookies的select方法
    rp = connection.execute(s)
    result = rp.fetchall()
    ```

### ResultProxy

+ ResultProxy 是DBAPI游标对象的包装器，主要目的是让语句返回的结果更容易使用和操作，比如，ResultProxy允许使用索引，名称或column对象进行访问，从而额简化了对查询结果的处理
    ```python 
    first_row = result[0]  # 获取ResultProxy的第一行
    print(first_row)
    print(first_row[1])  # 通过索引访问列
    print(first_row.cookie_name)  # 通过名称访问列
    print(first_row[cookies.c.cookie_name])  # 通过column对象访问列
    '''
    (5, 'chocolate chip', 'http://xxx', 'CC01', 12, Decimal('0.50'))
    chocolate chip
    chocolate chip
    chocolate chip
    '''
    ```

+ 迭代的方式获得ResultProxy的项
    ```python
    s = select([cookies])
    rp = connection.execute(s)
    print(rp.keys())  
    # ['cookie_id', 'cookie_name', 'cookie_recipe_url', 'cookie_sku', 'quantity', 'unit_cost']
    for record in rp:
        print(record)  # (9, 'chocolate chip', 'http://xxx', 'CC01', 12, Decimal('0.50'))
        print(type(record))  # <class 'sqlalchemy.engine.result.RowProxy'>  注意是 RowProxy
        print(record.cookie_name)  # chocolate chip
    ```

+ 除了将ResultProxy作为可迭代对象和调用fetchal()方法之外，还用其他通过ResultProxy访问数据的方式：
    - 每种对数据库的不同的操作对应的ResultProxy的方法不同
    - 使用rowcount()
    - 使用inserted_primary_key()获得插入记录的ID--使用insert插入数据的时候使用
    - first()，若有记录，则返回第一个记录并关闭连接，此时想再操作会报异常`sqlalchemy.exc.ResourceClosedError: This result object is closed.`
    - fetchone()，返回一行，并保持光标为打开的状态，以便你做更多的获取调用
    - scalar()，如果查询结果是包含一个列的单条记录，则返回单个值
    - 如果想产看结果集中的多个列，可以使用keys()方法来获得列名列表

+ 最佳实践，在编写生产代码时，应该遵循如下方针：
    > 1. 获取单条记录时，要多用first()方法，尽量不要使用fetchone()和scalar()方法，因为对程序员来说，first方法更加清晰，需要注意，使用first()返回第一条记录后将自动关闭连接
    > 2. 尽量使用可迭代对象ResultProxy，而不要用fetchall和fetchone方法，因为前者的内存效率更高，而且我们往往一次只对一条记录进行操作
    > 3. 避免使用fetchone方法，因为如果不小心，他会一直让连接处在打开状态，因为使用fetchone会返回一行，并保持光标为打开的状态，以便你做更多的获取调用
    > 4. 谨慎使用scalar方法，因为如果查询返回多行多列，就会引发错误，多行多列在测试过程中会经常丢失

+ 以上的查询方式得到各条记录的所有列，通常我们只需要使用这些列中的一部分。如果额外的列中的数据量很大，就会到导致应用程序运行变慢，消耗的内存远超预期，下面我们可以对查询返回的列数进行限制
    ```python
    # 为了限制返回的列数，需要以列表的形式将查询的列传递给select()方法
    s = select([cookies.c.cookie_name, cookies.c.quantity])
    rp = connection.execute(s)
    print(rp.keys())
    print(rp.first())
    '''
    ['cookie_name', 'quantity']
    ('chocolate chip', 12)
    '''
    ```
    **[ * ] `cookies.c.cookie_name`表示cookies表的Column对象（列对象）cookie_name**

### 排序

+ 需要将查找返回的数据按照特性的顺序排列，可以使用`order_by()`语句，默认是升序的方式排序
    ```python
    s = select([cookies.c.cookie_name, cookies.c.quantity])
    s = s.order_by(cookies.c.quantity)  # 在查询后，调用order_by()方法，通过表的列明来指定排列标准
    rp = connection.execute(s)
    for cookie in rp:
        print(f"{cookie.quantity} - {cookie.cookie_name}")   # 通过数据名称进行访问
    ```
    现将select语句保存到变量s中，而后向变量s中添加order_by语句，再讲其重新赋值给s变量。这个示例演示了如何以生成式或者分布方式编写语句。当然你也可以把select和order_by放在一起，就像这样`s = select([...]).order_by(...)`合并为一条语句。

+ 想要降序或者倒序排列，可以使用`desc()`语句，但是，这需要额外的导包`from sqlalchemy import desc`
    ```python
    from sqlalchemy import desc

    s = select([cookies.c.cookie_name, cookies.c.quantity])
    s = s.order_by(desc(cookies.c.quantity))  # 使用desc()对cookies.c.quantity列进行了包装
    # 使用print(s)直接打印，或者使用print(str(s))可以看到对应的sql语句为 SELECT cookies.cookie_name, cookies.quantity FROM cookies ORDER BY cookies.cookie_name DESC
    rp = connection.execute(s)
    ```
    除了调用`desc()`函数，`desc()`还可以作为Column对象的方法进行调用，像这样`cookies.c.quantity.desc()`，但是如果在长语句中这样使用，可能会造成阅读困难，所以建议将`desc()`作为函数进行使用。

### 对返回数据的结果条数进行限制

+ 虽然可以对ResultProxy使用first()和fetchone()获得一行数据，但是实际查询了所有的结果，使用limit()方法让limit语句成为查询的一部分，对返回的结果集的条数进行限制
    ```python
    # 查询两种库存量最少的cookies
    s = select([cookies.c.cookie_name, cookies.c.quantity])
    print(type(s))  # <class 'sqlalchemy.sql.selectable.Select'>
    s = s.order_by(cookies.c.quantity)
    s = s.limit(2)  # 和order_by一样，都是Select自带的，不需要导包
    rp = connection.execute(s)
    print(rp.fetchall())  # [('dack chocolate chip', 1), ('chocolate chip', 12)]
    print([result.cookie_name for result in rp])  # [] 此时为空，是查不到数据的
    ```
    注意最后一行，想再次从ResultProxy对象中获得数据的时候，发现获取不到了，这是因为[ResultProxy](#ResultProxy)对象是DBAPI游标对象的包装器，游标实际上是一种能从包括多条数据记录的结果集中每次提取一条记录的机制。游标充当指针的作用。</br>
    ResultProxy对象在被使用一次后，会记录位置，

### 内置SQL函数和标签

+ 许多数据库设计都包含了SQL函数，这些函数的设计目的是为了让某些操作可以直接在数据库服务器上使用，如SUM函数SqlAlchemy可以利用后端数据库中的SQL函数，常见的函数为SUM以及COUNT，想使用这两个函数，需要导入包 `sqlalchemy.sql.func`模块，这些函数包装在他们要操作的列上
    ```python
    # 计算cookie的总数
    from sqlalchemy.sql import func
    s = select([func.sum(cookies.c.quantity)]) 
    print(s)  # SELECT sum(cookies.quantity) AS sum_1 FROM cookies
    rp = connection.execute(s)
    print(rp.scalar())  # scalar只返回查询到的第一个数据的最左边的列，且只返回第一行的数据
    ```
    **[ * ]建议使用导入func模块的方式，因为直接导入sum可以会引起问题，而且还容易和Python内置的sum函数混淆**

+ count计数
    ```python 
    s = select([func.count(cookies.c.cookie_name)])
    print(s)  # SELECT count(cookies.cookie_name) AS count_1 FROM cookies
    rp = connection.execute(s)
    recode = rp.first()  # 数据类型为RowProxy，代表查询到的一行数据，若直接打印
    print(type(recode))  # <class 'sqlalchemy.engine.result.RowProxy'>，但是直接打印的话就是一行数据，以元组的形式打印的
    print(recode)
    print(recode.keys())  # 以列表的形式返回查找的数据的各列名组成的列表
    print(recode.count_1)  
    ```
    RequestProxy和RowProxy类型都可以调用keys()方法</br>
    print(recode.count_1)是以列名的形式打印内容的，因为使用使用COUNT和SUM方法都会以`<func_name>_<position>`的形式起别名，position是调用COUNT和SUM方法的次数，如第二个调用的count()函数得到的别名将是count_2。

+ 别名应当清晰、明确，SQLAlchemy提供了label()函数来解决这个问题，可以使用label()函数来为列取个别名
    ```python
    s = select([func.count(cookies.c.cookie_name).label('inventory_count')])
    print(s)  # SELECT count(cookies.cookie_name) AS inventory_count FROM cookies
    rp = connection.execute(s)
    print(rp.first().keys())  # ['inventory_count']
    ```
    只需要在更改的列对象上调用label()函数即可</br>
    这意味着，我们在查找原有列名时，也可以对返回的列明取别名，像这样`s = select([cookies.c.cookie_name.label('cookie_name_count')])`</br>
    **[ * ]除了fitchall得到的是list，其他的方法得到的行数据都是RowProxy类型，当然fitchall()得到的是行数据的集合，所以列表中的每一项都是RowProxy类型.**

### 过滤
+ 过滤，对查询进行过滤是通过where()语句来完成，可以把多个where()子句接在一起使用，功能就像传统的SQL语句中的AND一样
    ```python
    s = select([cookies]).where(cookies.c.cookie_name == "chocolate chip")
    print(s)  # SELECT cookies.cookie_id, cookies.cookie_name, cookies.cookie_recipe_url, cookies.cookie_sku, cookies.quantity, cookies.unit_cost FROM cookies WHERE cookies.cookie_name = :cookie_name_1
    print(s.compile().params)  # 查询发送的实际参数
    rp = connection.execute(s)  
    recode = rp.first()
    print(type(recode))
    print(recode.items())  # [('cookie_id', 5), ('cookie_name', 'chocolate chip'), ('cookie_recipe_url', 'http://xxx'), ('cookie_sku', 'CC01'), ('quantity', 12), ('unit_cost', Decimal('0.50'))]
    ```
    RowProxy对象的items()方法返回得到由列名和值组成的元组列表

+ like模糊查询，查询包含chocolate的cookie名
    ```python 
    s = select([cookies]).where(cookies.c.cookie_name.like('%chocolate%'))
    print(s)  # SELECT cookies.cookie_id, cookies.cookie_name, cookies.cookie_recipe_url, cookies.cookie_sku, cookies.quantity, cookies.unit_cost FROM cookies WHERE cookies.cookie_name LIKE :cookie_name_1
    ```

### ClauseElement
+ ClauseElement，是在子句中使用的实体，一般是表中的列。不过，与列不同的是ClauseElement拥有许多额外的功能，如之前我们调用的like()方法，此外还有许多额外的方法。
    方法 | 用途
    :--|:--:
    between(cleft, cright) | 查找cleft和cright之间的内容
    concat(column_two)|连接列
    destinct()|查找列的唯一值
    in_([list]) | 查找列在列表中的位置
    is_(None) | 查找列None的位置（通常用于检查Null和None）
    contains(string)| 查找包含string的列（区分大小写）
    endswith(string)| 查找以string结尾的列（区分大小写）
    like(string)| 查找与string匹配的列（区分大小写）
    startwith(string)| 查找以string开头的列（区分大小写）
    ilike(string)| 查找与string匹配的列（不区分大小写）
    以上这些方法都有相反的方法，例如notlike()和notin_()。not<方法>这种命名方式约定的唯一例外是不带下划线的isnot()方法。

### 除了ClauseElement中的这些方法之外，还可以在where()语句中使用运算符
+ 除了使用是否等于值，以及CalauseElement的方法之外，还可以使用运算符。SQLAlchemy针对大多数的Python运算符都做了重载，包括标准的比较运算符（==、！=、<、>、<=、=>），他们的功能和在Python中一样。在与None比较时，==被重载为IS NULL语句。算数运算符（+、-、*、/和%）还可以用来独立于数据库的字符串做连接处理。

+ 使用+连接字符串
    ```python
    s = select([cookies.c.cookie_name, 'SKU-' + cookies.c.cookie_sku])
    print(s)  # SELECT cookies.cookie_name, :cookie_sku_1 || cookies.cookie_sku AS anon_1 FROM cookies
    for row in connection.execute(s):
        print(row)
    '''
    ('chocolate chip', 'SKU-CC01')
    ('dack chocolate chip', 'SKU-CC02')
    ('peanut butter', 'SKU-PB01')
    ('oatmeal raisin', 'SKU-EWW01')
    ('chocolate chip', 'SKU-CC01')
    ('peanut butter', 'SKU-PB01')
    ('oatmeal raisin', 'SKU-EWW01')
    '''
    rp =  connection.execute(s)
    print(rp.keys())  # ['cookie_name', 'anon_1']
    ```
    在sql中，`||`为连接符

+ 运算符的另外一种用法是根据多个列来计算值
    ```python
    # 计算各种cookie的库存价值
    from sqlalchemy import cast, Numeric
    s = select([cookies.c.cookie_name,
            cast((cookies.c.quantity * cookies.c.unit_cost),
            Numeric(12,1)).label('inv_cost')])
    print(s)  # SELECT cookies.cookie_name, CAST(cookies.quantity * cookies.unit_cost AS NUMERIC(12,1)) AS inv_cost FROM cookies
    for row in connection.execute(s):
        print("{} - {}".format(row.cookie_name, row.inv_cost))
    """
    chocolate chip - 6.0
    dack chocolate chip - 0.8
    peanut butter - 6.0
    oatmeal raisin - 100.0
    chocolate chip - 6.0
    peanut butter - 6.0
    oatmeal raisin - 100.0
    """
    ```
    cast()是另一个允许做类型转换的函数，使用方式是cast(数据,转换类型)，这里如果不使用cast，即使用
    ```python
    s = select([cookies.c.cookie_name,
                (cookies.c.quantity * cookies.c.unit_cost).label('inv_cost')])
    ```
    也是可以获得相应的数据的，此时我们可以在python语句中对数据类型进行强制转换
    ```python
    print("{} - {:.2f}".format(row.cookie_name, row.inv_cost))
    ```
    显示的效果是一样的。

+ 布尔运算符： SQLAlchemy还支持布尔运算符AND、OR和NOT，他们用位运算符（&、|和~）来表示。受Python运算符优先级规则的影响，应当尽量使用连接词，而不要使用这些重载的运算符

+ 连接词：and_()、or_()、not_()。为了实现某种期望的效果，我们既可以把多个where()子句连接在一起，也可以使用连接词来实现，而且使用连接词的可读性更好，功能性更强。
    ```python
    from sqlalchemy import and_,or_,not_

    s = select([cookies]).where(
        and_(cookies.c.quantity > 23,
            cookies.c.unit_cost < 0.40
        )
    )
    '''
    # 等价的版本（使用布尔运算符--注意优先级）
    s = select([cookies]).where(
        (cookies.c.quantity > 23) &
            (cookies.c.unit_cost < 0.40)
        )
    '''
    print(s)  # SELECT cookies.cookie_id, cookies.cookie_name, cookies.cookie_recipe_url, cookies.cookie_sku, cookies.quantity, cookies.unit_cost FROM cookies WHERE cookies.quantity > :quantity_1 AND cookies.unit_cost < :unit_cost_1 
    for row in connection.execute(s):
        print(row.cookie_name)
    ```
+ 配合ClauseElement使用
    ```python
    s = select([cookies]).where(
        or_(
            cookies.c.quantity.between(10,50),
            cookies.c.cookie_name.contains('chip')  # 查询包含'chip'的项
        )
    )

    ```
## 更新数据
+ update()方法和之前的insert()方法相似，语法几乎一致，但是update()还可以指定一个where()子句，用来指出要更新的行。


## 删除数据







