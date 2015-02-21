chapter 7 Working with Django Forms
使用Django表单工作
We all know about HTML forms. This is a <form> tag that contains the <input> and
<select> tags. The user can fill in or edit these items and return them to the server.
This is the preferred way to store data provided by the client. Frameworks such as
Django seized the HTML form to make it better.
我们都知道HTML表单。这是一个包含<input>和<select>标签的的<form>标签。
用户可以填充或者编辑这些项然后将它们送回服务器。
这是一个由客户端提供的首选的办法。像Django这样的框架采用HTML表单从而使表单变得更友好。
A Django form is inherited from the Form class object. It is an object in which we
will set properties. These properties will be the fields in the form, and we will
define their type.
Django表单继承自Form类对象。它是我们将设置属性中的一个对象。这些属性将会是表单中的字段，
然后我们定义它们的类型。
In this chapter, we will learn how to do the following:
在本章，我们将学习如何做到这些事情：
•	 Create an HTML form
创建一个HTML表单
•	 Handle the data sent by a form
通过发送一个表单来处理数据
•	 Create a Django form
创建一个Django表单
•	 Validate and manipulate data sent from a Django form
验证和操作发送自Django表单的数据
•	 Create forms based on models
创建基于模型的表单
•	 Customize error messages and use widgets
定制错误消息，使用部件

The advantages of Django forms are as follows:
Django表单的优势如下：
•	 Protection against CSRF vulnerabilities can be easily implemented.
We'll talk about CSRF vulnerabilities thereafter.
防止CSRF漏洞行为也可以轻松地实现。然后我们会谈到CSRF的漏洞。
•	 Data validation is automatic.
自动验证数据。
•	 Forms are easily customizable.
表单是很容易定制的。
But the best way to compare a standard HTML form and a Django form is to practice
it with an example: the form to add a developer.
不过比较标准HTML表单和Django表单最好的方式就是通过带有例子去实践它：
添加一个developer 的表单。
In this section, we will show you how to add a developer without using Django
forms. This example will show the time that can be saved by using Django.
Add the following URL to your urls.py file:
在这一节，我们将向你演示如何不使用Django表单添加一个developer。
这个例子给出使用Django可以保存的时间。
添加下面的URL到你的urls.py文件：
url(r'^create-developer$', 'TasksManager.views.create_developer.page',name="create_developer"),

Template of an HTML form
HTML表单的模板
We will create a template before the view. Indeed, we are going to fill the view with
the template that contains the form. We do not put all the fields in the model because
the code is too long. It is better to learn using shorter code. The following is our
template in template/en/public/create_developer.html :
我们将创建一个之前视图的模板。确实，我们将用包含表单的模板去填充模型。
我们不会在模型放上所有的字段因为代码太长了。最好去学习使用更短的代码。下面
是我们位于template/en/public/create_developer.html中的模板。
{% extends "base.html" %}
{% block title_html %}
Create Developer
{% endblock %}
{% block h1 %}
Create Developer
{% endblock %}
{% block article_content %}
<form method="post" action="{% url "create_developer" %}" >
<table>
<tr>
<td>Name</td>
<td>
<input type="text" name="name" />
</td>
</tr>
<tr>
<td>Login</td>
<td>
<input type="text" name="login" />
</td>
</tr>
<tr>
<td>Password</td>
<td>
<input type="text" name="password" />
</td>
</tr>
<tr>
<td>Supervisor</td>
<td>
<select name="supervisor">
{% for supervisor in supervisors_list %}
<option value="{{ supervisor.id }}">{{ supervisor.name }}</option>
{% endfor %}
</select>
</td>
</tr>
<tr>
<td></td>
<td>
<input type="submit" value="ValData validation is automaticid" />
</td>
</tr>
</table>
</form>
{% endblock %}
Note that the template is impressive and yet it is a minimalist form.
注意模板这模板深重然而它却是最小化的模板。


The view using the POST data reception
使用POST数据接收的视图
The following screenshot shows the web page that we will create:
下面的截图是我们将要创建的网页：
 
The view that will process this form will be as follows. Save the view in the file
views/create_developer.py :
将处理这个表单的视图会是下面这样。把视图保存到文件views/create_developer.py中：
from django.shortcuts import render
from django.http import HttpResponse
from TasksManager.models import Supervisor, Developer
# View for create_developer 
create_developer的视图
def page(request):
    error = False
