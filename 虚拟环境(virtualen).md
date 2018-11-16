##使用virtualenv 模拟环境
1. 下载环境
pip3 install virtualenv
2. 创建
virtualenv --no-site-packages venv(文件夹) (添加参数之后，python环境都不会复制过来)
3. 开启
source venv/bin/activate
4. 关闭
deactivate 
##依赖
* 生成依赖：pip freeze > requirements.txt
* 安装依赖： pip install -r requirements.txt
（如果使用pycharm，需要进入Preferences>>project>>project Interpreter指定环境）