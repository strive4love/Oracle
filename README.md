# 关系模型 
## 关系术语
关系（Relation）即“表”或“元组的集合”；元组（Tuple）即“表中的行”；属性（Attribute）即“表中的列”；域（Domain）即“属性的取值范围”；主码/主关键字（main key）即“可以唯一确定一个元组的默认咯属性组”；分量（Component）即“元组中某一个属性的属性值”；关系模式（Relation Schema）即“对关系的描述，表示为关系名（属性1，属性2，...，属性n）”；

# Oracle物理存储结构
## 数据文件（*.dbf）：用户查询（select）的数据不再内存的数据缓冲区中，Oracle就从数据文件中把数据读取出来，放到内存的缓冲区中；用户添加（insert）或修改(update)的数据会先保存在内存的数据缓冲区中，然后由Oracle的后台进程DBWn将数据写入数据文件；一个数据文件只对应一个数据库（database）；一个数据文件只从属于一个表空间（TableSpace）但一个表空间可以占用多个数据文件；
## 控制文件（*.ctl）:数据库被创建时生成，用于存放数据文件和日志文件的信息。一个数据库（database）通常包含多个控制文件且需要不断更新控制文件，一旦控制文件受损，数据库将无法正常工作（Oracle默认创建3个包含相同信息的控制文件，其中一个受损时，可调用其它控制文件继续工作）。
## （重做）日志文件（*.log）：记录数据库中所有修改信息，以保证数据库安全，实现数据库备份与恢复。
## 归档（重做）日志文件：ARCn进程将写满的日志文件（*.log）内容复制到归档日志文件中保存。
## 警告、跟踪日志文件：当一进程发现一个内部错误时，先将错误事件的说明存放在警告文件中，随之再将错误的详细信息存放在跟踪文件爱你中。

# Oracle逻辑存储结构
## 表空间（Tablespace）


## oracle 完全学习手册
前言
•	数据主流数据库系统的比较
数据库系统/功能	Oracle	MySQL	SQL Server	DB2
是否开源／收费	闭源／收费	开源／免费○1
闭源／收费	闭源／收费
视图	支持	支持	支持	支持
触发器	支持	支持○2
支持	支持
存储过程	支持	支持	支持	支持
索引	强	弱	中	中
事务支持	强	一般	中	强
复杂查询	强	弱	中	中
安全性	强	中	中	强
快照	支持	不支持	支持	支持
易学性	难	简单	简单	难
用户友好度	中	一般	强	中
○1MySQL也有商业支持版，收费产品；○2MySQL 无法实现基于语句的触发
•	数据主流数据库系统的比较
基于行的存储实现将数据存为一下格式
1,Smith,Joe,40000;2,Jones,Mary,50000;3,Johnson,Cathy,44000;
基于列的存储实现将数据存储为以下格式
1,2,3;Smith,Jones,Johnson;Joe,Mary,Cathy;40000,50000,44000;
然后一条语句计算表中所有工资的平均数；Select  avg(salary) from T，列存储速度明显快于行存储。Google Map 和Google Reader 应用基于的BigTable，以及MySQL都是面向列存储的数据库系统，从长期来看，其他数据库系统也会从行存储转换为列存储。
•	插件式存储引擎○1（Pluggable Storage Engine）
例如MySQL 5.0 版之前，所有的存储引擎必须重新编译才能集成到 MySQL中。这种情况在MySQL5.1中发生了变化，插件式的设计（用反射+多态来实现？）使得存储引擎可在MySQL运行时动态的插入。○1引擎：以实现特定功能所编写的可复用的程序，如存储引擎（实现文件读取）、计算引擎
第一部分 ORACLE 数据库体系结构 
Oracle数据库物理存储结构
•	数据文件（Data File）包含表或索引形式逻辑表示的实际数据库数据，数据库文件和表空间之间是n：1的关系即一个数据文件只能从属于一个表空间，但一个表空间可占用多个数据文件
•	控制文件（Control File）是一个二进制文件，在数据库生成时生成。
Oracle数据库逻辑存储结构
Oracle数据库进程结构
Oracle数据库内存结构
数据库服务器（也叫做实例）是由一组内存结构和访问数据库文件的后台进程组成。Oracle服务器（实例）的内存结构主要由系统全局区（System Global Area）和程序全局区（Program Global Area）组成。一台电脑上可以安装多个数据库服务器（实例），但各自占用的端口一定不同；一个数据库服务器（实例）可以管理多个数据库，就好比一个Tomcat服务器（好比数据库服务器）上可以部署多个应用（好比多个数据库）一样。一个数据库就是一组数据集合，物理上表现为一组数据文件。 
例：一台电脑（ukspddmrx02a.uk）上安装了两个实例mx12a_sql,占用8201端口 和mx12b_sql，占用8202端口。其中 mx12a_sql实例中创建了两个数据库MXG_ENV802，用户名是MUREXDB,另一个数据库MXGDM_ENV802，用户名是DMDBO
[mx12a_sql]
master=TCP,ukspddmrx02a.uk,8201
query=TCP,ukspddmrx02a.uk,8201

