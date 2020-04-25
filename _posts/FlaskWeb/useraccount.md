修改account.html使其可以展示username, email, image

account.html

```html
{% extends "layout.html" %}
{% block content %}
    <h1>{{ current_user.username }}</h1>
{% endblock content%}
```

我们在static中创建一个

<img src="/Users/jones/Desktop/miaochenlu.github.io/assets/images/image-20200421120428826.png" alt="image-20200421120428826" style="zoom:50%;" />

<img src="/Users/jones/Desktop/miaochenlu.github.io/assets/images/image-20200421120548049.png" alt="image-20200421120548049" style="zoom:50%;" />

<br>

修改route.py中的account()

<img src="/Users/jones/Desktop/miaochenlu.github.io/assets/images/image-20200421123230303.png" alt="image-20200421123230303" style="zoom:50%;" />

修改account.html

```html
{% extends "layout.html" %}
{% block content %}
    <div class="content-section">
        <div class="media">
            <img class="rounded-circle account-img" src="{{ image_file }}">
            <div class="media-body">
                <h2 class="account-heading">{{ current_user.username }}</h2>
                <p class="text-secondary">{{ current_user.email }}</p>
            </div>
        </div>
    </div>
{% endblock content%}
```

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200421123849941.png" alt="image-20200421123849941" style="zoom:50%;" />

<br>

为了修改account信息

在forms.py汇总增加UpdateAccountForm

```python
class UpdateAccountForm(FlaskForm):
    username = StringField('Username', 
                            validators=[DataRequired(), Length(min=2, max=20)])
    email = StringField('Email', 
                            validators=[DataRequired(), Email()])
    

    submit = SubmitField('Update')

    def validate_username(self, username):
        if username.data != current_user.username:
            user = User.query.filter_by(username=username.data).first()
            if user:
                raise ValidationError('Username is taken. Please choose a different one')
        
    def validate_email(self, email):
        if email.data != current_user.email:
            user = User.query.filter_by(email=email.data).first()
            if user:
                raise ValidationError('Email is taken. Please choose a different one')
```

account.html

```html
{% extends "layout.html" %}
{% block content %}
    <div class="content-section">
        <div class="media">
            <img class="rounded-circle account-img" src="{{ image_file }}">
            <div class="media-body">
                <h2 class="account-heading">{{ current_user.username }}</h2>
                <p class="text-secondary">{{ current_user.email }}</p>
            </div>
        </div>

        <form method="post" action="">
            {{ form.hidden_tag() }}
            <fieldset class="form-group">
                <legend class="border-bottom mb-4">Account Info</legend>
                <div class="form-group">
                    {{ form.username.label(class="form-control-label") }}

                    {% if form.username.errors %}
                        {{ form.username(class="form-control form-control-lg is-invalid") }}
                        <div class="invalid-feedback">
                            {% for error in form.username.errors%}
                                <span>{{ error }} </span>
                            {% endfor %}
                        </div>
                    {% else %}
                        {{ form.username(class="form-control form-control-lg") }}
                    {% endif %}
                </div>
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
            </fieldset>
            <div class="from-group">
                {{ form.submit(class="btn btn-outline-info") }}
            </div>
        </form>
    </div>
{% endblock content%}

```

routes.py

```python
@app.route("/account", methods=['GET', 'POST'])
@login_required
def account():
    form = UpdateAccountForm()
    if form.validate_on_submit():
        current_user.username = form.username.data
        current_user.email = form.email.data
        db.session.commit()
        flash('Your account has been updated', 'success')
        return redirect(url_for('account'))
    elif request.method == 'GET':
        form.username.data = current_user.username
        form.email.data = current_user.email
    image_file = url_for('static', filename="profile_pics/" + current_user.image_file)
    return render_template('account.html', title='Account', 
                            image_file=image_file, form=form)
```



<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200421133512667.png" alt="image-20200421133512667" style="zoom:50%;" />



为了修改image

在form.py import FileField, FileAllowed

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200421134527077.png" alt="image-20200421134527077" style="zoom:50%;" />

在account.html中增加一个picture文件栏

```html
<div class="form-group">
  {{ form.picture.label }}
  {{ form.picture(class="form-control-file") }}
  {% if form.picture.errors %}
    {% for error in form.picture.errors %}
    	<span class="text-danger">{{ error }}</span><br>
    {% endfor %}
  {% endif %}
</div>
```

在form中增加enctype

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200421134057552.png" alt="image-20200421134057552" style="zoom:50%;" />

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200421134437875.png" alt="image-20200421134437875" style="zoom:50%;" />





继续修改

在routes.py中

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200421141407863.png" alt="image-20200421141407863" style="zoom:50%;" />

```python
def save_picture(form_picture):
    random_hex = secrets.token_hex(8)
    _, f_ext = os.path.splitext(form_picture.filename) #比如default.jpg, _=default;f_ext=jpg
    picture_filename = random_hex + f_ext #重组了一个文件名
    picture_path = os.path.join(app.root_path, 'static/profile_pics', picture_filename)
    form_picture.save(picture_path)
    
    return picture_filename
 

@app.route("/account", methods=['GET', 'POST'])
@login_required
def account():
    form = UpdateAccountForm()
    if form.validate_on_submit():
        if form.picture.data:
            picture_file = save_picture(form.picture.data)
            current_user.image_file = picture_file
            
        current_user.username = form.username.data
        current_user.email = form.email.data
        db.session.commit()
        flash('Your account has been updated', 'success')
        return redirect(url_for('account'))
    elif request.method == 'GET':
        form.username.data = current_user.username
        form.email.data = current_user.email
    image_file = url_for('static', filename="profile_pics/" + current_user.image_file)
    return render_template('account.html', title='Account', 
                            image_file=image_file, form=form)
```

**secrets.token_hex([nbytes=None])**

以十六进制格式返回安全的随机文本字符串。该字符串具有nbytes随机字节，每个字节转换为两个十六进制数字。如果未提供nbytes，则使用合理的默认值。

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200421141053967.png" alt="image-20200421141053967" style="zoom:50%;" />



<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200421140200811.png" alt="image-20200421140200811" style="zoom:50%;" />

有一个问题是头像的图片太大, 我们想要在存储图片之前先调整他的大小

```shell
pip install Pillow
```

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200421141641380.png" alt="image-20200421141641380" style="zoom:50%;" />

```python
def save_picture(form_picture):
    random_hex = secrets.token_hex(8)
    _, f_ext = os.path.splitext(form_picture.filename) #比如default.jpg, _=default;f_ext=jpg
    picture_filename = random_hex + f_ext #重组了一个文件名
    picture_path = os.path.join(app.root_path, 'static/profile_pics', picture_filename)
    
    output_size = (125, 125)
    resizedImage = Image.open(form_picture)
    resizedImage.thumbnail(output_size)
    resizedImage.save(picture_path)
    return picture_filename
```

可以看到头像缩小了

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200421141917519.png" alt="image-20200421141917519" style="zoom:50%;" />

