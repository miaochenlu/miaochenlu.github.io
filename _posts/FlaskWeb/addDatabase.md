使用flask提供的函数，我们切换数据库也会比较容易

```shell
pip install flask-sqlalchemy
```

从flask_sqlalchemy import SQLAlchemy

并且我们要指名数据库类型以及存放路径

```python
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///site.db'
```

'///'代表该目录下

<img src="/Users/jones/Desktop/miaochenlu.github.io/assets/images/image-20200418000237317.png" alt="image-20200418000237317" style="zoom:50%;" />

创建db

```python
db = SQLAlchemy(app)
```



blog系统中主要存储的数据有两个: User以及Post

```python
class User(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(20), unique=True, nullable=False)
    email = db.Column(db.String(120), unique=True, nullable=False)
    image_file = db.Column(db.String(20), nullable=False, default='default.jpg')
    password = db.Column(db.String(60), nullable=False)

    def __repr__(self):
        return f"User('{self.username}'. '{self.email}', '{self.image_file}')"

class Post(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    title = db.Column(db.String(100), nullable=False)
    date_posted = db.Column(db.DateTime, nullable=False, default=datetime.utcnow)
    content = db.Column(db.Text, nullable=False)

    def __repr__(self):
        return f"Post('{self.title}'. '{self.date_posted}')"
```

在User和Post之间有一对多关系

<img src="/Users/jones/Desktop/miaochenlu.github.io/assets/images/image-20200418001945319.png" alt="image-20200418001945319" style="zoom:50%;" />

lazy: 指定sqlalchemy数据库什么时候加载数据
select: 就是访问到属性的时候，就会全部加载该属性的数据
joined: 对关联的两个表使用联接
subquery: 与joined类似，但使用子子查询
dynamic: 不加载记录，但提供加载记录的查询，也就是生成query对象

backref表示在表的对象访问User的时候的加载方式, 比如Post1.author

这里'Post'大写是因为这里指定的是Post这个class

<img src="/Users/jones/Desktop/miaochenlu.github.io/assets/images/image-20200418001958289.png" alt="image-20200418001958289" style="zoom:50%;" />

这里user.id小写是因为user是table(默认小写)

接下来在命令行中创建database

```shell
>>> from tryflask import db
/Users/jones/anaconda3/lib/python3.7/site-packages/flask_sqlalchemy/__init__.py:835: FSADeprecationWarning: SQLALCHEMY_TRACK_MODIFICATIONS adds significant overhead and will be disabled by default in the future.  Set it to True or False to suppress this warning.
  'SQLALCHEMY_TRACK_MODIFICATIONS adds significant overhead and '
>>> db.create_all()
>>> 
```

我们发现site.db被创建

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200418003039444.png" alt="image-20200418003039444" style="zoom:50%;" />

创建一些数据

```shell
>>> from tryflask import User, Post
>>> user_1 = User(username='jones', email='clmiao@zju.edu.cn', password='password')
>>> db.session.add(user_1)
>>> user_2 = User(username='john', email='j@zju.edu.cn', password='password')
>>> db.session.add(user_2)
>>> db.session.commit()
```

一些查找的方法

```shell
>>> User.query.all()
[User('jones'. 'clmiao@zju.edu.cn', 'default.jpg'), User('john'. 'j@zju.edu.cn', 'default.jpg')]
>>> User.query.first()
User('jones'. 'clmiao@zju.edu.cn', 'default.jpg')
>>> User.query.filter_by(username='jones').all()
[User('jones'. 'clmiao@zju.edu.cn', 'default.jpg')]
>>> User.query.filter_by(username='jones').first()
User('jones'. 'clmiao@zju.edu.cn', 'default.jpg')
>>> user = User.query.filter_by(username='jones').first()
>>> user
User('jones'. 'clmiao@zju.edu.cn', 'default.jpg')
>>> user.id
1
>>> user = User.query.get(1)
>>> user
User('jones'. 'clmiao@zju.edu.cn', 'default.jpg')
>>> user.posts
[]
```

再加入post

