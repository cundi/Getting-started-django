    
#Django的Ajax使用
*****************

AJAX is an acronym for Asynchronous JavaScript and XML. This technology allows a browser to asynchronously communicate with the server using JavaScript. Refreshing the web page is not necessarily required to perform an action on the server.   

AJAX是一个

很多发行的web应用都基于AJAX运行。web应用经常被描述为一个只包含一个页面的网站，使用AJAX服务器来运行所有的操作。 


如果你不是用库，那么使用AJAX就会要求很多行的代码来兼容多个浏览器。当包括jQuery时，jQuery使AJAX请求变的轻松而且同时很可以兼容于很多浏览器。  


在本章，我们会学习：  

- 使用JQuery工作  

- JQuery基础  

- 在task管理器中使用AJAX  


## 使用JQUERY
jQuery是一个设计成更有效的操作HTML页面的DOM的JavaScript库。DOM（文档对象模型）是HTML代码的内部结构JavaScript，而则jQuery极大的简化了操作。下面是jQuery的优点：  

- 使用CSS1-3选择器来进行DOM操作是可行的。  


- jQuery集成了AJAX。

It is possible to animate the page with visual effects  
jQuery可以使用视觉效果对页面产生动画
Using AJAX with Django 
Good documentation with numerous examples  
Many libraries have been created around jQuery  


## jQuery基础
在这一章，我们使用jQuery产生AJAX请求。在使用jQuery之前，让我们理解它的基础。  

- jQuery中的CSS选择器  


- CSS选择器用于样式表可以使用非常少的代码有效地重新取回一个项。这一个非常有趣的特性，它在HTML5选择器API中的执行使用下面的语法：

```js
item = document.querySelector('tag#id_content');
```


- jQuery也允许我们去使用CSS选择器。要使用jQuery做同样的事情，你必须使用下面的语法：  

```js
item = $('tag#id_content');
```

在此时，最好使用jQuery而不是选择器API，因为jQuery1.XXX版本保证了对老旧浏览器的最大兼容性。



## 取回HTML内容
可以使用html()方法取回两个标签之间的HTML内容：
alert($('div#div_1').html());
这一行会显示一个含有<div id="div_1"> 标签的HTML代码内容。涉及到input和textarea标签，使用val()方法可以恢复它们自己的内容。  


- 在一个元素中配置HTML内容

- 改变一个标签的内容是非常简单的，因为我们使用相同的方法如同我们恢复而使用的方法。
两者之间的主要区别就是我们会发送一个参数到方法。因此，下面的指令会在div标签中添加一个按钮：
$('div#div_1').html($('div#div_1').html()+'<button>JQuery</button>');




## 循环元素
jQuery也允许我们匹配一个选择器的所有元素。要做到这个，你必须使用展示在下面的例子中each()方法：

```js
   var cases = $('nav ul li').each(function() {
     $(this).addClass("nav_item");
}); 
```


### 导入jQuery库
要使用jQuery，你首先必须导入库。添加jQuery到一个web页面有两种方法。
每种方式都有自己的优点，所述如下：  


- 下载jQuery并从web服务器导入它。使用这个方法，我们一直控制库，如果我们也有自己的网站要确保文件是可达的。  

- 使用从任意网站Google可达的托管的库。这优点就是我们避免一个到服务器的HTTP请求，这样可以省些电。在这一章，我们将在自己的web服务器上托管jQuery而不以赖于一个主机。我们将在应用中的所有页面导入jQuery，因为我们或许需要多个页面。另外，浏览器缓存有时候会保存jQuery，这样就不需要经常去下载它。因此，我们会下载jQuery 1.11.0并将它保存到TasksManager/static/javascript/lib/jquery-1.11.0.js文件上。然后，你必须在base.html文件的head标签中添加下面的行：  

```js
<script src="{% static 'javascript/lib/jquery-1.11.0.js' %}"></script>  {% block head %}{% endblock %}
```

- 对于这些改变，我们可以在网站的所有页面中使用jQuery，我们可以在模板要继承的base.html的head块添加几行。


## 在task manager中使用使用AJAX
在这一节，我们会为了删除task的执行而使用AJAX去修改task的列表显示页面。要做到这一点，我们执行以下步骤：  

- 在task_list页面添加一个删除按钮

- 创建一个包含AJAX代码和处理AJAX请求的返回值的函数。

- 创建一个Django删除task的视图。  


我们将通过修改tasks_list.html模板来添加删除按钮。要完成此操作，你必须如下改变tasks_list中的task循环的for task：  

```python
   {% for task in tasks_list %}
     <tr id="task_{{ task.id }}">
       <td><a href="{% url "task_detail" task.id %}">{{ task.title }}</
   a></td>
       <td>{{ task.description|truncatechars:25 }}</td>
       <td><a href="{% url "update_task" task.id %}">Edit</a></td>
       <td>
				<button onclick="javascript:task_delete({{ task.id }}, '{% url
   			"task_delete_ajax" %}');">Delete</button>
		  </td>
     </tr>
   {% endfor %}
```

In the preceding code, we added an id property to the <tr> tag. This property will be useful in the JavaScript code to delete the task line when the page will receive the AJAX response. We also replaced the Delete link with a Delete button that executes the JavaScript task_delete() function. The new button will call the task_delete() function to execute the AJAX request. This function accepts two parameters:   

