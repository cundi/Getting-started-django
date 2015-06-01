# Getting-started-Django
中文名：《Django入门》  
英文原版地址：https://www.packtpub.com/web-development/getting-started-django  
作者：Samuel Dauzon  
出版日期：June 2014  
特色：Develop simple web applications with the powerful Django framework.  
级别：Starting  

***************

# 第五章预览 使用模型

我们刚刚创建的网站只包含静态文件；然而，我们想要做的是存储数据，
像这样去自动完成所有任务。这就是为什么会有模型的存在，它们是放在
我们的视图和数据之间的一个链接。  

像很多的框架一样，Django提出带有一个抽象层的数据库访问。
该抽象层被称作对象关系映射（ORM）。它允许你使用Python实现对象以访问数据
而不用担心数据库的使用。对于这个ORM，我们不需要为了简单、稍复杂的动作
使用SQL查询。该ORM属于Django，但是还有别的比如SQLAlchemy，它是一个
高质量的ORM特别是用于Python TurboGears框架。 

模型是一个继承自Model类的对象。Model类是一个Django特别为数据持久保留
而设计的类。

我们在模型中定义字段。这些特性允许我们在一个模型内组织数据。为了让数据库和
SQL之间做到连接，我们可以说模型是数据库中一个表的体现，一个模型的属性是这个
表中字段的体现。  

在本章，我们将讲解：  
```
如何建立对数据库的访问  

如何为数据库迁移安装South  

如何创建一个简单的模型  

如何创建两个模型之间的关系  

如何扩展我们的模型  

如何使用管理模块  
```


## 数据库和Django
Django can interface with many databases. However, during the development of our
application, we use SQLite libraries that are included in Django.
We will modify settings.py to set our connection to the database:  

```python
DATABASES = {
'default': {
'ENGINE': 'django.db.backends.sqlite3',
'NAME': os.path.join(PROJECT_ROOT, 'database.db'),
'USER': '',
'PASSWORD': '',
'HOST': '',
'PORT': '',
}
}
```
  
The following is the description of the properties mentioned in the preceding code:  

```
•	 The ENGINE property specifies the type of database to be used.
•	 The NAME property defines the path and final name of the SQLite database. We
use a syntax using os.path.join to our code, and it is compatible with all
operating systems. The file's database will be contained in the project directory.
•	 The other properties are useful when we use a database server, but as we will
use SQLite, we do not need to define them.
```
  
## Migrations with South
South is a very useful extension of Django. It facilitates the migration of the database
when changing fields. It also keeps a history of the changes in the structure of
the database.  

We talk about it now because it must be installed before the creation of the database
to work correctly.  

Django 1.7 incorporates a migration system. You will not need to use South anymore
to make the migration of a Django application. You can find more information about
the migration systems integrated into Django 1.7 at https://docs.djangoproject.
com/en/dev/topics/migrations/ .  

## Installing South
To install South, we use the pip command. We have already used it to install Django.
To do this, run the following command:  

```python
pip install South
```
  
Before actually using South, we must change the settings.py file for South to be
well integrated in Django. To do this, you must go to INSTALLED_APPS and add the
following lines (depending on the version, it is possible that the installation of South
added the line):  

```python
'south',
'TasksManager',
```
  
## Using the South extension
Before we make our first migrations and generate our database, we also have to
create the schema migration. To do this, we must run the following command:  

```python
manage.py schemamigration TasksManager --initial
Then, we must perform an initial migration:
manage.py syncdb --migrate
```
  
Django asks us to first create an account. This account will be a superuser. Remember
the login and password that you enter; you will need this information later.  

South is now fully operational. Each time we need to modify the models, we will
make a migration. However, for the migration to be made correctly, you must keep
the following things in mind:  

```python
•	 Never perform the Django syncdb command. After running syncdb
--migrate for the first time, never run it again. Use migrate afterwards.
•	 Always put a default value in the new fields; otherwise, we will be asked to
assign a value.
•	 Each time we finish editing our models, we must execute the following two
```
  
commands in the correct order:  

```pytho
manage.py schemamigration TasksManager –auto
manage.py migrate TasksManager
```
 
### Tips 
>Django1.7版本用户已经不必再安装South进行迁移。

