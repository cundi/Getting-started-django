
# 第七章－ 使用Django表单工作

我们都知道HTML表单。这是一个包含<input>和<select>标签的的<form>标签。
用户可以填充或者编辑这些项然后将它们送回服务器。
这是一个由客户端提供的首选的办法。像Django这样的框架采用HTML表单从而使表单变得更友好。 


Django表单继承自Form类对象。它是我们将设置属性中的一个对象。这些属性将会是表单中的字段，然后我们定义它们的类型。  

在本章，我们将学习如何做到这些事情： 

- 创建一个HTML表单  

- 通过发送一个表单来处理数据  

- 创建一个Django表单  

- 验证和操作发送自Django表单的数据  

- 创建基于模型的表单  

- 定制错误消息，使用部件  


Django表单的好处如下：  


- 防止CSRF漏洞行为也可以轻松地实现。然后我们会谈到CSRF的漏洞。    

- 自动验证数据。  

- 表单是易于定制。

不过比较标准HTML表单和Django表单最好的方式就是通过带有例子去实践它：  

添加一个developer 的表单。   

在这一节，我们将向你演示如何不使用Django表单添加一个developer。
这个例子给出使用Django可以保存的时间。
添加下面的URL到你的urls.py文件：

```python
url(r'^create-developer$','TasksManager.views.create_developer.page',name="create_developer"),
```


## HTML表单的模板
我们将创建一个之前视图的模板。确实，我们将用包含表单的模板去填充视图。
我们不会在模型放上所有的字段，因为代码太长了。最好去学习使用更短的代码。下面是我们位于template/en/public/create_developer.html中的模板。  

```python
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
```

注意这模板给人笨重的感觉然而它却是最小化的模板。  



## 使用POST数据接收的视图

下面的截图是我们将要创建的网页：  
 
将处理这个表单的视图会是下面这样。把视图保存到文件views/create_developer.py中：  

```python
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
该行被用于重新取回POST字典中的值。通常，我们执行过滤器来恢复数据以避免false数据，不过这要求很多行代码。
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
        new_dev=Developer(name=name,login=login, password=password,supervisor=supervisor)
        new_dev.save()
        return HttpResponse("Developer added")
    else:
        return HttpResponse("An error as occured")
else:
    supervisors_list = Supervisor.objects.all()
    return render(request, 'en/public/create_developer.html')
```


在这个视图中，我们还没检查supervisor是否存在。注意这个视图要求有很多行而且我们还没有验证已传输的数据，即使代码是功能化的。  

我们使用HttpResponse()方法这样就不必创建额外的模板。当不正确地输入一个字段时，我们也没有详细的客户端的错误信息。

如果你想要验证你的代码是否正确地工作，不要忘了管理模块中数据。  

试试这个表单，你可以把下面的行添加到index.html文件的article_content块标签中：  

```python
<a href="{% url "create_developer" %}">Create developer</a>
```


## 用Django表单添加一个developer
Django表单同继承自Form类的一个对象一起作用。该对象将处理我们前面的很多的手动完成的例子。  

在显示表单时，它会生成表单模板的内容。
如果有需要我们也可以改变发送到模板对象字段的类型。  

当接收数据时，对象将会检查每个表单元素的内容。
如有错误，对象将发送清晰的错误到客户端。如没有错误，我们便断定那个表单数据是正确的。  


## CSRF保护
跨站请求伪造（CSRF）是一种以一个正在载入包含恶意请求的页面的为目标。
恶意脚本使用受害者的认证去执行多余的动作，比如改编数据或者访问敏感数据。  


以下步骤在CSRF攻击时执行：  

1- 攻击者脚本注入

2- HTTP请求被执行用于取得一个网页页面。

3- 下载包含恶意脚本的网页页面。

4- 恶意脚本执行。
 
于此种攻击中，骇客也可以违法地修改用户网站信息。因此，对于web开发者来说
知道如何由此种攻击中保护他们的站点，而且Django也会提供帮助。  

