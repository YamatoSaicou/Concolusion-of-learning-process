## 大的方面
1. 使用**虚拟环境**，让部署变的方便和规范。可以借助第三方工具virtualenv很方便的搭建虚拟环境，开发完成后，使用pip来输出requirements文件
```Python
pip freeze >requirements.txt
```
2. 为了使得程序可以动态修改程序配置，使用**程序工厂函数**来搭建程序，使用**蓝本**Blueprint来定义路由。

3. MySql数据库：部署的时候，ubuntu系统自动安装的Mysql会出现默认无密码的情况，需要手动添加root密码后加入程序配置，否则python程序会提示权限不足。

4. 电子邮件支持，理论上讲通过Flask-Mail这个flask框架提供的第三方库，可以很容易的提供电子邮件支持，但是在实际配置中还是出现了很多问题：
1) 为了保护邮箱账户安全，不能把账户密令直接写入脚本。为了保护账户信息，你需要让脚本从环境变量中导入敏感信息。但是要注意，在设置环境变量时，使用pycharm自带的终端或者cmd去配置的时候，根据环境不同，有时候要在账户信息上加上''，可以单独写一个脚本获取一下账户信息到底正不正确。

2) 注意使用的smtp服务器，如果使用的是谷歌邮箱来发送邮件，那么配置就应该是:
```python
app.config['MAIL_SERVER'] = 'smtp.googlemail.com' 
app.config['MAIL_PORT'] = 587 
```
同时需要手动到谷歌邮箱中开启“允许低安全性应用访问”。其他的邮箱需要自己查询一下端口和ip，并且额外将邮箱密钥（不是密码）加入到账户信息中。

5. 利用flask提供RESTful API.

6. 部署相关：
1) 云服务器相关：大多数的云服务器需要**配置安全组**，我自己使用的是亚马逊aws云服务器，需要特别配置安全组，允许tcp协议的XXXX（你的程序的端口号）流量的进站与出站，不然外界没有办法访问！

2) 数据库：安装完成以及配置root用户之后，要手动创建一个数据库，名字与项目中的配置文件相同，然后使用python manage.py deploy，调用提前写好的配置函数，在数据库中生成对应的表和列。

3) 在服务器中需要让终端长时间运行，并且不随shell关闭而停止，故一般使以下命令:
```python
nohup python manage.py runserver --host 0.0.0.0 --port 5000 &        #5000是你配置的端口号
```

## 具体的细节
### REST

1. REST架构特征:
* 客户端-服务器
客户端和服务器之间必须有明确的界线。
* 无状态
客户端发出的请求中必须包含所有必要的信息。服务器不能在两次请求之间保存客户端的任何状态。
* 缓存
服务器发出的响应可以标记为可缓存或不可缓存，这样出于优化目的，客户端（或客户端和服务器之间的中间服务）可以使用缓存。
* 接口统一
客户端访问服务器资源时使用的协议必须一致，定义良好，且已经标准化。REST Web服务最常使用的统一接口是 HTTP 协议。
* 系统分层
在客户端和服务器之间可以按需插入代理服务器、缓存或网关，以提高性能、稳定性和伸缩性。
* 按需代码
客户端可以选择从服务器上下载代码，在客户端的环境中执行。
2. 资源就是一切，资源是 REST 架构方式的核心概念。
  在 REST 架构中，资源是程序中我们要着重关注的事物。例如，在博客程序中，用户、博客文章和评论都是资源。

  每个资源都要使用唯一的 URL 表示。还是以博客程序为例，一篇博客文章可以使用 URL /api/posts/12345 表示，其中 12345 是这篇文章的唯一标识符，使用文章在数据库中的主键表示。URL 的格式或内容无关紧要，只要资源的 URL 只表示唯一的一个资源即可。

  某一类资源的集合也要有一个 URL。博客文章集合的 URL 可以是 /api/posts/，评论集合的URL 可以是 /api/comments/。

  注： 注意，Flask 会特殊对待末端带有斜线的路由。如果客户端请求的 URL 的末端没有斜线，而唯一匹配的路由末端有斜线，Flask 会自动响应一个重定向，转向末端带斜线的 URL。反之则不会重定向。
  
3. 请求方法：

  客户端程序在建立起的资源 URL 上发送请求，使用请求方法表示期望的操作。若要获取编号为 12345 的博客文章，客户端可以向 http://www.example.com/api/posts/12345 发送 GET 请求。若要从博客 API 中获取现有博客文章的列表，客户端可以向 http://www.exam-ple.com/api/posts/ 发送 GET 请求。

  注：REST 架构不要求必须为一个资源实现所有的请求方法。如果资源不支持客户端使用的请求方法，响应的状态码为 405，返回“不允许使用的方法”。 Flask 会自动处理这种错误。