[mx12b_sql]
master=TCP,ukspddmrx02a.uk,8202
query=TCP,ukspddmrx02a.uk,8202

mx12a_sql.MXG_ENV802(login: MUREXDB)
mx12a_sql.MXGDM_ENV802(login: DMDBO)
•	系统全局区（SGA）是由Oracle分配的一组用户共享的内存结构。当服务器（实例）启动时，SGA的存储空间自动得被分配，当服务器（实例）关闭时，SGA存储空间被回收。每个数据库服务器（实例）都有自己的一个SGA， SGA中存储该实例的数据和控制信息，所有连接到该实例的全部用户可共享SGA中的全部信息（SGA第一感觉像应用服务器的Application  ）。 SGA总容量 =共享池（Shared Poll）+ 高速缓存（Data Buffer） +重做日志缓冲区（Log Buffer）+ Java存储区+大型存储区。
o	共享池:大小主要由初始化参数SHARED_POLL_SIZE来决定，值越大，多用户系统的性能就越好；值越小，使用的内存就越少。主要包括两个高速缓存区（Cache），
	一个Cache是数据字典高速缓存用于存储数据字典信息、用户访问权限、安全性等信息。Oracle服务器（实例）读取这些信息来分析SQL语句。
	另一个Cache是库高速缓存用来存储已经提交给Oracle的SQL语句、分析过的格式和执行计划、被执行过的PL/SQL包头与过程Java的类。当一条SQL语句由应用程序发出时，Oracle服务器（实例）首先查找库高速缓存，查看相同的语句（完全相同，包括字母大小写）是否已经被使用过，如果有，Oracle将从相应的内存（数据缓冲区）中直接读取结果，从而提高SQL语句的执行效率。
o	高速缓存:大小由初始化参数DB_CACHE_SIZE来决定。当Oracle接收到修改数据的请求时，它首先查看高速缓存中是否包含要修改的目标数据，如果包含，就对数据块中数据进行修改，同时写入日志，然后该块缓冲区移入Dirty 表中（Dirty 表中存放已修改但未写入到磁盘的数据），此时当Oracle接收到查询数据的请求时，Oracle 实例会先在Dirty 表中查找，如Dirty 表中找不到时，Oracle实例才会从物理文件中读取，但读取之前会先确定有Free Buffer存在，然后把读取的信息放入高速缓存的Free Buffer中.然后这段Free Buffer被标注为Pinned Buffer即不再属于Free Buffer。Oracle采用LRU算法（Least Recently Used）即最近最少使用算法，对内存中的数据块进行排序管理，最近被使用的数据块排在列表最前面，最少被使用得数据块排在列表最后面，当列表已满，且有新的数据块进入列表，则最少使用的数据块将从列表中被清除，以为最新数据块释放空间。该算法保持内存中的数据块是最近被使用的。
o	重做日志缓冲区：大小由LOG BUFFER参数决定，日志读写进程会日志信息写入Log Buffer，当整个Log Buffer被填满时，进程会将它的全部内容写入在线重做日志文件。
o	Java存储区：大小由JAVA_POOL_SIZE参数决定，Oracle 9i明显加强了对Java的支持，9i之前JAVA_POOL_SIZE为可选项，即可以被设置为0。
o	大型存储区：大小由LARGE_POOL_SIZE参数决定，并行执行时，将它作为消息缓冲区，数据备份时将它用作磁盘I/O缓冲区。