为了重新启用CSRF保护，我们必须编辑settings.py文件，并取消下面一行的注释：  

```python
'django.middleware.csrf.CsrfViewMiddleware',
```

这个保护措施确保数据已经发送的数据是真的来自指定的正确页面。你可以用简单地两步来检查它。  

1.	 When creating an HTML or Django form, we insert a CSRF token that will
store the server. When the form is sent, the CSRF token will be sent too.  

2.	 When the server receives the request from the client, it will check the CSRF
token. If it is valid, it validates the request.  

Do not forget to add the CSRF token in all the forms of the site where protection is
enabled. HTML forms are also involved, and the one we have just made does not
include the token. For the previous form to work with CSRF protection, we need
to add the following line in the form of tags and <form> </form> :  

```python
{% csrf_token %}
```


## 含有Django表单的视图
我们首先写包含视图的表单，因为模板将要显示视图中定义过了的表单。
Django表单可以作为forms.py保存在根目录其他文件中。我们直接地把它们
包括在视图中，因为表单只用在这个页面。基于project，你必须悬着最适合自己的
架构。我们在文件views/create_developer.py中使用下面的行创建视图：  

```python
from django.shortcuts import render
from django.http import HttpResponse
from TasksManager.models import Supervisor, Developer
from django import forms


# 这一行导入Django表单的包
class Form_inscription(forms.Form):
# 这一行用了四个字段创建表单。它是一个集成自forms.Form的对象。
它包含定义表单字段的属性。
name = forms.CharField(label="Name", max_length=30)
login= forms.CharField(label="Login", max_length=30)
password= forms.CharField(label="Password", widget=forms.PasswordInput)
supervisor = forms.ModelChoiceField(label="Supervisor",
queryset=Supervisor.objects.all())


# View for create_developer create_developer的视图
def page(request):
    if request.POST:
    form = Form_inscription(request.POST)
# If the form has been posted, we create the variable that will
contain our form filled with data sent by POST form.
如果表单已经送出，我们创建包含通过发送POST表单填充数据的表单的变量
       if form.is_valid():
# This line checks that the data sent by the user is consistent
with the field that has been defined in the form.
该行检查由用户发送的数据始终带有已经在表单中定义过的字段。
          name= form.cleaned_data['name']
# This line is used to retrieve the value sent by the client. The
collected data is filtered by the clean() method that we will see
later. This way to recover data provides secure data.
该行用于重新取回客户端发送的数据。收集的数据通过我们稍后会见到的clean()方法过滤。该方法会恢复数据，提供安全的数据。
         login= form.cleaned_data['login']
         password= form.cleaned_data['password']
         supervisor= form.cleaned_data['supervisor']

# In this line, the supervisor variable is of the Supervisor
type, that is to say that the returned data by the cleaned_data
dictionary will directly be a model.
该行中，supervisor变量属于Supervisor类型，这就是说cleaned_data字典返回的数据将直接地作为一个模型。（cleaned_data -- 原来绑定到表单的值的干净版本
干净数据意义在于， 输入的数据需要规范化 --- 从一种或多种可能的输入格式转换为一个统一的输出格式，以方便验证和数据库存储
因为 request.POST 中存储的格式一般都是字符串， 经过干净之后
数字变量的字符串 -- int、long
日期变量的字符串 -- datetime）
    new_developer = Developer(name=name, login=login,password=password, email="", supervisor=supervisor)
    new_developer.save() 
    return HttpResponse("Developer added")
    else:
        return render(request, 'en/public/create_developer.html',{'form' : form})
# To send forms to the template, just send it like any other
variable. We send it in case the form is not valid in order to display
user errors:
发送表单到模板，就像发送其他变量那样。我们在例子表单中发送它是为了显示用户错误：
    else:
    form = Form_inscription()
# In this case, the user does not yet display the form, it instantiates with no data inside.
该例子中，用户还没有显示表单，在内部它没有数据来实例化。
    return render(request, 'en/public/create_developer.html', {'form': form})
```

此截图给出带有一个错误信息显示的表单的显示。  
 