4. 请求和响应的主体

  资源在客户端和服务器之间来回传送，但 REST 没有指定编码资源的方式。请求和响应中的 Content-Type 首部用于指明主体中资源的编码方式。使用HTTP 协议中的内容协商机制，可以找到一种客户端和服务器都支持的编码方式。
  
REST Web 服务常用的两种编码方式是 JavaScript 对象表示法（JavaScript Object Notation， JSON）和可扩展标记语言（Extensible Markup Language，XML）

5. 版本。

服务器可以随时更新Web 浏览器中的客户端，但无法强制更新智能手机中的应用，更新前先要获得机主的许可。即便机主想进行更新，也不能保证新版应用上传到所有应用商店的时机都完全吻合新服务器端版本的部署。

基于以上原因，Web 服务的容错能力要比一般的 Web 程序强，而且还要保证旧版客户端能继续使用。这一问题的常见解决办法是使用版本区分 Web 服务所处理的  URL。例如，首次发布的博客 Web 服务可以通过 /api/v1.0/posts/ 提供博客文章的集合。

在 URL 中加入 Web 服务的版本有助于条理化管理新旧功能，让服务器能为新客户端提供新功能，同时继续支持旧版客户端。博客服务可能会修改博客文章使用的    JSON 格式，同时通过 /api/v1.1/posts/ 提供修改后的博客文章，而客户端仍能通过 /api/v1.0/posts/ 获取旧的JSON 格式。在一段时间内，服务器要同时    处理 v1.1 和 v1.0 这两个版本的 URL。

### 使用Flask提供REST web 服务

1. REST API 相关的路由是一个自成一体的程序子集，所以为了更好地组织代码，我们最好把这些路由放到独立的蓝本中，在这个 API 蓝本中，各资源分别在不同的模块中实现。蓝本中还包含处理认证、错误以及提供自定义修饰器的模块。(这些模块是.py文件，里面定义好了对应功能的路由)
```python
from flask import Blueprint 
api = Blueprint('api', __name__) 
from . import authentication, posts, users, comments, errors
```
```python
def create_app(config_name): 
    #.....
    from .api_1_0 import api as api_1_0_blueprint 
    app.register_blueprint(api_1_0_blueprint, url_prefix='/api/v1.0')
```
2. 错误处理：
  * 为所有客户端生成适当响应的一种方法是，在错误处理程序中根据客户端请求的格式改写响应，这种技术称为内容协商。下面的例子是改进后的 404 错误处理程序，它向 Web 服务客户端发送 JSON 格式响应，除此之外都发送 HTML 格式响应。500 错误处理程序的写法类似。
```python
@main.app_errorhandler(404)
def page_not_found(e): 
    if request.accept_mimetypes.accept_json and \ 
        not request.accept_mimetypes.accept_html: 
      response = jsonify({'error': 'not found'}) 
      response.status_code = 404 
      return response 
    return render_template('404.html'), 404
```
  * 这个新版错误处理程序检查 Accept 请求首部（Werkzeug 将其解码为 request.accept_ mimetypes），根据首部的值决定客户端期望接收的响应格式。浏览器一般不限制响应的格式，所以只为接受 JSON 格式而不接受 HTML 格式的客户端生成 JSON 格式响应。其他状态码都由 Web 服务生成，因此可在蓝本的errors.py 模块作为辅助函数实现。
```python
def unauthorized(message):
    response = jsonify({'error': 'unauthorized', 'message': message})
    response.status_code = 401
    return response
```
3. 用户认证:
  * 和普通的 Web 程序一样，Web 服务也需要保护信息，确保未经授权的用户无法访问。为此，RIA 必须询问用户的登录密令，并将其传给服务器进行验证。前面说过，REST Web 服务的特征之一是无状态，即服务器在两次请求之间不能“记住”客户端的任何信息。客户端必须在发出的请求中包含所有必要信息，因此所有请求都必须包含用户密令。程序当前的登录功能是在 Flask-Login 的帮助下实现的，可以把数据存储在用户会话中。默认情况下，Flask 把会话保存在客户端 cookie 中，因此服务器没有保存任何用户相关信息，都转交给客户端保存。这种实现方式看起来遵守了 REST 架构的无状态要求，但在 REST Web 服务中使用 cookie 有点不现实，因为 Web 浏览器之外的客户端很难提供对 cookie 的支持。鉴于此，使用 cookie 并不是一个很好的设计选择。
  * REST 架构基于 HTTP 协议，所以发送密令的最佳方式是使用 HTTP 认证。
  * 基于令牌的认证：使用基于令牌的认证方案时，客户端要先把登录密令发送给一个特殊的 URL，从而生成认证令牌。一旦客户端获得令牌，就可用令牌代替登录密令认证请求。出于安全考虑，令牌有过期时间。令牌过期后，客户端必须重新发送登录密令以生成新令牌。令牌落入他人之手所带来的安全隐患受限于令牌的短暂使用期限。
