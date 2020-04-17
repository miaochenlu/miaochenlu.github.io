# Lec1-Basic

```python
# tryflask.py
from flask import Flask
app = Flask(__name__)

@app.route("/")
def hello():
  return "Hello World!"

if __name__ == '__main__':
    app.run(debug=True)
```

@app.route("/") 是进入/目录看到的内容

打开debug mode后修改的内容会在网页中看到

```shell
python tryflask.py
```

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200416154306357.png" alt="image-20200416154306357" style="zoom:50%;" />

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200416154133687.png" alt="image-20200416154133687" style="zoom:50%;" />

做些修改

```python
@app.route("/")
def hello():
  return "<h1>Hello World!</h1>"
```

可以看到hello world发生了变换

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200416154400105.png" alt="image-20200416154400105" style="zoom:50%;" />

<br>

接下来我们创建home page和about page页面

希望通过/和/home访问homepage, 通过/about访问about page

```python
@app.route("/")
@app.route("/home")
def home():
  return "<h1>Home Page!</h1>"

@app.route("/about")
def about():
    return "<h1>About Page</h1>"
```

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200416154658822.png" alt="image-20200416154658822" style="zoom:50%;" />

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200416154726271.png" alt="image-20200416154726271" style="zoom:50%;" />

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200416154745461.png" alt="image-20200416154745461" style="zoom:50%;" />

<br>

# Lec2

我们可以将网页内容放在return的内容里面

```python
@app.route("/")
@app.route("/home")
def home():
  return '''<!doctype html>
  <html>
    <head>
    <title>Home</title>
    </head>
    <body>
    <h1>Home Page</h1>
    <p>Welcome to my page</p>
    </body>
  </html>
  '''
```

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200416155205492.png" alt="image-20200416155205492" style="zoom:50%;" />

但是这样显然并不是优秀的做法。我们会创建template



<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200416155321909.png" alt="image-20200416155321909" style="zoom:50%;" />

在home.html中

```html
<!doctype html>
<html>
<head>
    <title></title>
</head>
<body>
    <h1>Home Page</h1>
</body>
</html>
```

在tryflask.py中import render_template模块, 并且修改home函数

```python
from flask import Flask, render_template

@app.route("/")
@app.route("/home")
def home():
  return render_template('home.html')
```

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200416155722486.png" alt="image-20200416155722486" style="zoom:50%;" />

about page同理

```html
<!-- about.html -->
<!doctype html>
<html>
<head>
    <title></title>
</head>
<body>
    <h1>About Page</h1>
</body>
</html>
```



```python
@app.route("/about")
def about():
    return render_template('about.html')
```

接下来我们尝试加入像template中加入一些参数

```python
posts = [
    {
        "author" : 'a',
        'title' : 'Blog Post 1',
        'content' : 'First Post Content',
        'data_posted' : 'April 2, 2020'
    } ,
    {
        "author" : 'b',
        'title' : 'Blog Post 2',
        'content' : 'Second Post Content',
        'data_posted' : 'April 3, 2020'
    }
]

@app.route("/")
@app.route("/home")
def home():
  return render_template('home.html', posts=posts)
```

修改home.html use jinja2

```jinja2
<body>
    <h1>Home Page</h1>
    {% for post in posts %}
        <h1>{{ post.title }}</h1>
        <p>By {{ post.author }} on {{ post.data_posted }}</p>
        <p>{{ post.content }}</p>
    {% endfor %}
</body>
```

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200416160641889.png" alt="image-20200416160641889" style="zoom:50%;" />

除了for语句，还可以使用if-else语句

如果有title参数，就显示参数，否则默认显示Flask Blgo

```html
<head>
    {% if title %}
        <title>Flask Blog - {{ title }}</title>
    {% else %}
        <title>Flask Blog</title>
    {% endif %}
</head>
```

```python
@app.route("/about")
def about():
    return render_template('about.html', title="About")
```



<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200416160957010.png" alt="image-20200416160957010" style="zoom:50%;" />



现在我们整个结构是这样的

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200416155321909.png" alt="image-20200416155321909" style="zoom:50%;" />

home.html

```html
<!doctype html>
<html>
<head>
    {% if title %}
        <title>Flask Blog - {{ title }}</title>
    {% else %}
        <title>Flask Blog</title>
    {% endif %}
</head>
<body>
    <h1>Home Page</h1>
    {%  for post in posts %}
        <h1>{{ post.title }}</h1>
        <p>By {{ post.author }} on {{ post.data_posted }}</p>
        <p>{{ post.content }}</p>
    {% endfor %}
</body>
</html>
```