图片：略  


## Django表单模板
我们为这个视图写个模板。这个模板会非常的短：  

```python
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
<!-- This line inserts a CSRF token. -->该行插入一个CSRF令牌
<table>
{{ form.as_table }}
<!-- This line displays lines of the form.-->该行显示表单的所有行
</table>
<p><input type="submit" value="Create" /></p>
</form>
{% endblock %}
```


随着在视图中完成表单操作，模板简单地执行as_table()方法生成HTML表单。  

前面的代码以表单形式显示数据。这三个方法生成一个HTML表单的结构如下：  

- as_table用于在<tr><td>标签中显示字段

- as_ul用于在<li>标签中显示表单字段

- as_p用于在<p>标签中显示表单字段

所以我们快速地通过Django表单写了一个带有错误处理和CSRF保护的安全表单。在附录和提示表中你可以找到一个表单中的不同字段。  

## 基于模型的表单
ModelForms是基于模型的Django表单。这些表单的字段由我们前面定义的模型自动地生成。
确实，对一个非MVC网站开发者经常被要求创建对应那些在数据库中字段的表单。   

这些特别的表单拥有save方法，它会在一个新的记录中保存表单。  

## 使用supervisor的创建表单
为了讨论，我们会举个例子，一个supervisor的添加。对于这个例子，我们会创建一个新页面。
要完成此操作，我们会创建下列的URL：  

```python
   url(r'^create-supervisor$', 'TasksManager.views.create_supervisor.
   page', name="create_supervisor"),
Our view will contain the following code: 我们的视图将包含下列代码：
   from django.shortcuts import render
   from TasksManager.models import Supervisor
   from django import forms
   from django.http import HttpResponseRedirect
   from django.core.urlresolvers import reverse
   def page(request):
     if len(request.POST) > 0:
       form = Form_supervisor(request.POST)
       if form.is_valid():
         form.save(commit=True)  #save方法有个参数叫commit,默认是true,即当使用f.save()的时候，会将数据保存到数据库，但是显示指出commit=false,则不会保存到数据库

         # If the form is valid, we store the data in a model record in
   the form.如果这个表单有效，我们在这个表单里的一个模型记录中存储数据。
         return HttpResponseRedirect(reverse('public_index'))
         # This line is used to redirect to the specified URL. We use the
   reverse() function to get the URL from its name defines urls.py.
这一行用于重定向到指定的URL。我们使用reverse()函数从它的urls.py中的name定义获取URL。
       else:
         return render(request, 'en/public/create_supervisor.html',{'form': form})
     else:
       form = Form_supervisor()
       return render(request, 'en/public/create_supervisor.html',{'form': form})
  class Form_supervisor(forms.ModelForm):
   # Here we create a class that inherits from ModelForm. 
这里我们创建一个集成自ModelForm的类。
class Meta: 
     # We extend the Meta class of the ModelForm. It is this class that
   will allow us to define the properties of ModelForm.
我们扩展ModelForm的元类。它是一个这样的类，它允许我们定义ModelForm类属性的类。
       model = Supervisor
       # We define the model that should be based on the form.
我们定义模型应该基于表单
       exclude = ('date_created', 'last_connetion', )
       # We exclude certain fields of this form. It would also have been
   possible to do the opposite. That is to say with the fields property,
   we have defined the desired fields in the form.
我们排除该表单的确定要排除的字段。它也可以做出相反的事情来。就是说，我们可以在表单中定义所期望字段的字段属性。
```


如同在exclude = ('date_created', 'last_connexion', )行所见的那样，它是可以限制表单字段的。exclude和fields属性两者都必须正确。的确，作为参数这些属性接收到一个字段元组，来包含或者排除类的属性。  


exclude：这个属性用在一个管理员可以方法的表单的例子。因为，如果你在模型中添加一个字段，
它也会被包含在表单中。  


fields：这个属性用在哪个表单是用户可以访问的例子中。的确，如果我们在模型中添加一个字段，
它对于用户是不可访问的。  