```shell
>>> post_1 = Post(title='Blog 1', content='First Post Content!', user_id = user.id)
>>> post_2 = Post(title='Blog 2', content='Second Post Content!', user_id = user.id)
>>> db.session.add(post_1)
>>> db.session.add(post_2)
>>> db.session.commit()
>>> post_1.author
User('jones'. 'clmiao@zju.edu.cn', 'default.jpg')
```

用\>>> db.drop_all()清除db

用\>>> db.create_all()创建db



封装成package

创建model.py

#### <img src="/Users/jones/Library/Application Support/typora-user-images/image-20200418135653344.png" alt="image-20200418135653344" style="zoom:50%;" />

models.py

```python
from tryflask import db
from datetime import datetime

class User(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(20), unique=True, nullable=False)
    email = db.Column(db.String(120), unique=True, nullable=False)
    image_file = db.Column(db.String(20), nullable=False, default='default.jpg')
    password = db.Column(db.String(60), nullable=False)
    posts = db.relationship('Post', backref='author', lazy=True)

    def __repr__(self):
        return f"User('{self.username}'. '{self.email}', '{self.image_file}')"

class Post(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    title = db.Column(db.String(100), nullable=False)
    date_posted = db.Column(db.DateTime, nullable=False, default=datetime.utcnow)
    content = db.Column(db.Text, nullable=False)
    user_id = db.Column(db.Integer, db.ForeignKey('user.id'), nullable=False)
    def __repr__(self):
        return f"Post('{self.title}'. '{self.date_posted}')"
```

tryflask中import User, Post

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200418135736876.png" alt="image-20200418135736876" style="zoom:50%;" />

结果失败

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200418135807278.png" alt="image-20200418135807278" style="zoom:50%;" />

这是因为在tryflask.py中

```python
from flask import Flask, render_template, url_for, flash, redirect
from flask_sqlalchemy import SQLAlchemy
from forms import RegistrationForm, LoginForm
from models import User, Post
```

然后我们去models.py中

```python
from tryflask import db
```

然而我们在tryflask中并没有运行到创建db的那一行，因此失败

<br>

我们修改

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200418140524889.png" alt="image-20200418140524889" style="zoom:50%;" />

在tryflask.py

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200418140545412.png" alt="image-20200418140545412" style="zoom:50%;" />

但是这种写法太丑了, 我们接下来turn it into package

创建一个文件夹， 增加一个文件\__init__.py

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200418140950100.png" alt="image-20200418140950100" style="zoom:50%;" />

将其他文件全部移动到tryflask这个文件夹

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200418141115643.png" alt="image-20200418141115643" style="zoom:50%;" />

\__init__.py

```python
from flask import Flask, render_template, url_for, flash, redirect
from flask_sqlalchemy import SQLAlchemy
from forms import RegistrationForm, LoginForm

app = Flask(__name__)
app.config['SECRET_KEY'] = 'e7d21abac1602651114761c1cd93b24e'
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///site.db'
db = SQLAlchemy(app)
```

创建一个文件routes.py来存放路径

routes.py

```python

from models import User, Post

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

@app.route("/register", methods=['GET', 'POST'])
def register():
    form = RegistrationForm()
    if form.validate_on_submit():
        flash(f'Account created for {form.username.data}!', 'success')
        return redirect(url_for('home'))
    return render_template('register.html', title="Register", form=form)

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

那我们的tryflask.py中就只剩下

```python
from tryflask import app    # in __init__.py
if __name__ == '__main__':
    app.run(debug=True)
