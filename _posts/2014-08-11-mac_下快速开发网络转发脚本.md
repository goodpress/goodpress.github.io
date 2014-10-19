#mac 下快速开发网络转发脚本

1. 安装 mac python 开发环境
	- 安装flask
		- [flask 中文简介及安装方法](http://dormousehole.readthedocs.org/en/latest/installation.html#installation)
		- 命令行:

		```
		#sudo easy_install virtualenv
		$ mkdir myproject
		$ cd webtest01
		$ virtualenv venv
		New python executable in env/bin/python
		Installing setuptools............done.
		$ . venv/bin/activate
		$ pip install Flask
		
			
		```
		
		
		
		```python
		from flask import Flask, request
		import json

		app = Flask(__name__)

		@app.route('/',methods=['POST'])
		def foo():
  		data = json.loads(request.data)
  		print "New commit by: {}".format(data['commits'][0]['author']['name'])
  		return "OK"

		if __name__ == '__main__':
  			 app.run()
		```
		