# If form has posted
如果表单已推送
    if request.POST:
# This line checks if the data was sent in POST. If so, this means
that the form has been submitted and we should treat it.
该行检查数据是否发送到POST。若是，即意味着表单已经提交，我们应该处理它。
    if 'name' in request.POST:
# This line checks whether a given data named name exists in the POST variables.
该行检查已给出数据命名的名称是否存在于POST变量中。
        name = request.POST.get('name', '')
# This line is used to retrieve the value in the POST
dictionary. Normally, we perform filters to recover the data to avoid
false data, but it would have required many lines of code.
该行被用于重新取回POST字典中的值。通常，我们执行过滤器来恢复数据以避免false数据，
不过这会要求很多行代码。
    else:
        error=True
    if  'login'  in request.POST:
        login = request.POST.get('login', '')
    else:
        error=True
    if 'password' in request.POST:
        password = request.POST.get('password', '')
    else:
        error=True
    if 'supervisor' in request.POST:
        supervisor_id = request.POST.get('supervisor', '')
    else:
        error=True
    if not error:
# We must get the supervisor
我们必须取得supervisor
        supervisor = Supervisor.objects.get(id = supervisor_id)
        new_dev = Developer(name=name, login=login, password=password,supervisor=supervisor)
        new_dev.save()
        return HttpResponse("Developer added")
    else:
        return HttpResponse("An error as occured")
else:
    supervisors_list = Supervisor.objects.all()
    return render(request, 'en/public/create_developer.html')
 In this view, we haven't even checked whether the supervisor exists. Even if the code
is functional, note that it requires a lot of lines and we haven't verified the contents of
the transmitted data.
在这个视图中，我们还没检查supervisor是否存在。注意这个视图要求有很多行而且我们还没有验证已传输的数据，即使代码是功能化的。
We used the HttpResponse() method so that we do not have to create
additional templates. We also have no details about client errors when a
field is entered incorrectly.
我们使用HttpResponse()方法这样就不必创建额外的模板。当不正确地输入一个字段时，我们也没有详细的客户端的错误信息。
If you want to verify whether your code works properly, do not forget to check the
data in the administration module.
如果你想要验证你的代码是否正确地工作，不要忘了管理模块中数据。
To try this form, you can add the following line in the block article_content of the
index.html file:
试试这个表单，你可以把下面的行添加到index.html文件的article_content块标签中：
<a href="{% url "create_developer" %}">Create developer</a>


Adding a developer with Django forms
用Django表单添加一个developer
Django forms work with an object that inherits from the Form class. This object will
handle much of the work we have done manually in the previous example.
Django表单同继承自Form类的一个对象一起作用。该对象将处理我们前面的很多的手动完成的例子。
When displaying the form, it will generate the contents of the form template.
We may change the type of field that the object sends to the template if needed.
在显示表单时，它会生成表单模板的内容。
如果有需要我们也可以改变发送到模板对象字段的类型。
While receiving the data, the object will check the contents of each form element.
If there is an error, the object will send a clear error to the client. If there is no error,
we are certain that the form data is correct.
当接收数据时，对象将会检查每个表单元素的内容。
如有错误，对象将发送清晰的错误到客户端。如没有错误，我们便断定那个表单数据是正确的。

CSRF protection
CSRF保护
Cross-Site Request Forgery (CSRF) is an attack that targets a user who is loading
a page that contains a malicious request. The malicious script uses the authentication
of the victim to perform unwanted actions, such as changing data or access to
sensitive data.
跨站请求伪造（CSRF）是一种以一个正在载入包含恶意请求的页面的为目标。
恶意脚本使用受害者的认证去执行多余的动作，比如改编数据或者访问敏感数据。
The following steps are executed during a CSRF attack:
以下步骤在CSRF攻击时执行：
1.	 Script injection by the attacker.
攻击者脚本注入
2.	 An HTTP query is performed to get a web page.
HTTP请求被执行用于取得一个网页页面。
3.	 Downloading the web page that contains the malicious script.
下载包含恶意脚本的网页页面。
4.	 Malicious script execution.
恶意脚本执行。
 
