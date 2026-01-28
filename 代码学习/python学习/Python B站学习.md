## 基础学习
###### 一些琐碎知识点
- ’‘’追着读
- 、（\）+符号使原本的
- 单\可以续行
- input可以做输出，后面括号接输入
- 数据类型（）可以直接转换
```python
#列表内容 
shoplist = ["niuzi","livre"]
shoplist.append("fenetre")
max,min,shorted#排序

#字典内容
zidian = {key:contenu，
          key2:contenu}
          
if A in zidian

#元组
yuanzu = ("yoann",32)#作为key
```
###### 字符串格式化内容
f-string方法
```python
year = input("请输入生肖（如：龙）：")
name = input("请输入收祝福人的名字：")
# f-string 生成祝福语
message_content = f"""律回春渐，新元肇启。
新岁甫至，福气东来。
金{year}贺岁，欢乐祥瑞。
金{year}敲门，五福临门。
给{name}及家人拜年啦！
新春快乐，{year}年大吉！"""
```
formath方法
```python
# 定义两个变量
year = "马"  # 可替换为生肖/年份，如"龙"、"2024"
name = "小明"  # 可替换为收祝福人的名字
# 方式1：使用位置占位符（{}），按变量传入顺序填充
message_content1 = """律回春渐，新元肇启。
新岁甫至，福气东来。
金{}贺岁，欢乐祥瑞。
金{}敲门，五福临门。
给{}及家人拜年啦！
新春快乐，{}年大吉！""".format(year, year, name, year)
# 方式2：使用命名占位符（{变量名}），按名称匹配填充（更易读，推荐）
message_content2 = """律回春渐，新元肇启。
新岁甫至，福气东来。
金{y}贺岁，欢乐祥瑞。
金{y}敲门，五福临门。
给{n}及家人拜年啦！
新春快乐，{y}年大吉！""".format(y=year, n=name)
```
###### 代码函数
```python
def Init_xu(a,x)
w=a**x
a=x
x=w
#外面都改了
```
###### 循环
```python
for name in contacts:
for i in range(1, 11):
for i in range(len(contacts)): 
print(f"第 {i+1} 个联系人：{contacts[i]}")
while user_input != correct_password:
    user_input = input("请输入密码：")
    if user_input != correct_password:
        print("密码错误，请重新输入！")
```
###### 库的使用
```python
#1
import math
math.midi()
#2
from math import median
print(median([12,-5,36]))
#3
from statistic import *
#后续直接用
```
###### 面向对象
```python
#构造函数
class stu:
	def _init_(self,name,number,address):
		self.name = name
		self.num  = number
		self.add  = address

student1 = stu("yoann",10010,"elie cartan")
print(f{student1.name})

#类内部函数调用
def print_grades(self):
        # 打印学生的成绩信息
        print(f"学生{self.name}（学号：{self.student_id}）的成绩为：")
        for course in self.grades:
            print(f"{course}: {self.grades[course]}分")
```