## 创建简单的模型
要创建模型，我们必须已经深入学习过应用。模型是任何应用的基础因为它们
都将存储所有的程序。因此，我们必须认真地准备它们。  

关于我们的Tasksmanager程序，我们需要在一个项目上面保存task实现的用户。
我们将创建两个模型：User _ django和Project。  


我们需要在models.py文件中存储模型。我们将编辑TasksManager文件夹中的
models.py文件。我们不需要修改配置文件，因为，当我们需要模型时，我们
不得不导入它。 

已经存在的文件有一行。下面的行允许你导入Django的基本模型： 

```python
from django.db import models
```
  

## UserProfile模型
为了创建UserProfile模型，我们会问我们自己一些问题：“我们需要保存用户的
什么数据？”。我们需要下面的数据：

```
用户的真实姓名  

用于识别每个用户的昵称  

一个对用户认证非常有用的密码  

电话号码  

出生日期（该项不是基本的要求，但是我们必须学习使用日期！）  

最后一次连接的日期和时间  

Email地址  

年龄（按年份） 

用户账户的创建日期  

一个特别的账户，如果它是supervisor  

用户的类型  

一个管理者，如果你是一个开发者  
```
  

所需要的模型如下：  

```python
class UserProfile(models.Model):
    name = models.CharField(max_length=50, verbose_name="Name")
    login = models.CharField(max_length=25, verbose_name="Login")
    password = models.CharField(max_length=100, verbose_name="Password")
    phone = models.CharField(max_length=20, verbose_name="Phone number", null=True, default=None, blank=True)
    born_date = models.DateField(verbose_name="Born date" , null=True,
default=None, blank=True)
    last_connection = models.DateTimeField(verbose_name="Date of lastconnection" , null=True, default=None, blank=True)
    email = models.EmailField(verbose_name="Email")
    years_seniority = models.IntegerField(verbose_name="Seniority",default=0)
    date_created = models.DateField(verbose_name="Date of Birthday",auto_now_add=True)
```
  

我们没有去定义特别类型的用户和管理者，因为这些部分将在下一部分见到。 

在之前的代码，我们可以看到Django_user继承自Model类。 
该模型类有我们需要的全部操控模型的方法。我们也可以忽略这些方法去定制
模型的使用。 

在这个类中，我们添加了一个带有我们指定值的属性字段。例如，第一个名称
字段是一个最大50字符长度的字符字符串类型。verbose_name属性将成为定义表单中
的字段的标签。下面是一个常用的字段类型列表： 

```
这是一个限制字符数量的字符串类型字符。
•	 TextField : This is a character string with unlimited characters
这是一个不限制字符的字符串类型字符。
•	 IntegerField : This is an integer field
这是一个整数字段。
•	 DateField : This is a date field
这是一个日期字段
•	 DateTimeField : This field consists of the date as well as the time in hours,
minutes, and seconds
该字段由不仅包含时、分还有秒组成的日期。
•	 DecimalField : This is a decimal number that can be defined precisely
这是一个可以被精确定义的十进制数字
```
  
  
Django自动地保存一个自增加的id字段。因此，我们不需要去定义一个主键。 


## Project模型
To save our projects, we will need the following data:  

```
要保存项目，我们需要下列内容：
•	 Title
标题
•	 Description
描述
•	 Client name
客户名称
```
  
  
因此这允许我们定义下列模型： 

```python
class Project(models.Model):
    title = models.CharField(max_length=50, verbose_name="Title")
    description = models.CharField(max_length=1000, verbose_name="Description")
    client_name = models.CharField(max_length=1000, verbose_name="Client name")
```
  

要遵守好的惯例，除了定义到客户表的一个关系，我们还将给customer定义一个文本字段。为了简化我们的第一个模型，我们要为客户名称定义一个文本字段。  


## 模型之间的关系
关系是连接模型的元素。例如，这个程序的例子中，一个task链接到了一个project。
确实，除非程序是一个更普通的task，开发者为一个特殊的project实现一个task
，不过这超出了我们项目的范围。  


这里有另外两种关系：  

```
•	 The one-to-one relationship sets apart a model in two parts. The resulting
database will create two tables linked by a relationship. We will see an
example in the chapter on the authentication module.

•	 The many-to-many relationship defines relationships with any model that
can be connected to several other models of the same type. For example, an
author can publish several books and a book may have several authors.
Creating the task model with relationships
```
  