•	系统全局区（PGA）： 是一个内存区，包含单个进程的数据和控制信息，所以又称为进程全局区，它主要包括排序区、用户私人会话信息、堆栈空间。
•	Oracle进程分为两类，服务器进程和后台进程。服务器进程用于处理连接到该实例的用户进程的请求。服务器进程在处理用户进程请求的过程中，将调用后台进程来实现对数据库的操作。大部分后台进程在实例启动时自动建立。一个 Oracle实例可以有许多后台进程，但它们  不是一直存在。后台进程可以分为以下几种：
o	DBWR ： 当服务器进程将一缓冲区移入Dirty 表，当该Dirty表达到临界长度时，服务器进程将通知DBWR将“弄脏”的高速缓冲（Data Buffer）中的数据写入磁盘文件；另外由于Dirty表中的内存数据块排序是采用LRU机制来管理的，当Oracle进程在LRU表中太长时间没有查找到可用的缓冲区，它将停止查找并通知DBWR进行写。
o	LGWR：负责将日志缓冲区中的日志信息写入磁盘日志文件。
o	CKPT：只有当数据库有许多数据文件，LGWR在检查点时明显的降低性能才使CKPT运行。
o	SMON：对有故障的CPU或实例进行实例恢复
o	PMON：对有故障的用户进程进行恢复
o	RECO：分布式应用当中所使用的一个进程，自动解决在分布式事务中的故障
o	ARCH：当数据库运行在归档状态下（初始化参数LOG_ARCHIVE_START=TRUE），该进程才会被启动，最大进程数目为10
o	LCKn：最大进程数目10个，用于实例 间的封锁
o	Dnnn：Oracle服务器可被配置为多线程服务器，以支持多个用户进程，网络监听器进程（不是Oracle实例的组成部分）监听到的用户请求发送给一个Dnnn进程（对每种网络协议至少建立一个Dnnn进程），如果不能将客户应用连接到Dnnn进程，网络监听器进程将启动一个专用服务器进程来服务该用户进程。
•	启动数据库服务器（实例）：数据库启动分为3个过程：数据库相关的例程启动；例程配置（Mount）数据库；和数据库被打开。下面的例子中我们通过SQL*PLUS启动数据库，首先启动SQL*PLUS
o	将数据库完全启动即被打开 ， startup 
o	启动到NOMOUNT状态, startup nomount，此时与数据库相关的例程被启动了
o	启动到MOUNT状态，startup mount，此时例程读取了控制文件信息，但数据库没有被打开，只可以查询某些V$视图，但不能查询任何以DBA为起首的视图
o	启动数据库到RESTRICTED状态，startup restrict,当前只有restricted session 系统权限的用户才可以连接到数据库。一般进行数据库维护时刻使用此种启动方式
o	启动数据库到READONLY状态，startup read only, 默认方式为READ WRITE状态。
o	Startup recover用来启动数据库进行数据库恢复
o	Startup force命令将重启数据库例程
•	关闭数据库服务器（实例）：也分3个过程，关闭数据库、例程写在数据库、例程关闭。当你发出关闭数据库命令后，Oracle把重做日志缓冲区的内容写到日志文件里，同时写数据缓冲区到数据文件，并且关闭数据文件和日志文件。当数据库关闭后，例程脱离数据库，控制文件在此时被关闭。内存和后台进程仍处于活跃状态。最后例程关闭，释放内存。
o	Shutdown Normal 或shutdown，默认方式，只要过程为：1）不允许新的用户连接2）等待所有用户脱离数据库，所有用户可以继续工作3）数据库关闭，所有用户脱离数据库。此方法关闭比较慢，一般不采用
o	Shutdown immediate，1）不允许新用户进行连接2）中止所有用户的链接3）回滚所有为提交的事务4）数据库关闭，所有用户脱离数据库
o	Shutdown transaction，1）不允许新用户进行连接2）禁止所有新事务发生，当用户试图建立新事务时，会话被中止。3）等待用户回滚或提交任何未提交 的事务4）数据库关闭，所有用户脱离数据库
o	Shutdown abort，1）中止所有的当前SQL语句2）中断所有用户连接3）立即结束例程4）将要回滚所有未提交的信息，例程重启后需要恢复，此过程一般由ORACLE自动完成，一般情况下不要使用此命令，除非当前3种命令均无法关闭全程时才考虑使用
o	通过Oracle企业管理器（图形化方式）来实现数据库服务器的启动与关闭
•	配置系统初始化参数：系统初始化参数是系统启动时各个参数的配置情况，它的配置情况可通过查询$ORACLE_HOME/ADMIN/ORACLE_SID/PFILE/INIT.ORA文件得到。也可以通过 Oracle企业管理器来查看系统初始化参数，如果需要修改哪个参数，直接单击在管理器中单击对应的区域，然后输入新值，单击“应用”按钮完成初始化参数的修改。

