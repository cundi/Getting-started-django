6 Getting a Model's Data with Querysets
6 用Querysets取回模型的内容

Querysets are used for data retrieval rather than for constructing SQL queries
directly. They are part of the ORM used by Django. An ORM is used to link the view
and controller by a layer of abstraction. In this way, the developer uses object model
types without the need to write a SQL query. We will use querysets to retrieve the
data we have stored in the database through models. These four operations are often
summarized by CRUD (Create, Read, Update, and Delete).
Querysets用于数据检索而不是直接地构建SQL查询。它们是用于Django ORM的一部分。ORM被用于通过一个抽象层链接到视图和控制器。
借此，开发者不必写SQL查询就可以使用对象模型类型。我们将通过模型去使用querysets，重新取回存储在数据库中的数据。
这四个操作常总结为CRUD（创建，读取，更新，和删除）。

The discussed examples in this chapter are intended to show you how the querysets
work. The next chapter will show you how to use forms, and thus, how to save data
sent from a client in the models.
这章所讨论的例子打算将向你演示是querysets如工作的。下一章将向你演示如何使用表单，就此，如何在模型中
保存发送自客户端的数据。

By the end of this chapter, we will know how to:
•	 Save data in the database
•	 Retrieve data from the database
•	 Update data from the database
随着本章的结束，我们将了解如何做到以下事情：
在数据中句保存数据
由数据库中重新获取数据
从数据库更新数据


The persisting model's data on the database
保留数据库上的模型数据
Data storage is simple with Django. We just need to fill the data in the models,
and use methods to store them in a database. Django handles all the SQL queries;
the developer does not need to write any.
Django的数据存储是很简单的。我们只需要填充模型中的数据，使用方法将它们保存在一个数据库中。
Django处理所有的SQL查询，而开发者便再也不需去写了。

Filling a model and saving it in the database
填充模型并将之存于数据库
Before you can save data from a model instance to the database, we need to define
all the values of the model's required fields. We can show the examples in our
view index.        
在你从一个模型实例保存到数据库之前，我们需要定义模型所需字段的全部的值。我们在索引视图中演示这个例子。
The following example shows how to save a model:
下面的例子演示了如何保存一个模型：
from TasksManager.models import Project # line 1第一行
from django.shortcuts import render
def page(request):
   new_project = Project(title="Tasks Manager with Django",
   description="Django project to getting start with Django easily.",
   client_name="Me") # line 2第二行
   new_project.save() # line 3第三行
return render(request, 'en/public/index.html', {'action':'Save datas of model'})
We will explain the new lines of our view: 
我们来解释视图中的新行：
	We import our models.py file; it's the model that we will use in the view  
	我们导入models.py文件，它是我们将用在视图中的模型
	We then create an instance of our Project model and fill it with data  
	然后，我们创建Project模型的实例并用数据填充
	Finally, we execute the save() method that saves the present data in the instance  
	最终，我们执行在实例中保存当前数据的save()方法
We will test this code by starting the development server (or runserver) and then go to our URL. In the render() method, the value that we defined in the action variable is displayed. To check if the query is executed, we can use the administration module. There is also the software for managing databases.  We need to add more records by changing the values randomly in line 2. To find out how to do this, we'll need to read this chapter.  
我们会通过启动开发服务器（或者 runserver）测试此代码，然后转到URL。
在render()方法中，我们定义过的action变量的值用于显示。要检查查询是否被执行，
我们可以使用管理模块。这里也有软件用于管理数据库。在第二行我们需要通过
随机地改变值来添加更多的记录。要弄清楚如何做到，我们需要阅读这一章。
Getting data from the database  Before using Django to retrieve data from a database, we were using SQL queries to retrieve an object containing the result. With Django, there are two ways to retrieve records from the database depending on whether we want to get back one or several records.  
Getting multiple records 
取得多个记录
 To retrieve records from a model, we must first import the model into the view as we have done before to save the data in a model.  