例如，我们有一个基于ModelForm表单的出售无版权使用费图片的网站。管理员在用户的扩展中
添加一个credit字段。如果开发者在一些字段中使用了exclude属性，而且没有添加credit，用户将能够看到他或者她想看到的信用卡。
We will resume our previous template, where we will change the URL present in the attribute action of the <form> tag:    
我们将从出现在<form>标签的属性行为中的我们想要改变URL的地方，开始前面的模板：  

```python
 {% url "create_supervisor" %}
```


这个例子向我们演示了，在开发中通过使用一个可以定制的表单ModelForms 可以节省你很多的时间。在下一章，我们将见到使用基于类的视图如何能更快一些。  


## Django表单的高级用法

我们研习了表单基础，它允许你使用轻量化的定制创建简单的表单。有时候，
它对于定制方面大有用处，比如数据验证和错误显示，或者使用特殊图表。  

### 扩展验证表单
对于指定的表单字段验证它是比较有用的。在你想起表单的好处时那是Django使得它能够轻松的操作。我们会举一个添加开发者的例子，这也是我们引导一个密码审计的地方。  

为此，我们将改变下列做法中视图的表单：  

```python
   class Form_inscription(forms.Form):
     name = forms.CharField(label="Name", max_length=30)
     login = forms.CharField(label = "Login")
     password = forms.CharField(label = "Password", widget = forms.
   PasswordInput)
     # We add another field for the password. This field will be used
   to avoid typos from the user. If both passwords do not match, the
   validation will display an error message
我们为密码添加另外的字段。这个字段会用于避免用户错误输入。如果这两个密码
都不匹配，验证将显示一个错误信息。
     password_bis = forms.CharField(label = "Password", widget = forms.
   PasswordInput)
     supervisor = forms.ModelChoiceField(label="Supervisor",
   queryset=Supervisor.objects.all())
     def clean(self):
     # This line allows us to extend the clean method that is responsible
   for validating data fields.
该行允许我们扩张负责验证数据字段的clean方法
       cleaned_data = super (Form_inscription, self).clean()
       # This method is very useful because it performs the clean()
   method of the superclass. Without this line we would be rewriting the
   method instead of extending it.
该方法非常有用因为它执行了超类的clean()方法。没有这一行我们得重写方法而不是扩展它。
       password = self.cleaned_data.get('password')
       # We get the value of the field password in the variable.
我们获取变量中字段password的值
       password_bis = self.cleaned_data.get('password_bis')
       if password and password_bis and password != password_bis:
         raise forms.ValidationError("Passwords are not identical.")
         # This line makes us raise an exception. This way, when the view
   performs the is_valid() method, if the passwords are not identical,
   the form is not validated .
该行抛出一个异常。这样，当视图执行is_valid()方法时，如果密码时不相等的话，表单就是无效的。
       return self.cleaned_data
 ```

通过这个例子，我们可以见到Django在表单的管理和审核上显得非常灵活。它也允许你定制错误显示。  


## 定制错误显示
Sometimes, it may be important to display user-specific error messages. For example, a company may request for a password that must contain certain types of characters; for example, the password must contain at least one number and many letters. In such cases, it would be preferable to also indicate this in the error message. Indeed, users read more carefully the error messages than help messages.   

To do this, you must use the error_messages property in the form fields and set the error message as a text string. 
It is also possible to define different messages depending on the type of error. We will create a dictionary of the two most common mistakes and give them a message. We can define this dictionary as follows:   

它也可以基于不同的错误类型来定义不同的提示信息。我们创建一个两个最常见错误的字典，并
给它们一个提示信息。如下我们定义这个字典：  

```python
   error_name = {
     'required': 'You must type a name !',
     'invalid': 'Wrong format.'
}
```

