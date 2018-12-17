#python函数
（1）时间相差 
距离当前时间的几个月
from dateutil.relativedelta import relativedelta
end_time = start_time + relativedelta(months=int(dic['txsx']))
（2）文件
open() 函数常用形式是接收两个参数：文件名(file)和模式(mode)。
open(file, mode='r')
完整格式
open(file, mode='r', buffering=-1, encoding=None, errors=None, newline=None, closefd=True, opener=None)
>file: 必需，文件路径（相对或者绝对路径）。
mode: 可选，文件打开模式
buffering: 设置缓冲
encoding: 一般使用utf8
errors: 报错级别
newline: 区分换行符
closefd: 传入的file参数类型
opener:
rb:以二进制格式打开一个文件用于只读。文件指针将会放在文件的开头。这是默认模式。一般用于非文本文件如图片等
wb:以二进制格式打开一个文件用于读写,一般用于非文本文件如图片等.
ab:以二进制格式打开一个文件用于追加.

(3)you-get

(4)random

random.random() 用于生成一个随机浮点数

random.choice(sequence)    从序列中获取一个随机元素，参数sequence表示一个有序类型，并不是一种特定类型，泛指list，tuple，字符串等

random.sample(sequence,k)    从指定序列中随机获取k个元素作为一个片段返回，sample函数不会修改原有序列

(5)string

string.ascii_letters 生成所有字母，从a-z和A-Z

string.digits 是生成所有数字0-9.

string.punctuation 是生成各种符号,!"#$%&\'()*+,-./:;<=>?@[\\]^_`{|}~