要重新从一个模型取回记录，我们必须首先导入模型到视图里
We can retrieve and display all the records in the Project model as follows: 
如下，在Project模型中我们可以重新取回并显示所有的记录：
from TasksManager.models import Project
from django.shortcuts import render
def page(request):
   all_projects = Project.objects.all()
   return render(request, 'en/public/index.html', {'action': "Displayall project", 'all_projects': all_projects})

The code template that displays the projects becomes:
显示项目的模板代码可以是：
{% extends "base.html" %}
{% block title_html %}
Projects list
{% endblock %}
{% block h1 %}
Projects list
{% endblock %}
{% block article_content %}
<h3>{{ action }}</h3>
{% if all_projects|length > 0 %}
<table>
<thead>
<tr>
<td>ID</td>
<td>Title</td>
</tr>
</thead>
<tbody>
{% for project in all_projects %}
<tr>
<td>{{ project.id }}</td>
<td>{{ project.title }}</td>
</tr>
{% endfor %}
</tbody>
</table>
{% else %}
<span>No project.</span>
{% endif %}
{% endblock %}
The all() method can be linked to a SQL SELECT * FROM query. Now, we will use
the filter() method to filter our results and make the equivalent of a SELECT *
FROM Project WHERE field = value query.
all()方法可以被链接到一个SQL SELECT * FROM查询。现在我们可以使用filter()方法去过滤结果，计算SELECT * FROM 项目 WHERE 字段=值 查询。
The following is the code to filter model records:
下面是过滤模型的代码内容：

from TasksManager.models import Project
from django.shortcuts import render
def page(request):
    action='Display project with client
    name = "Me"'
    projects_to_me = Project.objects.filter(client_name="Me")
    return render(request, 'en/public/index.html', locals())
We used a new syntax to send the variables to the template. The locals() function
sends all the local variables to the template, which simplifies the render line.
我们用一个新的语法把变量发送到模板。local()函数发送所有的本地变量到模板，这将简化render所在的行。
Best practices recommend that you pass the variables one by one
and only send the necessary variables.
实践经验推荐我们要按次序传递变量而且只发送必要的变量。
Each argument from the filter() method defines a filter for the query. Indeed, if
we wanted to make two filters, we would have written the following line of code:
每个filter()方法的参数都给query定义了一个过滤器。确实，若是我们
想要做两个过滤器，可以写出下面的一行代码：
projects_to_me = Project.objects.filter(client_name="Me", title="Project test")
This line is equivalent to the following:
这一行等价于下面内容：
projects_to_me = Project.objects.filter(client_name="Me")
projects_to_me = projects_to_me.filter(title="Project test")
The first line can be broken into two, because the querysets are chainable. Chainable
methods are methods that return a queryset such that other queryset methods can
be used.
开始的那行我们可以将其分为两个，因为querysets是链式的。链式方法是
返回一个querysets，而其他querysets方法可以使用它的方法。
The response obtained with the all() and filter() methods is of the queryset type. 
A queryset is a collection of model instances that can be iterated over.
响应获取带有all()和filter()的属于querys类型的方法。queryset是一个可以重复的模型实例。

Getting only one record
只取一个记录
The methods that we will see in this chapter return objects of the Model type, which
will be used to record relationships or to modify the instance of the model recovered.
在本章我们将见到的方法返回Model类型的对象，它将被用于记录关系或者修改恢复的模型的实例。
To retrieve a single record with a queryset, we should use the get() method as in the following line:
要重新取回带有queryset的单个记录，我们应该使用如下行所示的get()方法：
The get() method when used as the filter() method accepts filter arguments.
However, you should be careful with setting the filters that retrieve a single record.
get()方法用于filter()方法接受过滤器参数之时。然而，你在配置过滤器重新取回单个记录时应该小心一些。
If the argument to get() is client_name = "Me" , it would generate an error if we
had more than two records corresponding to client_name .
若传递到get()的参数是 client_name = "Me" ，而我们有超过两个记录去响应client_name ，它就会产生一个错误。