We will modify the name field of the Form_inscription form of create_ developer.py: name = forms.CharField(label="Name", max_length=30, error_ messages=error_name) This way, if the user doesn't fill the name field, he/she will see the following message: You must type a name!. To apply this message to ModelForm, we have to go to the models.py file and modify the line that contains the name field. name = models.CharField(max_length=50, verbose_name="Name", error_ messages=error_name)  
我们修改create_ developer.py的Form_inscription表单的那么字段：name = forms.CharField(label="Name", max_length=30, error_ messages=error_name)   

这样，如果用户没有填充name字段，他或者她将会见到以下信息：你必须输入一个名字！。
要将此信息应用到ModelForm上，我们必须到models.py文件，修改包含name字段的行。name = models.CharField(max_length=50, verbose_name="Name", error_ messages=error_name)  

当编辑models.py文件时，我们应该不要忘记去指定error_name字典。通过对用户的告知这些错误信息提高了网站的质量。当验证比较复杂时，对字段使用定制提示信息就显得非常重要了。然而，不要对基本字段过多使用，这样做会浪费开发者的很多时间。  


## 使用部件
部件是一种定制表单元素显示的有效方式。的确，在一些例子中，使用ModelForm指定特别规格的一个文本框字段它可以给予不少帮助。  

要学习使用部件的实际操作，并继续应用的开发，我们将创建创建项目的页面。此页面包含一个
Django表单，我们在HTML的<textarea>标签中设置description字段。  


我们需要添加下列URL到文件urls.py：  

```python
 url(r'^create_project$', ' TasksManager.views.create_project.page',name='create_project'),
```

然后，在文件create_project.py中用写列代码创建代码：  

```pyhton
   from django.shortcuts import render
   from TasksManager.models import Project
   from django import forms
   from django.http import HttpResponseRedirect
   from django.core.urlresolvers import reverse
   class Form_project_create(forms.Form):
     title = forms.CharField(label="Title", max_length=30)
     description = forms.CharField(widget= forms.Textarea(attrs={'rows':
   5, 'cols': 100,}))
     client_name = forms.CharField(label="Client", max_length=50)


   def page(request):
     if request.POST:
       form = Form_project_create(request.POST)
       if form.is_valid():
         title = form.cleaned_data['title']
         description = form.cleaned_data['description']
         client_name = form.cleaned_data['client_name']
         new_project = Project(title=title, description=description,
   client_name=client_name)
         new_project.save()
         return HttpResponseRedirect(reverse('public_index'))
       else:
         return render(request, 'en/public/create_project.html', {'form'
   : form})
     else:
       form = Form_project_create()
     return render(request, 'en/public/create_project.html', {'form' :
   form})
```

你可以取得一个我们创建的和适配的模板。这个表单和Django表单我们之前创建过的同样使用。
在拷贝一个我们已经创建过的模板，需要改变<form>标签的action属性的title和URL。访问页面，我们注意到小部件正常的工作并显示更适合长文本的一个文本框。  


有很多定制表单的小部件。一个Django的伟大品质就是通用和完全地适用时间。  

## 设置表单中的初始化数据
有两种使用Django声明表单字段的初始值。下面的例子放在creat_develope.py文件。  


### 何时初始化表单

下面的代码显示在name字段中显示new，选择定义supervisor的<select>字段中的第一个supervisor。
这些字段可被用户编辑：   

```python
   form = Form_inscription(initial={'name': 'new', 'supervisor':
   Supervisor.objects.all()[:1].get().id})
```

这一行必须替换create_developer.py中的 form = Form_inscription() 行。  


### 何时定义字段
要对前面的的小节使用相同的效果，在name字段中显示new，选择对应字段中的第一个supervisor；
你必须改变使用下列代码改变supervisor字段的name声明：  

```python
   name = forms.CharField(label="Name", max_length=30, initial="new")
   supervisor = forms.ModelChoiceField(label="Supervisor",
   queryset=Supervisor.objects.all(), initial=Supervisor.objects.all()[:1].get().id)
```


## 总结
在这一章，我们学习了如何使用Django 表单。这些表单自动数据验证和错误显示可以让你节省很多时间。  

下一章，我们会深入到通用行为中，使用表单节省更多的时间。  