对于task模型，我们需要下面的元素：  

```
•	 A way to define the task in a few words
一种用少量词语定义task的方法
•	 A description for more details about the task
关于task的更加详细描述
•	 A past life
•	 Its importance
它的重要性
•	 The project to which it is attached

•	 The developer who has created it
```
  


这让我们可以写出下面的模型：

```python
class Task(models.Model):
    title = models.CharField(max_length=50, verbose_name="Title")
    description = models.CharField(max_length=1000, verbose_name="Description")
    time_elapsed = models.IntegerField(verbose_name="Elapsed time" ,null=True, default=None, blank=True)
    importance = models.IntegerField(verbose_name="Importance")
    project = models.ForeignKey(Project, verbose_name="Project" , null=True, default=None, blank=True)
    app_user = models.ForeignKey(UserProfile, verbose_name="User")
```
  
In this model, we have defined two foreign key field types: project and app_user .
In the database, these fields contain the login details of the record to which they are
attached in the other table.
在这个模型中，我们定义了两个外键字段类型：project和app_user。
数据库中，这些字段包含
The project field that defines the relationship with the Project model has two
additional attributes:  

```
project字段定义了Project模型两个额外的属性的关系。
•	 Null : This decides whether the element can be defined as null. The fact
that this attribute is in the project field means that a task is not necessarily
related to a project.
Null：这个属性决定元素是否可以被定义为null。
•	 Default : This sets the default value that the field will have. That is, if we do
not specify the value of the project before saving the model, the task will not
be connected to a domain.
```
  


## 扩展模型
继承模型允许对两个不同的模型公用字段的使用。例如，在我们的App_user模型
中，我们不能确定随机记录是否为开发者或者管理者。 

一个解决方案将创建两个不同的模型，但是我们不得不重复如下所有的公有字段比如name，username，和password： 

```python
class Supervisor(models.Model):
# Duplicated common fields
重复的共有字段
    specialisation = models.CharField(max_length=50, verbose_name="Specialisation")

class Developer(models.Model):
# Duplicated common fields
重复的共有字段
    supervisor = models.ForeignKey(Supervisor, verbose_name="Supervisor")
```
  

重复代码是可耻的，但是Django和DRY的原则也是不得不遵循的。这就是
有一个继承模型的原因。

确实早前的模型被用于定义一个包含到多个模型的共有字段的主模型（或者超级模型）。子模型子动地继承超级模型的字段。 

没有什么比一个例子来的清晰，我们将定义类——开发者和管理者，让它们继承App_ueser:  

```python
class Supervisor(UserProfile):
    specialisation = models.CharField(max_length=50, verbose_name="Specialisation")

class Developer(UserProfile):
    supervisor = models.ForeignKey(Supervisor, verbose_name="Supervisor")
```
  

早前的数据库结果允许我们创建三个表：

```
•	 A table for the App_user model that contains the fields for the properties of
the model
一张含有模型属性字段的app_user模型的表。
•	 A table for the Supervisor model, with a text field for specialization and a
field that has a foreign key relationship with the App_user table
一张含有一个专门化的文本字段和一个包含app_user的外键关系字段的表。
•	 A Developer table with two fields: a field in liaison with the Supervisor
table and a field that links to the App_user table
一个含有两个字段的Developer表：一个使用Supervisor联络的字段以及一个链接到
app_user表的字段。
```
  

现在我们已经分隔了两种用户类型，我们会使用app_user修改关系，因为只有
developer才会记录他或者她的task。在Tasks模型里，我们有以下行：

```python
app_user = models.ForeignKey(App_user, verbose_name="User")
```
  
This code is transformed as follows:  

```python
developer = models.ForeignKey(Developer, verbose_name="User")
```
  

为了数据库的生成按顺序来工作，我们必须以正确的顺序放置模型。
确实，如果我们使用一个还未被定义的模型来定义一个关系，Python将抛出异常。
此时，模型将按照所描述的顺序被定义。之后，我们将见到如何解决这种局限性。 

在接下来的章节里，我们将对模型执行查询。该操作要求用模型来同步。 
首先在开始下一章之前，我们必须迁移South。

