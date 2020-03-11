```python
from flask import Flask
app = Flask(__name__)

@app.route('/')
def index():
  return '<h1>Hello World!</h1>'

if __name__ == '__main__':
  app.run(debug = True)
```

接下来我们对这份代码进行解析

## part1

```python
app = Flask(__name__)
```

Flask类的构造函数有一个必须指定的参数：程序主模块或包的名字，如：Python的`__name__`变量

`__name__`变量

\__name__ 是当前模块名，当模块被直接运行时模块名为 \_\_main__ 。`if __name__ == '__main__'`这句话的意思就是，当模块被直接运行时，以下代码块将被运行，当模块是被导入时，代码块不被运行。

<br/>

## part2

```python
@app.route('/')
def index():
  return '<h1>Hello World!</h1>'
```

`app.route`是Python修饰器(decorator)

修饰器详解<a href="https://www.zhihu.com/question/26930016">参考这个回答</a>和<a href="https://lotabout.me/2017/Python-Decorator/">这个回答</a>

```python
def route(self, rule, **options):
  """A decorator that is used to register a view function for a
        given URL rule.  This does the same thing as :meth:`add_url_rule`
        but is intended for decorator usage::

            @app.route('/')
            def index():
                return 'Hello World'

        For more information refer to :ref:`url-route-registrations`.

        :param rule: the URL rule as string
        :param endpoint: the endpoint for the registered URL rule.  Flask
                         itself assumes the name of the view function as
                         endpoint
        :param options: the options to be forwarded to the underlying
                        :class:`~werkzeug.routing.Rule` object.  A change
                        to Werkzeug is handling of method options.  methods
                        is a list of methods this rule should be limited
                        to (``GET``, ``POST`` etc.).  By default a rule
                        just listens for ``GET`` (and implicitly ``HEAD``).
                        Starting with Flask 0.6, ``OPTIONS`` is implicitly
                        added and handled by the standard request handling.
        """

  def decorator(f):
    endpoint = options.pop('endpoint', None)
    self.add_url_rule(rule, endpoint, f, **options)
    return f

  return decorator
```







```python
from flask import Flask
from flask import request
app = Flask(__name__)

@app.route('/')
@app.route('/home')
def home():
    return "<h1>Home Page</h1>"

@app.route('/about')
def about():
    return "<h1>About Page</h1>"
if __name__ == '__main__':
    app.run(debug=True)
```