Oracle数据字典
数据字典是Oracle存放数据库实例
数据表字段：syscolumns
数据对象： sysobjects
数据类型： systypes
字段属性信息： sysproperties
表属性信息： sysproperties
注释信息： syscomments
索引中的键或列的信息： sysindexkeys
数据库索引表： sysindexes
本语句包括了表、字段、字段类型、字段长度、是否为空、是否递增字段、索引名称、索引的定位、索引类型、主键、外键等；通过合理的裁剪可以很方便的生成相应的数据字典。
SELECT
  Sysobjects.name AS 表名称,
  --sysproperties.[value] AS 表说明,
  Syscolumns.name AS 字段名称,
  --properties.[value] AS 字段说明,
  Systypes.name AS 字段类型,
  Syscolumns.length AS 字段长度,
  ISNULL(COLUMNPROPERTY(syscolumns.id,syscolumns.name,’Scale’),0) AS 小数位数,
  CASE WHEN syscolumns.isnullable=0 
      THEN ‘N’   
      ELSE ‘Y’ 
END AS 是否为空，
CASE WHEN syscomments.text IS NULL
THEN ‘’
ELSE syscomments.text
END AS 缺省值,
CASE WHEN COLUMNPROPERTY(syscolumns.id,syscolumns.name,’IsIdentity’) = 1
THEN ‘Y’
ELSE ‘N’
END AS 递增字段,
CASE WHEN sysindexes.name IS NULL
THEN ‘’
ELSE sysindexes.name
END AS 索引名称,
CASE WHEN sysindexkeys.keyno IS NULL
THEN ‘’
ELSE CONVERT(VARCHAR(10),sysindexkeys.keyno) 
END AS 索引位置,
CASE WHEN sysindexes.indid=1
THEN ‘聚集索引’
WHEN sysindexes.indid>1 AND sysindexes.indid <>255
THEN ‘非聚集索引’
WHEN sysindexes.indid IS NULL
THEN ‘’
ELSE ‘其他’
END AS 索引类型,
CASE WHEN EXISTS
(SELECT 1 FROM sysobjects WHERE xtype =’PK’ and name IN 
(SELECT name FROM sysindexes WHERE indid IN
    (SELECT indid FROM sysindexkeys WHERE id=syscolumns.id AND colid=syscolumns.colid)))
THEN ‘Y’
ELSE  ‘N’
END AS 主键,
CASE WHEN sysforeignkeys.constid IS NULL 
THEN ‘N’
ELSE  ‘Y’
END AS 外键,
FROM  syscolumns   INNER JOIN sysobjects ON sysobjects.id = syscolumns.id 
             INNER JOIN systypes ON syscolumns.xtype = systypes.xtype 
             LEFT OUTER JOIN sysproperties properties ON syscolumns.id = properties.id 
                    AND syscolumns.colid = properties.smallid
             LEFT OUTER JOIN syscomments ON syscolumns.cdefault = syscomments.id
             LEFT OUTER JOIN sysindexkeys ON sysindexkeys.id = syscolumns.id
                    AND sysindexkeys.colid = syscolumns.colid
             LEFT OUTER JOIN sysindexes ON sysindexes.id = sysindexkeys.id
                    AND sysindexes.indid = sysindexkeys.indid
              LEFT OUTER JOIN sysforeignkeys ON sysforeignkeys.fkeyid=syscolumns.id
                    AND sysforeignkeys.fkey = syscolumns.colid
WHERE (sysobjects.xtype = ‘U’)
ORDER BY sysobjects.id,syscolumns.colid

我不知道表名，知道列名 ，知道列里面有内容是‘xiaoming’
如何能把这个表的表名查找出
要找的不是字段名为xiaoming的，某个字段里面的内容 叫xiaoming，
比如 字段名 为NAME 的字段下 内容 为 xiaoming  的所有的表
分享到： 
2013-09-23 13:53 
提问者采纳 
1
2
3
4
5
6
7	--我这里说的字段名跟列 是同一个意思
 
select column_name,
table_name,data_type ,data_length,data_precision,data_scale from user_tab_columns where column_name='字段名'; 
--根据字段名查出相关的表名出来。记录下来
--然后对查出来的表进行查询，找到含这内容字段的表
select  * from 表名  where 字段名='xiaoming'
追问：
我查出来的有800多个表，有没有什么方法能更便捷一些 能查出来 内容 包含‘xiaoming’的
追答：
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25	declare
l_cnt    varchar2(20);
v_sql  varchar2(4000);
v_tablename varchar(200);
  cursor cursor_jsdx is select 'select count(*) from ' || table_name || ' where NAME=''xiaoming''',table_name from user_tab_columns where column_name='NAME';
  --注：这里的字段名要大写 