4. 资源和JSON的序列化转换
  * 开发 Web 程序时，经常需要在资源的内部表示和 JSON 之间进行转换。JSON 是 HTTP 请求和响应使用的传输格式。例子是新添加到 Post 类中的 to_json() 方法以及利用json数据创建post的方法。
 ```python
 class Post(db.Model): 
     # ... 
    def to_json(self): 
       json_post = { 
            'url': url_for('api.get_post', id=self.id, _external=True), 'body': self.body, 
            'body_html': self.body_html, 
            'timestamp': self.timestamp, 
            'author': url_for('api.get_user', id=self.author_id, 
                       _external=True), 
            'comments': url_for('api.get_post_comments', id=self.id, 
                          _external=True) 
            'comment_count': self.comments.count() 
            } 
    return json_post
    
    @staticmethod 
    def from_json(json_post):
       body = json_post.get('body') 
       if body is None or body == '': 
          raise ValidationError('post does not have a body') #在实现过程中只选择使用 JSON 字典中的 body 属性，而把 body_html属性忽略了，因为只要 body 属性的值发生变化，就会触发一个 SQLAlchemy 事件，自动在服务器端渲染 Markdown。
    return Post(body=body)
```
  * url、author 和 comments 字段要分别返回各自资源的 URL，因此它们使用 url_for() 生成，所调用的路由即将在 API 蓝本中定义。注意，所有 url_for() 方法都指定了参数 _ external=True，这么做是为了生成完整的 URL，而不是生成传统 Web 程序中经常使用的相对 URL。
5. 实现GET方法,返回单篇博客文章，如果在数据库中没找到指定 id 对应的文章，则返回 404错误。
```python
@api.route('/posts/<int:id>')
def get_post(id):
    post = Post.query.get_or_404(id)    #最短路径效应
    return jsonify(post.to_json())
```
6. 实现post方法，插入新资源。
```python
@api.route('/posts/', methods=['POST'])
@permission_required(Permission.WRITE_ARTICLES)
def new_post():
    post = Post.from_json(request.json)
    post.author = g.current_user
    db.session.add(post)
    db.session.commit()
    return jsonify(post.to_json()), 201, \
        {'Location': url_for('api.get_post', id=post.id, _external=True)}
```
7. 实现put方法，更新资源。
```python
@api.route('/posts/<int:id>', methods=['PUT'])
@permission_required(Permission.WRITE_ARTICLES)
def edit_post(id):
    post = Post.query.get_or_404(id)
    if g.current_user != post.author and \
            not g.current_user.can(Permission.ADMINISTER):
        return forbidden('Insufficient permissions')
    post.body = request.json.get('body', post.body)
    db.session.add(post)
    return jsonify(post.to_json())
```
### 程序结构

1. 最简单的初始化方式与程序结构。
  * 所有 Flask 程序都必须创建一个程序实例。Web 服务器使用一种名为 Web 服务器网关接口（Web Server Gateway Interface，WSGI）的协议，把接收自客户端的所有请求都转交给这个对象处理。程序实例是Flask 类的对象，经常使用下述代码创建：
```python
from flask import 
Flask app = Flask(__name__)
```
  * 客户端（例如 Web 浏览器）把请求发送给 Web 服务器，Web 服务器再把请求发送给 Flask 程序实例。程序实例需要知道对每个 URL 请求运行哪些代码，所以保存了一个 URL 到Python 函数的映射关系。处理 URL 和函数之间关系的程序称为**路由**。在 Flask 程序中定义路由的最简便方式，是使用程序实例提供的 app.route 修饰器，把修饰的函数注册为路由。下面的例子说明了如何使用这个修饰器声明路由：
 
