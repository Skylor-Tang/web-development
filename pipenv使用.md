## pipenv使用

### 优势
+ 保存包的依赖关系，不需要requirements.txt文件，使用`pipenv graph`来查看包的依赖关系
+ 不需要先激活虚拟环境，只需要在虚拟环境的文件夹下直接使用`pipenv`命令即可，当然也可以使用`pipenv shell`激活当前的虚拟环境
	- 激活虚拟环境后可以使用Python自带的`pip list`来产看安装了哪些包，当然可以直接查看Pipfile文件，以及使用`pipenv graph`来查看包的依赖关系
+ 实际上是将虚拟环境和包管理联系起来，包的信息保存在项目中的Pipfile和Pipfile.lock中，代替了requirements.txt的作用，创建的虚拟环境需要和项目中的Pipfile、Pipfile.lock文件配合使用。在项目下使用`pipenv shell`激活相应的虚拟环境
+ 删除虚拟环境的时候不需要先激活虚拟环境 

### 安装
+ 使用`pip install pipenv`进行安装

### 使用
+ 使用之前先要创建一个文件夹，存档Pipfile文件
	- 建议修改Pipfile中的url，修改安装包时的源，改为国内的`https://mirrors.aliyun.com/pypi/simple`
+ `pipenv --three`会使用当前系统的Python3创建环境
+ 也可以使用`pipenv --python 版本号`来创建环境
+ 创建完成后，使用`pipenv shell`激活虚拟环境
	- 注意，在未创建虚拟环境的情况下，使用该命令，会在当前文件夹下创建以文件夹命名的虚拟环境并启用该虚拟环境
+ 注意以下三个命令只能在创建好的文件夹下（即含有Pipfile文件的文件夹）执行
	- 使用`pipenv --where`显示项目目录信息，为当前创建的文件夹的路径
	- 使用`pipenv --venv`显示虚拟环境定义的路径
	- 使用`pipenv --py`显示Python解释器路径

### 虚拟环境的使用
+ 在虚拟环境中`pipenv`基本代替`pip`的使用
+ 安装库，并添加到Pipfile中
	- `pipenv install 模块`进行相应的库的安装
    - `pipenv install 模块==版本号`可以安装指定版本的库  
    - 安装时，会创建Pipfile.lock文件，用来保证包不被恶意篡改，但是这个过程是耗时的，我们可以在安装模块的时候添加参数，跳过该步骤`pipenv install requests --skip-lock`
+ Pipfile文件的内容
```python
	[[source]]                                                                                                            
	name = "pypi"                                                                                                         
	url = "https://pypi.org/simple"                                                                                       
	verify_ssl = true                                                                                                     
	                                                                                                                      
	[dev-packages]                                                                                                        
	                                                                                                                      
	[packages]                                                                                                            
	                                                                                                                      
	[requires]                                                                                                            
	python_version = "3.7"      
```
	- 其中`[dev-packages]`为开发环境安装的包，使用`pipenv install --dev requests --skip-lock`创建在开发环境下的包

+ `pipenv graph`查看当前安装的库以及依赖
+ `pipenv update`会将当前安装的包全部卸载并安装最新的包
+ `pipenv check`检查安全漏洞
+ `pipenv uninstall --all`卸载全部包并从Pipfile中移除  
+ `exit`退出当前的虚拟环境
+ 使用`pipenv --rm`删除当前的虚拟环境