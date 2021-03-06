# Flask

### 安装

    pip install flask

***

### 程序基本结构

1. 初始化

所有 Flask 程序都必须创建一个程序实例。Web 服务器使用一种名为 Web 服务器网关接口（Web Server Gateway Interface，WSGI）的协议，把接收自客户端的所有请求都转交给这个对象处理。

    from flask import Flask
    app = Flask('__name__')

2. 路由和函数

程序实例需要知道对每个URL请求运行哪些代码，所以保存了一个URL到Python函数的映射关系。处理URL和函数之间关系的程序称为路由。
在Flask程序中定义路由的最简便方式，是使用程序实例提供的app.route修饰器，把修饰的函数注册为路由。

    @app.route('/')
    def index():
        return '<h1>Hello World!</h1>'

3. 启动服务

    if __name__ == '__main__'
        app.run(Debug=True)

***

### 模版

模板是一个包含响应文本的文件，其中包含用占位变量表示的动态部分，其具体值只在请求的上下文中才能知道。使用真实值替换变量，再返回最终得到的响应字符串，这一过程称为渲染。为了渲染模板，Flask 使用了一个名为 Jinja2 的强大模板引擎。

##### Jinja2模板引擎

templates/user.html：Jinja2 模板

    <h1>Hello {{ name }} !</h1>

1. 渲染模版

示例，使用render_template

    @app.route('/user/<name>')
    def user(name)
        return render_template('user.html',name=name)

2. 变量

Jinja2 能识别所有类型的变量，甚至是一些复杂的类型，例如列表、字典和对象。

    <p>A value from a dictionary: {{ mydict['key'] }}.</p>
    <p>A value from a list: {{ mylist[3] }}.</p>
    <p>A value from a list, with a variable index: {{ mylist[myintvar] }}.</p>
    <p>A value from an object's method: {{ myobj.somemethod() }}.</p>

可以使用过滤器修改变量，过滤器名添加在变量名之后，中间使用竖线分隔。

    Hello, {{ name|capitalize }} 


过滤器

    safe 渲染值时不转义
    capitalize 把值的首字母转换成大写，其他字母转换成小写
    lower 把值转换成小写形式
    upper 把值转换成大写形式
    title 把值中每个单词的首字母都转换成大写
    trim 把值的首尾空格去掉
    striptags 渲染之前把值中所有的 HTML 标签都删掉

3. 控制结构

Jinja2 提供了多种控制结构，可用来改变模板的渲染流程。

    {% 控制条件 %}

***

##### 使用Flask-Bootstrap集成Twitter Bootstrap

Bootstrap是Twitter开发的一个开源框架，它提供的用户界面组件可用于创建整洁且具有吸引力的网页，而且这些网页还能兼容所有现代 Web 浏览器。

    pip install flask-bootstrap

1. 初始化

示例

    from flask.ext.bootstrap import Bootstrap
    bootstrap = Bootstrap(app)
    
***

##### 自定义错误页面

    @app.errorhandler(404)
    def page_not_found(e):
     return render_template('404.html'), 404
    @app.errorhandler(500)
    def internal_server_error(e):
     return render_template('500.html'), 500

***

##### 链接

Flask 提供了 url_for() 辅助函数，它可以使用程序 URL 映射中保存的信息生成 URL。


##### 静态文件


***

##### 使用Flask-Moment本地化日期和时间

示例 

    pip install flask-moment
    from flask.ext.moment import Moment
    moment = Moment(app)

    {% block scripts %}
    {{ super() }}
    {{ moment.include_moment() }}
    {% endblock %}

    <p>The local date and time is {{ moment(current_time).format('LLL') }}.</p>

***

### WEB表单

示例

    pip install flask-wtf
    from flask.ext.wtf import Form
    from wtforms import StringField, SubmitField
    from wtforms.validators import Required
    class NameForm(Form):
        name = StringField('What is your name?', validators=[Required()])
        submit = SubmitField('Submit')

表单渲染

    {% import "bootstrap/wtf.html" as wtf %}
    {{ wtf.quick_form(form) }}

视图处理表单：

    form = NameForm()
    return render_template('index.html', form=form)

***

### 数据库

初始化

    pip install flask-sqlalchemy

    from flask.ext.sqlalchemy import SQLAlchemy
    basedir = os.path.abspath(os.path.dirname(__file__))
    app = Flask(__name__)
    app.config['SQLALCHEMY_DATABASE_URI'] =\
    'sqlite:///' + os.path.join(basedir, 'data.sqlite')
    app.config['SQLALCHEMY_COMMIT_ON_TEARDOWN'] = True
    db = SQLAlchemy(app)

模型定义

    class Role(db.Model):
        __tablename__ = 'roles'
        id = db.Column(db.Integer, primary_key=True)
        name = db.Column(db.String(64), unique=True)
        def __repr__(self):
            return '<Role %r>' % self.name

创建表

    db.create_all()

插入行

    from hello import Role
    admin_role = Role(name='Admin')
    db.session.add(admin_role)
    db.session.commit()

***

### 电子邮件

    pip install flask-mail

***

### 项目结构

多文件 Flask 程序的基本结构

flasky

* app/
    * templates/
    * static/
    * main/
        * __init__.py
        * errors.py
        * forms.py
        * views.py
    * __init__.py
    * email.py
    * models.py
 * migrations/
 * tests/
    * __init__.py
    * test*.py
 * venv/
 * requirements.txt
 * config.py
 * manage.py


这种结构有 4 个顶级文件夹：
* Flask 程序一般都保存在名为 app 的包中；
* 和之前一样，migrations 文件夹包含数据库迁移脚本；
* 单元测试编写在 tests 包中；
* 和之前一样，venv 文件夹包含 Python 虚拟环境。
同时还创建了一些新文件：
* requirements.txt 列出了所有依赖包，便于在其他电脑中重新生成相同的虚拟环境；
* config.py 存储配置；
* manage.py 用于启动程序以及其他的程序任务。