在前面的代码中，我们添加了一个id属性到<tr>标签。当页面接收AJAX响应时，在JavaScript代码中这个属性将删除task行  


- task的标识符  

- AJAX请求的URL  

添加下面的代码，我们会在文件static/javascript/task.js中创建这个函数：  

```js
 function task_delete(id, url){
     $.ajax({
      type: 'POST',

//这里，我们定义所使用的方法去发送数据Django视图。其他的值可能是POST，GET，以及其它的HTTP请求方法。
url: url, 
// 这一行用于指定将要处理请求的那个URL

	data: {task: id},
// data属性用于将要和AJAX请求一起发送的定义数据
 	dataType:'json',
// 这一行定义我们所期望返回到服务器的数据的类型。在这个例子中我们不一定需要JSON，但是当响应更加完整时，我们使用此种数据类型。
       success: task_delete_confirm,
// success属性允许我们定义了一个在AJAX请求工作时所执行的函数。这个函数当做AJAX响应接收的一个参数。
       error: function () {alert('AJAX error.');}
// 当AJAX请求不工作时，error属性可以定义一个函数。在之前的代码我们定义过一个显示一个AJAX错误到用户的匿名函数。
}); } 
   function task_delete_confirm(response) {
     task_id = JSON.parse(response);
// 当请求成功时，函数中的这一行接收AJAX响应。这一行允许由Django视图返回序列化的JSON响应。
     if (task_id>0) {
       $('#task_'+task_id).remove();
// 这一行会删除我们刚刚移除task的<tr>标签。
     }
     else {
       alert('Error');
} } 
```


在模板中，我们必须在task_list.html模板中 title_html 块之后添加下面的行以导入taks.js。  

```python
   {% load static %}
   {% block head %}
     <script src="{% static 'javascript/task.js' %}"></script>
   {% endblock %}
```

我们必须添加下面的URL到url.py文件：  

```python
 url(r'^task-delete-ajax$', 'TasksManager.views.ajax.task_delete_ ajax.page',name="task_delete_ajax"),
```

这个URL会使用包含在文件view/ajax/task_delete_ajax.py中的视图。我们必须用下面的内容以及文件__init__.py和task_delete_ajax.py 一起创建AJAX模块：  
  
```python
 from TasksManager.models import Task
   from django.http import HttpResponse
   from django import forms
   from django.views.decorators.csrf import csrf_exempt
   # We import the csrf_exempt decorator that we will use to line 4.
我们导入要使用的csrf_exempt装饰器到第4行。
   import json

   # We import the json module we use to line 8.
我们导入要使用的json模块到第8行。

   class Form_task_delete(forms.Form):

   # We create a form with a task field that contains the identifier
   of the task. When we create a form it allows us to use the Django
   validators to check the contents of the data sent by AJAX. Indeed, we
   are not immune that the user sends data to hack our server.
我们创建一个含有一个task带有task标识的字段的表单。当我们创建一个表单时，它允许我们
使用Django验证器去检查AJAX发送来的数据内容。确实我们

     task       = forms.IntegerField()
   @csrf_exempt

   # This line allows us to not verify the CSRF token for this view.
   Indeed, with AJAX we cannot reliably use the CSRF protection.
#这一行允许我们不对该视图验证CSRF令牌环。确实，用AJAX我们不能可靠地使用CSRF保护。

   def page(request):
     return_value="0"

     # We create a variable named return_value that will contain a code
   returned to our JavaScript function. We initialize the value 0 to the
   variable.
# 我们创建一个名称为return_value的变量，它包含了一个返回到JavaScript函数的代码。我们初始化值0到变量。

     if len(request.POST) > 0:
       form = Form_task_delete(request.POST)
       if form.is_valid():

       # This line allows us to verify the validity of the value sent by
   the AJAX request.
#该行允许我们去验证由AJAX请求发送的值的有效性。
         id_task = form.cleaned_data['task']
         task_record = Task.objects.get(id = id_task)
         task_record.delete()
         return_value=id_task

         # If the task been found, the return_value variable will contain
   the value of the id property after removing the task. This value will
   be returned to the JavaScript function and will be useful to remove
   the corresponding row in the HTML table.
如果task被发现，变量return_value将在移除task后包含id属性的值。这个值会被返回
到JavaScript函数，在HTML table中移除对应的排是比较有用的。
     # The following line contains two significant items. The json.
   dumps() function will return a serialized JSON object. Serialization
   allows encoding an object sequence of characters. This technique
   allows different languages to share objects transparently. We also
   define a content_type to specify the type of data returned by the
   view.
下面的行包含两个重要意义的选项。json.dumps()函数返回一个序列化的JSON对象。
序列化允许编码一个字符序列对象。此技术允许不同的语言无障碍地共享对象。
我们也可以定义一个content_type指定视图返回的数据类型。
     return HttpResponse(json.dumps(return_value), content_type =
   "application/json")
```

## 总结
在这一章，我们学习了如何使用jQuery。我们见到了如何使用库轻松地访问DOM。我们也在TasksManager应用中创建了一个AJAX请求，而且我们将会处理这个请求的视图。  

在下一章，我们会学习如何基于Nginx和PostgreSQL的服务器上发布一个Django项目。我们看到和会一步步地讨论安装。  