begin
     open cursor_jsdx;
      
      
     Loop
            fetch cursor_jsdx into v_sql,v_tablename;
            exit when cursor_jsdx%notfound;
            execute immediate v_sql       into l_cnt;           
         if l_cnt >0 then   
         ---如果该表有那内容的就打印那个表的名字。
            dbms_output.put_line(v_tablename);
         end if;       
 
     end loop;
      
    Close cursor_jsdx;
 
 
end;

select obj.name from syscolumns col,sysobjects obj where col.id = obj.id and col.name like '%TH_STRUCT%'

第二部分 操作Oracle
关系数据库概念与 Oracle的安装
    关系术语
关系(Relation)	表
关系名	表名
元组(Tuple)	记录或行
属性(Attribute)	列
属性名	列名
属性值	列值
域(Domain)	属性的取值范围
关键字或码(Key)	表中的某个属性组，它可唯一确定一个元组
分量(Component)	一行记录中的一个列值
关系模式（Relation schema）	对关系的描述，准确描述一个schema需要5部分信息，形式化表示为R(U,D,dom,F)  R关系名，U属性名集合，D属性的域，dom属性向域的映射的集合，F属性之间数据的依赖关系集合
规范化	关系模型必须满足的规范条件，例如其中最基本的一条就是“关系的每一个分量必须是一个不可分的数据项，也就是说不允许表中还有表”，还有其他规范，见后续章节
非规范化	如：表中有表（大表中嵌套有小表）

    关系模型的特点
关系必须规范化	关系模型中每一个关系模式都必须满足一定的要求
模型概念单一	万物皆关系，即无论实体还是实体之间的联系都用关系来表示，对数据检索和更新的结果也是关系，所以其数据结构简单、清晰、易于理解和使用
集合操作	关系操作的对象都和结果都是集合，称为一次一集合，而非关系数据模型的数据操作方式则为一次一记录的方式

优点	缺点
1.	关系模型与非关系模型不同，它是建立在严格的数学概念的基础上的。
2.	关系数据模型的存取路径对用户透明，从而具有更高的数据独立性、更好的安全保密性，也简化了程序员的工作
3.	概念单一，易于理解和使用	1．关系模型由于存储路径对用户透明，查询效率往往不如非关系数据模型。因此为了提高性能，必须对用户的查询请求进行优化，这增加了开发数据库管理系统的难度。
    


•	关系操作：操作对象和结果都是集合。关系操作包括：查询操作（包括选择、投影、并、差、笛卡尔积5种基本查询操作，和其他如连接、除、交等导出操作）和插入操作、删除操作和修改操作。关系操作早期是用代数方式和逻辑方式表示，分别被称为关系代数和关系演算。关系代数是用对关系的运算来表达查询要求的方式。关系演算师用谓词来表达查询要求的方式。这两种方式都是抽象的查询语言。实际的关系数据库系统中，为了实现关系操作，为用户提供了一种介于关系代数和关系演算之间的语言SQL，是关系数据库的标准语言。例如Oracle数据库系统中的 SQL*Plus， SQL Server中的 Transact_SQL
•	关系的完整性（正确性）：
1. 实体完整性：基本关系的所有元组的主键属性不能取空值，即数据库表格的主键不能为空，当数据库表格采用复合主键时，这些组成主键的所有列的值都不能为空。所谓空值NULL就是“不知道”或者“不存在”的值。基本表对应现实世界中一个实体集，由于现实世界中实体是可区分的，即每个实体有唯一可确定的标识，所以基本关系中的每个元组的主键也必须唯一可确定即不能为NULL 
2.参照完整性：现实世界中，实体之间因此需要互相引用来体现彼此之间存在联系，映射到关系数据模型中就是关系与关系之间的引用，在关系模型中通过外键来表达关系之间的相互引用。
参照完整性规则：若属性（或属性组）F是基本关系R的外键，它与基本关系S的主键K相对应（基本关系R和S不一定是不同的关系），则对于R中每个元组在F上的值必须为如下两种情况之一：空值（F的每个属性值均为空值）或等于S中某个元组的主键值。
3.用户定义的完整性：实体完整性和参照完整性是任何关系型数据库都必须支持的，被称为关系的两个不变性，因此由关系数据库系统自动支持。此外不同关系数据库系统根据其应用环境的不同，往往还需要一些特殊的约束条件，来反映某一具体应用所涉及的数据必须满足的语义要求。
•	关系的规范化（暂略，参考1.3）
•	数据库设计（暂略，参考1.4）
•	Oracle应用系统结构

单磁盘独立主机结构：只有一个实例

ORACLE事务管理
第三部分 ORACLE备份与恢复

第四部分 设计（关系型）数据库

