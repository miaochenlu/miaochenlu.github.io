在这一节，我们将完善login和register的功能

这设计到表单，密码邮箱格式的验证等功能，我们采用flask-wtf来辅助实现

创建form.py

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200417163231682.png" alt="image-20200417163231682" style="zoom:50%;" />

```python
from flask_wtf import FlaskFrom

class RegistrationForm(FlaskFrom):
```

我们的form里面需要有username这个项目，username是个string, 为了处理他，我们从wtforms import StringField

```python
username = StringField('Username')
```

在表单中就会显示这个字段的名称为'Username'

同时，我们要求这一栏的数据是必须填的datarequired

我们从wtforms.validators import DataRequired

```python
from flask_wtf import FlaskFrom
from wtfroms import StringField
from wtforms.validators import DataRequired, Length

class RegistrationForm(FlaskFrom):
    # username
    username = StringField('Username', validators=[DataRequired()])
```

另外，我们对这个字段的长度也有要求，我们从wtforms.validators import Length

```python
from flask_wtf import FlaskFrom
from wtfroms import StringField
from wtforms.validators import DataRequired, Length

class RegistrationForm(FlaskFrom):
    username = StringField('Username', validators=[DataRequired(), Length(min=2, max=20)])
```

接下来也同理

```python
from flask_wtf import FlaskFrom
from wtfroms import StringField, PasswordField, SubmitField
from wtforms.validators import DataRequired, Length, Email, EqualTo

class RegistrationForm(FlaskFrom):
    username = StringField('Username', 
                            validators=[DataRequired(), Length(min=2, max=20)])
    email = StringField('Email', 
                            validators=[DataRequired(), Email()])
    password = PasswordField('Password', 
                            validators=[DataRequired(), Length(min=2, max=20)])
    password = PasswordField('Confirm Password', 
                            validators=[DataRequired(), EqualTo('password')])

    submit = SubmitField('Sign Up')
```

同理我们创建LoginForm

```python
class LoginForm(FlaskFrom):
    email = StringField('Email', 
                            validators=[DataRequired(), Email()])
    password = PasswordField('Password', 
                            validators=[DataRequired(), Length(min=2, max=20)])
    remember = BooleanField('Remember Me')
    submit = SubmitField('Login')
```

我们需要为我们的应用创建一个secret key, 这会保护cookie不被篡改 

```python
>>> import secrets
>>> secrets.token_hex(16)
'e7d21abac1602651114761c1cd93b24e'
```

goto tryflask.py

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200417171224032.png" alt="image-20200417171224032" style="zoom:50%;" />

```python
app.config['SECRET_KEY'] = 'e7d21abac1602651114761c1cd93b24e'
```

接下来我们创建login, register页面

<img src="/Users/jones/Desktop/miaochenlu.github.io/assets/images/image-20200417171536595.png" alt="image-20200417171536595" style="zoom:50%;" />

```python
@app.route("/register")
def register():
    form = RegistrationForm()
    return render_template('register.html', title="Register", form=form)

@app.route("/login")
def login():
    form = LoginForm()
    return render_template('login.html', title="Login", form=form)
```

<img src="/Users/jones/Desktop/miaochenlu.github.io/assets/images/image-20200417171755961.png" alt="image-20200417171755961" style="zoom:50%;" />

在register.html

```html
<div class="content-section">
        <form method="post" action="">
            {{ form.hidden_tag() }}
            <fieldset class="form-group">
                <legend class="border-bottom mb-4">Join Today</legend>
                <div class="form-group">
                    {{ form.username.label(class="form=control-label") }}
                    {{ form.username(class="form=control form-control-lg") }}
                </div>
                <div class="form-group">
                    {{ form.email.label(class="form=control-label") }}
                    {{ form.email(class="form=control form-control-lg") }}
                </div>
                <div class="form-group">
                    {{ form.password.label(class="form=control-label") }}
                    {{ form.password(class="form=control form-control-lg") }}
                </div>
                <div class="form-group">
                    {{ form.confirm_password.label(class="form=control-label") }}
                    {{ form.confirm_password(class="form=control form-control-lg") }}
                </div>
            </fieldset>
            <div class="from-group">
                {{ form.submit(class="btn btn-outline-info") }}
            </div>
        </form>
    </div>
```

这里的form.username, form.eail, form.password等等对应

<img src="/Users/jones/Desktop/miaochenlu.github.io/assets/images/image-20200417173617948.png" alt="image-20200417173617948" style="zoom:50%;" />

通常在register下面会有Already have an account?

因此我们增加一个

```html
<div class="border-top pt-3">
        <small class="text-muted">
            Already Have An Account? <a class="ml-2" href="{{ url_for('login') }}">Sign in</a>
        </small>
    </div>
```

注意，插入连接最好使用url_for函数，里面的参数对应的不是文件名路径名，是函数名

<img src="/Users/jones/Desktop/miaochenlu.github.io/assets/images/image-20200417173847162.png" alt="image-20200417173847162" style="zoom:50%;" />

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200417231125608.png" alt="image-20200417231125608" style="zoom:30%;" />