```

那这样的话我们就把tryflask.py改名为run.py

修改各个文件的import关系

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200418142213223.png" alt="image-20200418142213223" style="zoom:50%;" />

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200418142236819.png" alt="image-20200418142236819" style="zoom:50%;" />

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200418142337236.png" alt="image-20200418142337236" style="zoom:50%;" />

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200418142311716.png" alt="image-20200418142311716" style="zoom:50%;" />

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200418142359531.png" alt="image-20200418142359531" style="zoom:50%;" />

运行成功

再来创建数据库

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200418142929788.png" alt="image-20200418142929788" style="zoom:50%;" />

<br>

<br>

因为要对用户密码信息进行加密，因此使用bcrypt

```shell
pip install flask-bcrypt
```

```python
>>> from flask_bcrypt import Bcrypt
>>> bcrypt = Bcrypt()
>>> bcrypt.generate_password_hash('testing')
b'$2b$12$Amqdx5yEaS3uVMfbqZHi2eFpPz8g1Kras47HrYFqjGY5nCEQg3YLa'
>>> bcrypt.generate_password_hash('testing').decode('utf-8')
'$2b$12$1Wyt0VrGmjIqpU6YC9VHhuSC33fzP8pfAy.HyFLZCa6D/87joYheS'
>>> bcrypt.generate_password_hash('testing').decode('utf-8')
'$2b$12$PzfIKzRT.MaHTmP3f1DfNeVYOAX/5ZacRC73VHJPdYZX9j5Y1GPbe'
>>> hashed_pw = bcrypt.generate_password_hash('testing').decode('utf-8')
>>> bcrypt.check_password_hash(hashed_pw, 'password')
False
>>> bcrypt.check_password_hash(hashed_pw, 'testing')
True
>>> 
```

我们将bcrypt加入blog

\__init__.py

<img src="/Users/jones/Desktop/miaochenlu.github.io/assets/images/image-20200418144820173.png" alt="image-20200418144820173" style="zoom:50%;" />

routes.py

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200418145023847.png" alt="image-20200418145023847" style="zoom:50%;" />

```python
@app.route("/register", methods=['GET', 'POST'])
def register():
    form = RegistrationForm()
    if form.validate_on_submit():
        hashed_password = bcrypt.generate_password_hash(form.password.data).decode('utf-8')
        user = User(username=form.username.data, email=form.email.data, password=hashed_password)
        db.session.add(user)
        db.session.commit()
        flash('Your account has been created! You are now able to log in', 'success')
        return redirect(url_for('login'))
    return render_template('register.html', title="Register", form=form)
```



<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200418153658861.png" alt="image-20200418153658861" style="zoom:50%;" />

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200418153801724.png" alt="image-20200418153801724" style="zoom:50%;" />

```python
>>> from tryflask import db
>>> from tryflask.models import User
>>> user = User.query.first()
>>> user
User('jones'. 'clmiao@zju.edu.cn', 'default.jpg')
>>> user.password
'$2b$12$U3a0VChFWPFwb29H8NwUvOg6OdnE9sGzXkOT8Amz9Gj.sJmGJ39ee'
>>> 
```

如果再注册一个相同的用户就会出现error, 因为我们要求unique

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200418153658861.png" alt="image-20200418153658861" style="zoom:50%;" />

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200418154116475.png" alt="image-20200418154116475" style="zoom:50%;" />

我们在froms.py中的RegistrationForm中创建以下形式的函数

```python
def validate_field(self, field):
        if True:
            raise ValidationError('Validation Message')
```

<img src="/Users/jones/Desktop/miaochenlu.github.io/assets/images/image-20200418155115159.png" alt="image-20200418155115159" style="zoom:50%;" />

```python
class RegistrationForm(FlaskForm):
    # username
    username = StringField('Username', 
                            validators=[DataRequired(), Length(min=2, max=20)])
    email = StringField('Email', 
                            validators=[DataRequired(), Email()])
    password = PasswordField('Password', 
                            validators=[DataRequired(), Length(min=2, max=20)])
    confirm_password = PasswordField('Confirm Password', 
                            validators=[DataRequired(), EqualTo('password')])

    submit = SubmitField('Sign Up')

    def validate_username(self, username):
        user = User.query.filter_by(username=username.data).first()
        if user:
            raise ValidationError('Username is taken. Please choose a different one')
    
    def validate_email(self, email):
        user = User.query.filter_by(email=email.data).first()
        if user:
            raise ValidationError('Email is taken. Please choose a different one')