要执行迁移，我们必须使用我们在本章开始见过的命令。为了简化迁移，我们在Python
文件夹内也可以创建一组文件，我们使用下面的行： 

```python
manage.py schemamigration TasksManager --auto
manage.py migrate
pause
```
  

下面是一个让你可以在Work_manager文件夹内基于Debian Linux执行相同的动作的bash脚本：

```python
#!/bin/bash
manage.py runserver 127.0.0.1:8000
```
  
当你迁移South时，该方法将执行这个文件。暂停命令允许你看到结果或者错误消息而不用关闭窗口。


## admin模块
The administration module is very convenient and is included by default with Django.
It is a module that will maintain the content of the database without difficulty. This is
not a database manager because it cannot maintain the structure of the database.  

管理模块非常体贴，而且默认被Django使用。它是一个可以轻松地管理
数据库内容的模块。这不是一个数据库管理器因为它不能维护数据库的结构。 

One question that you may ask is, "what does it have other than a management tool
database?". The answer is that the administration module is fully integrated with
Django and uses these models.  

你或许会问的一个问题是，“比起管理数据库工具它还有什么？”。
答案是管理模块完全地集成于Django并使用这些模块。

下面是它的作用： 

```
•	 It manages the relationships between models. This means that if we want to
save a new developer, the module will propose a list of all the supervisors.
In this way, it will not create a non-existent relationship.
它管理模型之间的关系。这就是说如果我们想保存一个新的developer，模块将
计划做出一个含有所有supervisor的列表。这样，它就不会创建一个不存在的
关系。
•	 It manages Django permissions. You can set permissions for users according
to models and CRUD operations.
它管理Django权限。你可以为用户配置相对的模型和CRUD操作的权限。
•	 It is quickly established.
它可以很快地被建立。
Being based on Django models and not on the database, this module allows the user
to edit the recorded data.
基于Django模块而不基于数据库，此模块允许用户编辑已经记录的数据。
```
  


## 安装模块
为了执行管理模块，编辑settings.py文件。在INSTALLED_APPS中你需要
添加到或者注释掉下面的行： 

```python
'django.contrib.admin'
```
You also have to edit the urls.py file by adding or uncommenting the following
lines:
你也得通过添加或者注释来下面的行编辑urls.py文件

```python
from django.contrib import admin
admin.autodiscover()

url (r'^admin', include(admin.site.urls)),
```
  
The line that imports the administration module has to be at the beginning of the file
with other imports. The line that runs the autodiscover() method must be found
after the imports and before the urlpatterns definition. Finally, the last line is a
URL that should be in urlpatterns .  

导入管理模块的那行必须和其他的导入处在文件的起始处。运行autodiscover()方法的
的那行必须在urlpatterns定义之前import之后。最终，最后的行是一个应该位于urlpatterns的URL。 

We also have to create an admin.py file in the TasksManager folder in which we will
define the styles we want to integrate into the management module:  

我们也必须在我们将定义想要集成到管理模块的风格的
那个文件夹内创建一个admin.py文件： 

```python
from django.contrib import admin
from TasksManager.models import UserProfile, Project, Task , Supervisor , Developer
admin.site.register(UserProfile)
admin.site.register(Project)
admin.site.register(Task)
admin.site.register(Supervisor)
admin.site.register(Developer)
```
  

现在我们已经配置了管理模块，我可以轻松地管理数据了。


## 使用模块
为了使用管理模块，我们必须连接到刚才我们定义过的URL：http://localhost:8000/admin/。
We must connect with the logins defined when creating the database:  

```
当创建数据库时我们必须用定义过的登录来连接：
1.	 Once we are connected, the model list appears.
只要我们已经连接上，模型列表就会出现。
2.	 If we click on the Supervisor model link, we arrive at a page where we can
add a supervisor by using the button at the top-right corner of the window:
如果我们点击Supervisor模型链接，我们就到达了一个页面的可以使用窗口的右上角添加一个supervisor地方：
 
3.	 By clicking on this button, we load a page consisting of a form. This form
automatically provides practical tools to manage dates and times:
通过点击这个按钮，我们载入了一个表单组成的页面。这个表单自动地提供实用工具管理日期和时间。
```
 
Let's add a new supervisor and then add a developer. When you want to choose the
supervisor, you can see the one we have just created in a combobox. The green cross
on the right-hand side allows you to quickly create a supervisor.  