Getting a model instance from the queryset instance
从queryset取得一个模型实例
We said that only the get() method makes it possible to retrieve an instance of a
model. This is true, but sometimes it can be useful to retrieve an instance of a model
from a queryset.
我们说过只有get()方法才可以使重新取回一个模型的实例变得有可能。这是事实，不过有时候对于重新由一个queryset取回模型实例，它也是很有用的。
For example, if we want to get the first record of the customer Me , we will write:
例如，若我们想要取得客户Me 的第一个记录，我们会这样写：
queryset_project = Project.objects.filter(client_name="Me").order_by("id")
# This line returns a queryset in which there are as many elements as there are projects for the Me customer
# 此行返回一个Me客户的项目并带有很多元素的queryset

first_item_queryset = queryset_project[:1]
# This line sends us only the first element of this queryset, but this element is not an instance of a model
该行发送给我们其queryset的第一个元素，不过这个元素并不是一个模型的实例。

project = first_item_queryset.get()
# This line retrieves the instance of the model that corresponds to the first element of queryset
该行重新取回响应queryset第一个元素的模型实例。

These methods are chainable, so we can write the following line instead of the previous three lines:
这些方法时链式的，因此我们可以写出下面这行来替代之前的三行：
project = Project.objects.filter(client_name="Me").order_by("id")[:1].get()


Using the get parameter
使用get参数
Now that we have learned how to retrieve a record and we know how to use a URL,
we will create a page that will allow us to display the record of a project. To do this,
we will see a new URL syntax:
现在我们已经学会了如重新取回一个记录，而且也知道如何去使用URL，
我们将创建一个可以允许我们显示项目记录的一个页面。就此，我们将见识到一个新的URL语法：
url(r'^project-detail-(?P<pk>\d+)$','TasksManager.views.project_detail.page',name="project_detail"),
This URL contains a new string, (?P<pk>\d+) . It allows the URL with a decimal
parameter to be valid because it ends with \d . The + character at the end means that
the parameter is not optional. The <pk> string means that the parameter's name is pk .
该URL包含一个新的字符串，(?P<pk>\d+)。它允许一个十进制的URL参数变得有效，因为它以 \d 结尾。末尾的 + 字符意思是参数是必选的。<pk>字符串意思是参数的名称是pk。
The system routing Django will directly send this parameter to our view. To use
it, simply add it to the parameters of our page() function. Our view changes to the following:
系统到Django的路径将直接地发送该参数到我们的视图。为了使用它，我们简单地把它加到page()函数的参数。我们的视图就变为下面这样：
from TasksManager.models import Project
from django.shortcuts import render
def page(request, pk):
    project = Project.objects.get(id=pk)
    return render(request, 'en/public/project_detail.html', {'project' : project})
We will then create our en/public/project_detail.html template extended from
base.html with the following code in the article_content block:
我们之后将使用下面位于article_content 块中的代码，来创建扩展至base.html 的en/public/project_detail.html模板。
<h3>{{ project.title }}</h3>
<h4>Client : {{ project.client_name }}</h4>
<p>
{{ project.description }}
</p>
We have just written our first URL containing a parameter. We will use this later,
especially in the chapter about the class-based views.
我们刚刚写完第一个包含一个参数的URL。我们稍后使用它，特别是在基于类的视图那章。


Saving the foreign key
保存外键
We have already recorded data from a model, but so far, we have never recorded it
in the relationship database. The following is an example of recording a relationship
that we will explain later in the chapter:
我们已经记录了来自一个模型的数据，不过目前，我们从来没有在关系性数据库
中记录它。下面是一个我们将在本章稍后讲解的一个关系记录：
from TasksManager.models import Project, Task, Supervisor, Developer
from django.shortcuts import render
from django.utils import timezone
def page(request):
# Saving a new supervisor
保存一个新的supervisor
           new_supervisor=Supervisor(name="GuidovanRossum",login="python",password="password",last_connection=timezone.now(),email="python@python.com", specialisation="Python") # line 1 第一行
    new_supervisor.save()
