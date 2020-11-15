# Flask Vue.js全栈开发｜第1章：创建第一个Flask RESTful API

> Sijia Guo / 2020/11/13

------

> **Synopsis:** 本文是Flask+Vue.js全栈开发的第一篇，主要是以比较优化的结构搭建Flask应用，并提供一个测试的API，客户端访问 /ping 后会返回 Pong! 响应，用于下一篇前端Vue.js测试与后端的连通性



## 1.代码管理

### 1.1 安装git

	- 选择对应操作系统的Git进行下载；
	- 傻瓜式安装；

### 1.2 创建Github仓库

 - 登陆**Github**账户，创建个人项目，我的命名为**flask-vuejs-blog**；

 - 配置git：
```
git config --global user.email "your github email" # 设置email
git config --global user.name "your github name" # 设置name
git config user.email # 查看email
git config user.name  # 查看name
git config --global --unset user.email # 取消本地git的email绑定
```

- 在你的 Github 账户中添加该电脑的 **SSH keys**：
	- 在本地创建SSH Keys：
`ssh -keygen -t rsa -C "your email" `
	- 之后会要求确认路径和输入密码，一路回车就行。
	- 成功的话会在 对应文件夹（/User/xxx/）下生成 .ssh文件夹，进去，打开 id_rsa.pub，复制里面的key。
	- 复制全部内容，打开 GitHub 主页，左侧选择 SSH and GPG Keys， 点击 Add SSH Keys，然后输入名称，并将复制的内容粘贴过来，添加即可。
	- 验证 SSH Keys 是否添加成功
`ssh -T git@github.com`
	- 如果是第一次的会提示是否continue，输入yes就会看到：You've successfully authenticated, but GitHub does not provide shell access 。这就表示已成功连上github。


### 1.3 git clone
克隆到本地，打开 Git Bash：
```
$ cd /python-code/
$ git clone git@github.com:guosijia/flask-vuejs-blog.git 
```

### 1.4 init commit
添加 MIT License，然后第一次提交：
```
$ cd flask-vuejs-blog/
$ git status
On branch main
Your branch is up-to-date with 'origin/main'.

nothing to commit, working tree clean 
$ git add .
$ git commit -m "init"
$ git push
```

### 1.5 分支管理
创建 **dev** 开发分支：
```
$ git checkout -b dev
$ git branch
* dev
  main
```

## 2.配置Flask
安装 <b>Python 3</b>，请前往 <b>官方网站</b> 下载 对应操作系统的 对应版本
>  <b>Flask</b> 基础部分可阅读官方文档

打开 **cmd/终端** ，切换到项目目录，创建 **back-end** 目录，这是我们的后端API应用所在位置：
```
flask-vuejs-blog (dev) ✔ mkdir back-end
flask-vuejs-blog (dev) ✔ cd back-end
back-end (dev) ✗ python3 -m venv venv
back-end (dev) ✗ source venv/bin/activate
back-end (dev) ✗ pip install flask
back-end (dev) ✗ pip freeze > requirements.txt
```
使用 **Visual Studio Code** 打开项目，提供 **.gitignore** 文件：
```
.idea/
__pycache__/
venv/
.env
app.db
blog.log*
```
### 2.1 应用工厂
创建 **app** 目录，然后新建 **app/\__init__.py**，将使用**应用工厂函数**来创建 **Flask** 应用：
```
from flask import Flask
from config import Config


def create_app(config_class=Config):
    app = Flask(__name__)
    app.config.from_object(config_class)

    # 注册 blueprint
    from app.api import bp as api_bp
    app.register_blueprint(api_bp, url_prefix='/api')

    return app
```
### 2.2 API蓝图
创建 **app/api** 目录，然后新建 **api/\__init__.py**，定义蓝图：
```
from flask import Blueprint

bp = Blueprint('api', __name__)

# 写在最后是为了防止循环导入，ping.py文件也会导入 bp
from app.api import ping
```
定义我们第一个路由函数，当客户端访问 **/ping** 时返回包含 **Pong!** 的 **JSON** 数据，创建 **api/ping.py**：
```
from flask import jsonify
from app.api import bp


@bp.route('/ping', methods=['GET'])
def ping():
    '''前端Vue.js用来测试与后端Flask API的连通性'''
    return jsonify('Pong!')
```

### 2.3 启动应用文件
创建 back-end/blog.py：
```
from app import create_app

app = create_app()
```

### 2.4 配置文件
创建 **back-end/config.py**：
```
import os
from dotenv import load_dotenv

basedir = os.path.abspath(os.path.dirname(__file__))
load_dotenv(os.path.join(basedir, '.env'), encoding='utf-8')


class Config(object):
    pass
```
我们的 **Flask** 应用会使用很多系统环境变量，准备写在 **back-end/.env** 中，Python 可以使用 **python-dotenv** 这个包来读取环境变量信息，所以我们先安装这个包：
```
back-end (dev) ✗ pip install python-dotenv
back-end (dev) ✗ pip freeze > requirements.txt
```
创建 **back-end/.env**：
```
FLASK_APP=blog.py
FLASK_DEBUG=1
```
最终项目结构：
- FLASK-VUEJS-BLOG
	- back-end
		- \__pycache__
		- app
			- \__pycache__
			- api
				- \__pycache__
				- \__init__
				- ping.py
			- \__init.py
	- venv
	- .env
	- .gitignore
	- config.py
	- blog.py
	- requirements.txt
### 2.5 启动应用
```
(venv) ➜  back-end (dev) ✗ flask run
 * Serving Flask app "blog.py" (lazy loading)
 * Environment: production
   WARNING: Do not use the development server in a production environment.
   Use a production WSGI server instead.
 * Debug mode: on
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
 * Restarting with stat
 * Debugger is active!
 * Debugger PIN: 246-274-168
```
- 打开浏览器，访问 **http://127.0.0.1:5000/api/ping**
- 如果显示"Pong!"的话，说明我们定义的 **Ping-Pong** 测试路由正常
## 3.提交代码
```
$ git add .
$ git commit -m "1. 后端Flask RESTful API提供测试接口 Ping-Pong!"
$ git checkout main
$ git merge dev
$ git branch -d dev
$ git branch
```
将本地 **main** 分支代码上传到 Github 代码仓库中的 **main** 分支：
```
$ git push
或者：
$ git push -u origin main
```
打上标签 **tag**并上传：
```
$ git tag v0.1
$ git tag
v0.1
$ git show v0.1

1. 同步单个标签
$ git push origin v0.1

2. 同步所有标签
$ git push --tags
或者：
$ git push origin --tags
```