让我们在添加一个developer时添加一个新的supervisor。当你想要选择supervisor时，
你可以在下列列表框中看到我们刚刚创建的那个。在右手边的路色十字允许你快速地创建一个supervisor。  

In the following chapter, we will define the str method for our models. This will
improve the display lists of objects in this management module.  

在下面的章节，我们会为模型定义str方法。该方法提高在管理模块中的对象的显示列表。


## 模型的高级用法
We studied the basics of the models that allow us to create simple applications.
Sometimes, it is necessary to define more complex structures.  

我们已经研习了模型的基础，它允许我们创建简单应用。有时候，它也是定义更复杂结构所必须的。 


## 对相同的模型使用不同的关系
Sometimes, it is useful to store two foreign keys (or more) in a single model. For
example, if we want two developers to work in parallel on the same task, we must
use the related_name property in our models. For example, our Task model
contains two relationships with the following lines:  

有时候，在一个单一模型中存储两个外键（或者更多）是非常有用的。例如，
如果我们想要两个developer并行的工作在相同的任务上，我们必须在模型中使用related_name属性。例如，下面的行中Task模型包含两个关系：

```python
developer1 =related_name
developer2 =related_name
models.ForeignKey (Developer , verbose_name = "User" ,= "dev1" )
models.ForeignKey (Developer , verbose_name = "User" ,= "dev2" )
```
  
Further in this book, we will not use these two relationships. To effectively follow
this book, we must return to our previously defined Task model.  

进一步来说在这本书中，我们不会使用这两个关系。为了更有效率地遵循这本书，
我们必须返回到我们之前定义的Task模型。 

Here, we define two developers on the same task. Best practices advise us
to create a many-to-many relationship in the Task model. The thorough
argument allows you to specify an intermediate table to store additional
data. This is an optional step. An example of this type of relationship is as
follows:  

这里我们在相同的task上定义两个developer。最佳实践建议我们在Task模型里创建一个多对多关系。详尽的参数允许你定义一个中间的表存储额外的数据。
这是可选的步骤。此种类型的关系的一个例子如下：

```python
#Relationship to add to the Task model
添加到Task模型的关系
developers = models.ManyToManyField(Developer ,through="DeveloperWorkTask")

class DeveloperWorkTask(models.Model):
    developer = models.ForeignKey(Developer)
    task = models.ForeignKey(Task)
    time_elapsed_dev = models.IntegerField(verbose_name="Time elapsed", null=True, default=None,blank=True)
```
  

## 定义str方法
As already mentioned in the section on the use of the admin module, the __str__()
method will allow a better view of our models. This method will set the string that
will be used to display our model instance. When Django was not compatible with
Python 3, this method was replaced by the __unicode__() method.  

一如本节admin模块的使用中所提到的，str方法允许模型的一个最佳视图。
该方法将设置用户显示模型实例的方法。当Django不兼容Python3时，该方法
被__unicode__()方法替代。 

For example, when we added a developer, the drop-down list that defines a
supervisor showed us the "Supervisor object" lines. It would be more helpful to
display the name of the supervisor. In order to do this, change our App_user class
and add the str() method:  

例如，当我们添加了一个developer，下拉列表定义一个supervisor向我们展示"Supervisor object"行。这对于显示supervisor的名称很有这帮助。为了完成这个操作，
改变我们的app_user类，并添加str()方法： 

```python
class UserProfile ( models.Model ) :
# Fields...字段
    def __str__ (self):
       return self.name
```
  
This method will return the name of the supervisor for the display and allows you to
manage the administration easily:  

该方法将返回需要显示的supervisor的名字并允许你轻松地处理administration：
 

## 总结
In this chapter, we learned migration with South. We also learned how to create
simple models and relationships between the models. Furthermore, we learned
how to install and use the admin module. In the next chapter, we will learn how
to manipulate our data. We will learn how to use four main operations on the data:
adding, reading (and research), modification, and deletion.  

于此章中，我们学会了用South迁移。我们也学会了如何创建简单模型以及
两个模型中的关系。此外，我们学会了如何安装和使用admin模块。在下一章，
我们将学到如何操控数据。我们将学到如何使用4个数据上的主要操作：
添加，读取（和搜索），修改，以及删除。