# Saving a new developer
保存一个新的developer
      new_developer=Developer(name="Me",login="me",password="pass",last_connection=timezone.now(),email="me@python.com", supervisor=new_supervisor)
    new_developer.save()
# Saving a new task
保存一个新的task
    project_to_link = Project.objects.get(id = 1) # line 2第二行
    new_task = Task(title="Adding relation", description="Exampleof adding relation and save it", time_elapsed=2, importance=0,project=project_to_link, developer=new_developer) # line 3第三行
new_task.save()
    return render(request, 'en/public/index.html', {'action' : 'Save
relationship'})
In this example, we have loaded four models. These four models are used to create
our first task. Indeed, a spot is related to a project and developer. A developer is
attached to a supervisor.
这个例子中，我们已经载入了四个模型。这四个模型被用于创建我们的第一个task。的确，有一个地点已关联到了project和developer。developer被加进了supervisor。
Following this architecture, we must first create a supervisor to add a developer.
The following list explains this:
下面是架构，我们首先必须创建一个supervisor添加到developer。
下面列出的东西解释了原因：
•	 We create a new supervisor. Note that the extending model requires no
additional step for recording. In the Supervisor model, we define the
fields of the App_user model without any difficulties. Here, we use
timezone to record the current day's date.
我们创建一个新的supervisor。注意，扩展模型对于记录来说不要求额外的步骤。在Supervisor模型中，
没有任何难度地我们就定义了App_user模型。这里，我们使用时区来记录当前天时的日期。
•	 We look for the first recorded project. The result of this line will record
a legacy of the Model class instance in the project_to_link variable.
Only the get() method gives the instance of a model. Therefore, we
must not use the filter() method.
我们寻找第一个记录过的项目。这一行的结果将记录project_to_link变量中的早期的Model类的实例。
只有get()方法给出模型的实例。就此，我们不必再使用filter()方法。
•	 We create a new task, and attribute the project created in the beginning of
the code and the developer that we just recorded.
我们创建一个新的任务，归属我们刚刚记录的创建于代码开始部分的project和developer。
This example is very comprehensive, and it combines many elements that we
have studied from the beginning. We must understand it in order to continue
programming in Django.
这个例子非常综合，它合并了我们从一开始学习过的很多元素。我们为了继续Django中的编程必须去理解它。


Updating records in the database
更行数据库中的记录
There are two mechanisms to update data in Django. Indeed, there is a mechanism
to update one record and another mechanism to update multiple records.
在Django中有两个个更新数据的机制。的确，有一个机制更新一个记录，另外一个机制去更新多个记录。


Updating a model instance
更新模型实例
Updating the existing data is very simple. We have already seen what it takes to be
able to do so. The following is an example where it modifies the first task:
更新以存在的数据非常简单。我们已经见过如何使它起作用以及如何做到。
下面是一个在何处修改第一个task的例子：
from TasksManager.models import Project, Task
from django.shortcuts import render

def page(request):
    new_project = Project(title = "Other project", description="Try to update models.", client_name="People")
    new_project.save()
    task = Task.objects.get(id = 1)
    task.description = "New description"
    task.project = new_project
    task.save()
    return render(request, 'en/public/index.html', {'action' : 'Update model'})


In this example, we created a new project and saved it. We searched our task for id
= 1 . We changed the description and project to the task it is attached to. Finally, we
saved this task.
这个例子中，我们创建了一个新的项目然后保存它。我们用id=1搜索了我们的task。
我们改变了附加到task的description和project。最后，我们保存了这个task。


Updating multiple records
更新多个记录
To edit multiple records in one shot, you must use the update() method with a
queryset object type. For example, our People customer is bought by a company
named Nobody , so we need to change all the projects where the client_name
property is equal to People :
要一下子编辑多个记录，你必须使用一个有queryset 对象类型的update()方法。
例如，我们的Pepole客户是被一个名字叫做Nobody的公司购买的，因此我们
需要改变所有含有client_name属性的project地方去等于People。
from TasksManager.models import Project
from django.shortcuts import render

def page(request):
    task = Project.objects.filter(client_name = "people").update(client_name="Nobody")
    return render(request, 'en/public/index.html', {'action' : 'Update for many model'})
The update() method of a queryset can change all the records related to this
queryset. This method cannot be used on an instance of a model.
queryset的update()方法可以改变所有关联到这个queryset 的记录。该方法不能用在一个模型的实例上。

Deleting a record
删除记录
To delete a record in the database, we must use the delete() method. Removing
items is easier than changing items, because the method is the same for a queryset
as for the instances of models. An example of this is as follows:
要删除数据中的记录，我们必须使用delete()方法。移除项要比改变项简单很多，
因为此方法对于queryset和对于模型的实例是相同的。该方法的例子如下：
from TasksManager.models import Task
from django.shortcuts import render
def page(request):
    one_task = Task.objects.get(id = 1)
    one_task.delete() # line 1第一行
    all_tasks = Task.objects.all()
    all_tasks.delete() # line 2第二行
    return render(request, 'en/public/index.html', {'action' : 'Delete tasks'})
In this example, line 1 removes the stain with id = 1 . Then, line 2 removes all
the present tasks in the database.
这个例子中，行1用id = 1移除污染。之后，行2移除所有数据库中出现的task。
Be careful because even if we use a web framework, we keep hold of the data. No
confirmation will be required in this example, and no backup has been made. By
default, the rule for model deletion with ForeignKey is the CASCADE value. This rule
means that if we remove a template instance, the records with a foreign key to this
model will also be deleted.
即使使用一个web框架也要小心，因为我们要保存所持有的数据。这个例子中不要求确认，而且也没有做过备份。默认，带有ForeignKey的模型删除规则是CASCADE值。该规则意味着若我们移除一个模板的实例，则这个模型的外键记录也将被删除。

Getting linked records
取得已链接记录
We now know how to create, read, update, and delete the present records in the
database, but we haven't recovered the related objects. In our TasksManager
application, it would be interesting to retrieve all the tasks in a project. For example,
as we have just deleted all the present tasks in the database, we need to create others.
We especially have to create tasks in the project database for the rest of this chapter.
现在我们知道如何创建，读取，更新和删除数据库中存在的记录，但是我们还未恢复
相关对象。在我们的TasksManager应用中，程序有意地重新取回在project中的task。
例如，当我们刚删除数据库中所有已存在task，我们就需要创建其他的task。
特别地我们不得不为本章后面的内容创建project数据库中的task。
With Python and its comprehensive implementation of the object-oriented model,
accessing the related models is intuitive. For example, we will retrieve all the project
tasks when login = 1 :
随着Python及其面向模型的广泛执行，访问相关模型是。例如，在login = 1时，我们将重新取回所有项目的task：
from TasksManager.models import Task, Project
from django.shortcuts import render
def page(request):
    project = Project.objects.get(id = 1)
    tasks = Task.objects.filter(project = project)
    return render(request, 'en/public/index.html', {'action' : 'Tasks for project', 'tasks':tasks})
We will now look for the project task when id = 1 :
现在，在id = 1时我们寻找项目task：
from django.shortcuts import render
def page(request):
    task = Task.objects.get(id = 1)
    project = task.project
    return render(request, 'en/public/index.html', {'action' : 'Project for task', 'project':project})
We will now use the relationship to access the project task.
现在我们将使用关系去访问项目task。


Advanced usage of the queryset
queryset的高级用法
We studied the basics of querysets that allow you to interact with the data. In specific
cases, it is necessary to perform more complex actions on the data.
我们已经学习了queryset的基础，它允许你和数据交互。特定的例子中，有必要对数据执行更复杂的行为。

Using an OR operator in a queryset
在queryset中使用一个OR运算符
In queryset filters, we use a comma to separate filters. This point implicitly means a
logical operator AND . When applying an OR operator, we are forced to use the Q object.
于queryset过滤器中，我们使用一个逗号来分割过滤器。这清楚地点出一个逻辑
元素符AND。在应用OR运算符时，我们强制使用Q对象。
This Q object allows you to set complex queries on models. For example, to select the
projects of the customers Me and Nobody , we must add the following lines in our view:
这个Q对象允许你在模型上设置复杂的query。例如，选择客户Me和Nobody的project，
我们必须添加下面的行到视图：
from TasksManager.models import Task, Project
from django.shortcuts import render
from django.db.models import Q
def page(request):
    projects_list = Project.objects.filter(Q(client_name="Me") | Q(client_name="Nobody"))
    return render(request, 'en/public/index.html', {'action' : 'Project with OR operator', 'projects_list':projects_list})


Using the lower and greater than lookups
使用更小和更大的查询
With the Django queryset, we cannot use the < and > operators to check whether a
parameter is greater than or less than another.
对于Django的queryset我们不能够使用 < and > 运算符去检查一个参数是否大于或小于另外一个参数。
You must use the following field lookups:
你必须使用下面的字段查询：
•	 __gte : This is equivalent to SQL's greater than or equal to operator, >=
它等同于SQL的大于或等于运算符 >=
•	 __gt : This is equivalent to SQL's greater than operator, >
它等同于SQL的大于运算符
•	 __lt : This is equivalent to SQL's lower than operator, <
它等同于SQL的小于运算符
•	 __lte : This is equivalent to SQL's lower than or equal to operator, <=
它等同于SQL的小于或等于运算符
For example, we will write the queryset that can return all the tasks with a duration
of greater than or equal to four hours:
例如，我们将写个可以在大于或等于四个小时的稍后返回所有task的queryset：
tasks_list = Task.objects.filter(time_elapsed__gte=4)


Performing an exclude query
执行一个明显的query
The exclude queries can be useful in the context of a website. For example, we want
to get the list of projects that do not last for more than four hours:
明显的query在一个网站的上下文中是很有用的。例如，我们想取得超过4个小时不是最后一个project的列表：
from TasksManager.models import Task, Project
from django.shortcuts import renderdef page(request):
    tasks_list = Task.objects.filter(time_elapsed__gt=4)
    array_projects = tasks_list.values_list('project', flat=True).distinct()
    projects_list = Project.objects.all()
    projects_list_lt4 = projects_list.exclude(id__in=array_projects)
    return render(request, 'en/public/index.html', {'action' : 'NOT SQL equivalent', 'projects_list_lt4':projects_list_lt4})

The following is an explanation of the code snippet:
•	 In the first queryset, we first retrieve the list of all the tasks for which
time_elapsed is greater than 4
•	 In the second queryset, we got the list of all the related projects in these tasks
•	 In the third queryset, we got all the projects
•	 In the fourth queryset, we excluded all the projects with tasks that last for
more than 4 hours


Making a raw SQL query
生成一个原始的query
Sometimes, developers may need to perform raw SQL queries. For this, we can
use the raw() method, defining the SQL query as an argument. The following is
an example that retrieves the first task:
有时候，开发者或许需要执行原始的SQLquery。就此，我们可以使用raw()方法，定义SQL query为一个参数。下面是一个重新取回第一个task的例子：
first_task = Project.objects.raw("SELECT * FROM TasksManager_project")[0]
To access the name of the first task, just use the following syntax:
要访问第一个任务的名称，只要使用下面的语法就好：
first_task.title





Summary
总结
In this chapter, we learned how to handle the database, thanks to the Django ORM.
Indeed, thanks to the ORM, the developer does not need to write SQL queries. In
the next chapter, we will learn how to create forms using Django.
在这一章，我们学会了如何处理数据库，向Django ORM致谢。
的确，要感谢ORM，因为开发者不再需要去写SQL query了。在下一章，我们将学到如何使用Django生成表单。