```

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200418155021043.png" alt="image-20200418155021043" style="zoom:50%;" />

login

```shell
pip install flask-login
```

<img src="/Users/jones/Desktop/miaochenlu.github.io/assets/images/image-20200418155424091.png" alt="image-20200418155424091" style="zoom:50%;" />

<img src="/Users/jones/Desktop/miaochenlu.github.io/assets/images/image-20200418160719533.png" alt="image-20200418160719533" style="zoom:50%;" />



<img src="/Users/jones/Desktop/miaochenlu.github.io/assets/images/image-20200418160620254.png" alt="image-20200418160620254" style="zoom:50%;" />

```python
@app.route("/login", methods=['GET', 'POST'])
def login():
    form = LoginForm()
    if form.validate_on_submit():
        user = User.query.filter_by(email=form.email.data).first()
        if user and bcrypt.check_password_hash(user.password, form.password.data):
            login_user(user, remember=form.remember.data)
            return redirect(url_for('home'))
        else:
            flash('Login Unsuccessful. Please check email and password', 'danger')
    return render_template('login.html', title="Login", form=form)
```



<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200418160320070.png" alt="image-20200418160320070" style="zoom:50%;" />

一个问题是login了以后还能看到

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200418160532145.png" alt="image-20200418160532145" style="zoom:50%;" />

在route.py中

<img src="/Users/jones/Desktop/miaochenlu.github.io/assets/images/image-20200418161215888.png" alt="image-20200418161215888" style="zoom:50%;" />

<img src="/Users/jones/Desktop/miaochenlu.github.io/assets/images/image-20200418161232543.png" alt="image-20200418161232543" style="zoom:50%;" />

<img src="/Users/jones/Desktop/miaochenlu.github.io/assets/images/image-20200418161245200.png" alt="image-20200418161245200" style="zoom:50%;" />

增加一个logout

```python
@app.route("/logout")
def logout():
    logout_user()
    return redirect(url_for('home'))
```

修改layout.html

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200418161544954.png" alt="image-20200418161544954" style="zoom:50%;" />

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200418161640611.png" alt="image-20200418161640611" style="zoom:50%;" />



同时，我们想要用户登陆后能够查看自己的账户，因此我们增加一个account界面

在routes.py中

```python
@app.route("/account")
def account():
    return render_template('account.html', title='Account')
```

增加account.html

```html
{% extends "layout.html" %}
{% block content %}
    <h1>{{ current_user.username }}</h1>
{% endblock content%}
```

修改layout.html

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200418162111100.png" alt="image-20200418162111100" style="zoom:50%;" />

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200418162211261.png" alt="image-20200418162211261" style="zoom:50%;" />

logout了之后，再进入account界面, 能够进入界面，不显示任何信息

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200418162418141.png" alt="image-20200418162418141" style="zoom:50%;" />



我们希望用户登陆后才能进入该页面，因此

在routes.py中

<img src="/Users/jones/Desktop/miaochenlu.github.io/assets/images/image-20200418162511902.png" alt="image-20200418162511902" style="zoom:50%;" />

<img src="/Users/jones/Desktop/miaochenlu.github.io/assets/images/image-20200418162531729.png" alt="image-20200418162531729" style="zoom:50%;" />

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200418162657676.png" alt="image-20200418162657676" style="zoom:50%;" />

在\__init__.py中加入

<img src="/Users/jones/Desktop/miaochenlu.github.io/assets/images/image-20200418162833135.png" alt="image-20200418162833135" style="zoom:50%;" />

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200418162817396.png" alt="image-20200418162817396" style="zoom:50%;" />

我们可以看到url

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200418163415172.png" alt="image-20200418163415172" style="zoom:50%;" />

也就是说登陆后想直接跳到account界面，但是我们的login函数是redirect到homepage的

在routes.py中

<img src="/Users/jones/Desktop/miaochenlu.github.io/assets/images/image-20200418163502615.png" alt="image-20200418163502615" style="zoom:50%;" />

<img src="/Users/jones/Desktop/miaochenlu.github.io/assets/images/image-20200418164003928.png" alt="image-20200418164003928" style="zoom:50%;" />

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200418163944232.png" alt="image-20200418163944232" style="zoom:50%;" />

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200418163903546.png" alt="image-20200418163903546" style="zoom:50%;" />