In this kind of attack, the hacker can also modify information that may be critical for
the users of the website. Therefore, it is important for a web developer to know how
to protect their site from this kind of attack, and Django will help with this.
于此种攻击中，骇客也可以违法地修改用户网站信息。因此，对于web开发者来说
知道如何由此种攻击中保护他们的站点，而且Django也会提供帮助。
To re-enable CSRF protection, we must edit the settings.py file and uncomment
the following line:
为了重新启用CSRF保护，我们必须编辑settings.py文件，并取消下面一行的注释：
'django.middleware.csrf.CsrfViewMiddleware',

This protection ensures that the data that has been sent is really sent from a specific
property page. You can check this in two easy steps:
这个保护措施确保数据已经发送的数据是真的来自指定的正确页面。你可以用简单地两步来检查它。
1.	 When creating an HTML or Django form, we insert a CSRF token that will
store the server. When the form is sent, the CSRF token will be sent too.

2.	 When the server receives the request from the client, it will check the CSRF
token. If it is valid, it validates the request.
Do not forget to add the CSRF token in all the forms of the site where protection is
enabled. HTML forms are also involved, and the one we have just made does not
include the token. For the previous form to work with CSRF protection, we need
to add the following line in the form of tags and <form> </form> :
{% csrf_token %}


The view with a Django form
含有Django表单的视图
We will first write the view that contains the form because the template will display
the form defined in the view. Django forms can be stored in other files as forms.
py at the root of the project file. We include them directly in our view because the
form will only be used on this page. Depending on the project, you must choose
which architecture suits you best. We will create our view in the views/create_
developer.py file with the following lines:
from django.shortcuts import render
from django.http import HttpResponse
from TasksManager.models import Supervisor, Developer
from django import forms
# This line imports the Django forms package
class Form_inscription(forms.Form):
# This line creates the form with four fields. It is an object that
inherits from forms.Form. It contains attributes that define the form
fields.
name = forms.CharField(label="Name", max_length=30)
login
= forms.CharField(label="Login", max_length=30)
password
= forms.CharField(label="Password", widget=forms.
PasswordInput)
supervisor = forms.ModelChoiceField(label="Supervisor",
queryset=Supervisor.objects.all())
# View for create_developer
def page(request):
if request.POST:
form = Form_inscription(request.POST)
# If the form has been posted, we create the variable that will
contain our form filled with data sent by POST form.
if form.is_valid():
# This line checks that the data sent by the user is consistent
with the field that has been defined in the form.
name
= form.cleaned_data['name']
# This line is used to retrieve the value sent by the client. The
collected data is filtered by the clean() method that we will see
later. This way to recover data provides secure data.
login
= form.cleaned_data['login']
password
= form.cleaned_data['password']
supervisor
= form.cleaned_data['supervisor']
# In this line, the supervisor variable is of the Supervisor
type, that is to say that the returned data by the cleaned_data
dictionary will directly be a model.
new_developer = Developer(name=name, login=login,
password=password, email="", supervisor=supervisor)
new_developer.save()
return HttpResponse("Developer added")
else:
return render(request, 'en/public/create_developer.html',
{'form' : form})
# To send forms to the template, just send it like any other
variable. We send it in case the form is not valid in order to display
user errors:
else:
form = Form_inscription()
# In this case, the user does not yet display the form, it
instantiates with no data inside.
return render(request, 'en/public/create_developer.html', {'form'
: form})
This screenshot shows the display of the form with the display of an error message:
此截图给出带有一个错误信息显示的表单的显示。
 
Template of a Django form
We set the template for this view. The template will be much shorter:
{% extends "base.html" %}
{% block title_html %}
Create Developer
{% endblock %}
{% block h1 %}
Create Developer
{% endblock %}
{% block article_content %}
<form method="post" action="{% url "create_developer" %}" >
{% csrf_token %}
<!-- This line inserts a CSRF token. -->
<table>
{{ form.as_table }}
<!-- This line displays lines of the form.-->
</table>
<p><input type="submit" value="Create" /></p>
</form>
{% endblock %}
As the complete form operation is in the view, the template simply executes the
as_table() method to generate the HTML form.

The previous code displays data in tabular form. The three methods to generate
an HTML form structure are as follows:
•	 as_table : This displays fields in the <tr> <td> tags
•	 as_ul : This displays the form fields in the <li> tags
•	 as_p : This displays the form fields in the <p> tags