about.html

```html
<!doctype html>
<html>
<head>
    {% if title %}
        <title>Flask Blog - {{ title }}</title>
    {% else %}
        <title>Flask Blog</title>
    {% endif %}
</head>
<body>
    <h1>About Page</h1>
</body>
</html>
```

tryflask.py

```python
from flask import Flask, render_template
app = Flask(__name__)

posts = [
    {
        "author" : 'a',
        'title' : 'Blog Post 1',
        'content' : 'First Post Content',
        'data_posted' : 'April 2, 2020'
    } ,
    {
        "author" : 'b',
        'title' : 'Blog Post 2',
        'content' : 'Second Post Content',
        'data_posted' : 'April 3, 2020'
    }
]

@app.route("/")
@app.route("/home")
def home():
  return render_template('home.html', posts=posts)

@app.route("/about")
def about():
    return render_template('about.html', title="About")

if __name__ == '__main__':
    app.run(debug=True)
```

我们可以看到home.html和about.html的代码中有很多重合的部分，因此我们将这些部分抽取出来成layout

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200416180520731.png" alt="image-20200416180520731" style="zoom:50%;" />

layout.html

```html
<!doctype html>
<html>
<head>
    {% if title %}
        <title>Flask Blog - {{ title }}</title>
    {% else %}
        <title>Flask Blog</title>
    {% endif %}
</head>
<body>
    {% block content %}{% endblock %}
</body>
</html>
```

body的内容是其后代有差别的地方，因此创建block.

home.html继承layout.html

补充block content中的内容

```jinja2
{% extends "layout.html" %}
{% block content %}
{%  for post in posts %}
    <h1>{{ post.title }}</h1>
    <p>By {{ post.author }} on {{ post.data_posted }}</p>
    <p>{{ post.content }}</p>
{% endfor %}
{% endblock content %}
```

about.html同理

```jinja2
{% extends "layout.html" %}
{% block content %}
    <h1>About Page</h1>
{% endblock content%}
```

<br>



为了网站的样式，使用bootstrap

```html
<!doctype html>
<html>
<head>
    <!-- Required meta tags -->
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

    <!-- Bootstrap CSS -->
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous">

    {% if title %}
        <title>Flask Blog - {{ title }}</title>
    {% else %}
        <title>Flask Blog</title>
    {% endif %}
</head>
<body>
    <div class="container">
        {% block content %}{% endblock %}
    </div>
    <!-- Optional JavaScript -->
    <!-- jQuery first, then Popper.js, then Bootstrap JS -->
    <script src="https://code.jquery.com/jquery-3.2.1.slim.min.js" integrity="sha384-KJ3o2DKtIkvYIK3UENzmM7KCkRr/rE9/Qpg6aAZGJwFDMVNA/GpGFF93hXpG5KkN" crossorigin="anonymous"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.9/umd/popper.min.js" integrity="sha384-ApNbgh9B+Y1QKtv3Rn7W3mgPxhU9K/ScQsAP7hUibX39j7fakFPskvXusvfa0b4Q" crossorigin="anonymous"></script>
    <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js" integrity="sha384-JZR6Spejh4U02d8jOt6vLEHfe/JQGiRRSQQxSfFWpi1MquVdAyjUar5+76PVCmYl" crossorigin="anonymous"></script>
</body>
</html>
```

<br>

接下来我们添加navigation bar和global style

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200416183523065.png" alt="image-20200416183523065" style="zoom:50%;" />

在layout中使用static/main.css, 这需要url_for函数，

在tryflask.py中import

```python
from flask import Flask, render_template, url_for
```

```html
<link rel="stylesheet" type="text/css" href="{{ url_for('static', filename='main.css') }}" >
```

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200416183953303.png" alt="image-20200416183953303" style="zoom:50%;" />

修改home.html

```jinja2
{% extends "layout.html" %}
{% block content %}
{%  for post in posts %}
    <article class="media content-section">
        <div class="media-body">
        <div class="article-metadata">
            <a class="mr-2" href="#">{{ post.author }}</a>
            <small class="text-muted">{{ post.date_posted }}</small>
        </div>
        <h2><a class="article-title" href="#">{{ post.title }}</a></h2>
        <p class="article-content">{{ post.content }}</p>
        </div>
    </article>
{% endfor %}
{% endblock content %}

```

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200416184307603.png" alt="image-20200416184307603" style="zoom:50%;" />