填入表单，然后submit, 结果是not allowed

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200417231257735.png" alt="image-20200417231257735" style="zoom:50%;" />

加上这一段后就允许submit了

<img src="/Users/jones/Desktop/miaochenlu.github.io/assets/images/image-20200417231405123.png" alt="image-20200417231405123" style="zoom:50%;" />

我们希望在register能够提供一些alerts, 比如注册成功，失败，名称不符合要求等等

用flash来辅助

<img src="/Users/jones/Desktop/miaochenlu.github.io/assets/images/image-20200417231630560.png" alt="image-20200417231630560" style="zoom:50%;" />

```python
@app.route("/register", methods=['GET', 'POST'])
def register():
    form = RegistrationForm()
    if form.validate_on_submit():
        flash(f'Account created for {form.username.data}!', 'success')
    return render_template('register.html', title="Register", form=form)
```

flash()的第二个参数代表alert的category, 比如success, warning, error

注册成功之后，我们想要重定向到home page, 因此我们需要redirect function

<img src="/Users/jones/Desktop/miaochenlu.github.io/assets/images/image-20200417232146395.png" alt="image-20200417232146395" style="zoom:50%;" />

<img src="/Users/jones/Desktop/miaochenlu.github.io/assets/images/image-20200417232251947.png" alt="image-20200417232251947" style="zoom:50%;" />

我们想要在网页中显示flash message, 还需要修改layout.html

<img src="/Users/jones/Desktop/miaochenlu.github.io/assets/images/image-20200417232946686.png" alt="image-20200417232946686" style="zoom:50%;" />

其中with_categories=true代表是否给了category参数信息，如'success'



<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200417232914016.png" alt="image-20200417232914016" style="zoom:50%;" />

接下来我们完善flash message, 如果出现错误，给予用户详细信息，那么用户就会知道如何修改

修改register.html

<img src="/Users/jones/Desktop/miaochenlu.github.io/assets/images/image-20200417233734626.png" alt="image-20200417233734626" style="zoom:50%;" />

password, confirm_password...其他字段同理

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200417234204386.png" alt="image-20200417234204386" style="zoom:50%;" />

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200417234238320.png" alt="image-20200417234238320" style="zoom:50%;" />

同理login.html

```html
{% extends "layout.html" %}
{% block content %}
    <div class="content-section">
        <form method="post" action="">
            {{ form.hidden_tag() }}
            <fieldset class="form-group">
                <legend class="border-bottom mb-4">Join Today</legend>
                <div class="form-group">
                    {{ form.email.label(class="form-control-label") }}
                    {% if form.email.errors %}
                        {{ form.email(class="form-control form-control-lg is-invalid") }}
                        <div class="invalid-feedback">
                            {% for error in form.email.errors%}
                                <span>{{ error }} </span>
                            {% endfor %}
                        </div>
                    {% else %}
                        {{ form.email(class="form-control form-control-lg") }}
                    {% endif %}
                </div>
                <div class="form-group">
                    {{ form.password.label(class="form-control-label") }}

                    {% if form.password.errors %}
                        {{ form.password(class="form-control form-control-lg is-invalid") }}
                        <div class="invalid-feedback">
                            {% for error in form.password.errors%}
                                <span>{{ error }} </span>
                            {% endfor %}
                        </div>
                    {% else %}
                        {{ form.password(class="form-control form-control-lg") }}
                    {% endif %}
                </div>
                <div class="form-check">
                    {{ form.remember(class="form-check-input") }}
                    {{ form.remember.label(class="form-check-label") }}
                </div>
            </fieldset>
            <div class="from-group">
                {{ form.submit(class="btn btn-outline-info") }}
            </div>
            <small class="test-muted ml-2">
                <a href="#">Forgot Password?</a>
            </small>
        </form>
    </div>
    <div class="border-top pt-3">
        <small class="text-muted">
            Need An Account? <a class="ml-2" href="{{ url_for('register') }}">Sign Up</a>
        </small>

    </div>
{% endblock content%}
```

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200417234739087.png" alt="image-20200417234739087" style="zoom:40%;" />

在tryflask.py中修改login函数

```python
@app.route("/login", methods=['GET', 'POST'])
def login():
    form = LoginForm()
    if form.validate_on_submit():
        if form.email.data == 'admin@blog.com' and form.password.data == 'password':
            flash('You have been logged in!', 'success')
            return redirect(url_for('home'))
        else:
            flash('Login Unsuccessful. Please check username and password', 'danger')
    return render_template('login.html', title="Login", form=form)
```

因为还没有数据库，我们假定"admin@blog.com"用户密码为'password'能登陆成功

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200417235217458.png" alt="image-20200417235217458" style="zoom:50%;" />

其他都会登陆失败

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200417235103525.png" alt="image-20200417235103525" style="zoom:50%;" />

在layout.html中我们的href都是direct link, 改成url_for

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200417235427482.png" alt="image-20200417235427482" style="zoom:50%;" />

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200417235543566.png" alt="image-20200417235543566" style="zoom:50%;" />