```python
@app.route('/') #修饰器是 Python 语言的标准特性，可以使用不同的方式修改函数的行为。惯常用法是使用修饰器把函数注册为事件的处理程序。
def index(): 
    return '<h1>Hello World!</h1>'  #你想做的事情
```
  * 这个例子把 index() 函数注册为程序根地址的处理程序。如果部署程序的服务器域名为 www. example.com，在浏览器中访问 http://www.example.com 后，会触发服务器执行 index() 函数。这个函数的返回值称为响应，是客户端接收到的内容。如果客户端是 Web 浏览器，响应就是显示给用户查看的文档。
像 index() 这样的函数称为**视图函数**（view function）
  * 动态路由:下例定义的路由中就有一部分是动态名字,尖括号中的内容就是动态部分，任何能匹配静态部分的 URL 都会映射到这个路由上。调用视图函数时，Flask 会将动态部分作为参数传入函数。在这个视图函数中，参数用于生成针对个人的欢迎消息。
 ```python
 @app.route('/user/<name>')
 def user(name): 
     return '<h1>Hello, %s!</h1>' % name
 ```
2. 使用程序工厂函数
  * 在单个文件中开发程序很方便，但却有个很大的缺点，因为程序在全局作用域中创建，所以无法动态修改配置。运行脚本时，程序实例已经创建，再修改配置为时已晚。
  * 解决方法是延迟创建程序实例，把创建过程移到可显式调用的工厂函数中。这种方法不仅可以给脚本留出配置程序的时间，还能够创建多个程序实例。
  * 程序的工厂函数在 app 包的构造文件(__init__)中定义，构造文件导入了大多数正在使用的 Flask 扩展。配置对象，则可以通过名字从 config 字典中选择。程序创建并配置好后，就能初始化扩展了。在之前创建的扩展对象上调用init_app() 可以完成初始化过程。
3. 在蓝本中实现程序功能
  * 转换成程序工厂函数的操作让定义路由变复杂了。在单脚本程序中，程序实例存在于全局作用域中，路由可以直接使用 app.route 修饰器定义。但现在程序在运行时创建，只有调用 create_app() 之后才能使用 app.route 修饰器，这时定义路由就太晚了。和路由一样，自定义的错误页面处理程序也面临相同的困难，因为错误页面处理程序使用 app. errorhandler 修饰器定义。
  * 蓝本提供了更好的解决方法。蓝本和程序类似，也可以定义路由。不同的是，在蓝本中定义的路由处于休眠状态，直到蓝本注册到程序上后，路由才真正成为程序的一部分。和程序一样，蓝本可以在单个文件中定义，也可使用更结构化的方式在包中的多个模块中创建。
  * 通过实例化一个 Blueprint 类对象可以创建蓝本。这个构造函数有两个必须指定的参数：
`python
from flask import Blueprint 
main = Blueprint('main', __name__) 
from . import views, errors
`
     * 蓝本的名字和蓝本所在的包或模块。和程序一样，大多数情况下第二个参数使用 Python 的__name__ 变量即可。
     * 程序的路由保存在包里的 app/main/views.py 模块中，而错误处理程序保存在 app/main/errors.py 模块中。导入这两个模块就能把路由和错误处理程序与蓝本关联起来。注意，这些模块在 app/main/__init__.py 脚本的末尾导入(这些模块是.py文件，里面存放了定义好的类和函数)
     * 蓝本在工厂函数 create_app() 中注册到程序上
 ```python
     # ... 
    from .main import main as main_blueprint    
    app.register_blueprint(main_blueprint) 
    app.register_blueprint(auth_blueprint, url_prefix='/auth')  #这一条的路由是在/auth/之后生效的
    return app
 ```
 4. 在蓝本中定义路由与错误处理程序
 
  * 在蓝本中编写错误处理程序稍有不同，如果使用errorhandler 修饰器，那么只有蓝本中的错误才能触发处理程序。要想注册程序全局的错误处理程序，必须使用app_errorhandler。
``python
 from flask import render_template from . import main 
@main.app_errorhandler(404) 
def page_not_found(e): 
    return render_template('404.html'), 404 
``
  * 在蓝本中编写视图函数主要有两点不同：第一，和前面的错误处理程序一样，路由修饰器由蓝本提供；第二，url_for() 函数的用法不同。你可能还记得url_for() 函数的第一个参数是路由的端点名，在程序的路由中，默认为视图函数的名字。例如，在单脚本程序中，index() 视图函数的 URL 可使用url_for('index') 获取。
``python
@main.route('/', methods=['GET', 'POST']) 
def index(): 
.....
``

