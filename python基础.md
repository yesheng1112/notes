

## 变量和简单的数据类型

### 变量

- 变量命名的规则
  
  - 变量名只能包含字母、 数字和下划线。 变量名可以字母或下划线打头， 但不能以数字打头
     	
     	- 变量名不能包含空格， 但可使用下划线来分隔其中的单词
   	   	- 不要将Python关键字和函数名用作变量名， 即不要使用Python保留用于特殊用途的单词
   	   	- 变量名应既简短又具有描述性
   	   - 慎用小写字母l和大写字母O， 因为它们可能被人错看成数字1和0
  
- 错误
  
   ```python
    # 名称错误通常意味着两种情况： 要么是使用变量前忘记了给它赋值， 要么是输入
    # 变量名时拼写不正确。
    NameError: name 'mesage' is not defined    
   ```

### 字符串

- 常用方法

  - title()方法：以首字母的方式显示每一个单词

  	```python
    name = "ada lovelace"
    # title()方法以首字母的方式显示每一个单词
    print(name.title())
    #输出:Ada Lovelace
    ```
  - 合并（拼接）字符串：使用 ‘+‘ 号

    ```python
        first_name = "ada"
        last_name = "lovelace"
        full_name = first_name + " " + last_name
        print("Hello, " + full_name.title() + "!")
        # 输出 Hello, Ada Lovelace !
    ```

  - 使用制表符或换行符来添加空白
  
    - 制表符\t
    - 换行符\n
  
  - 删除空白
  
    - 删除末尾空白：rstrip()
    - 删除开头空白：lstrip()
    - 删除两头空白：strip()
    
    ```python
    message = " python "
    #输出: ! python!
    print("!"+message.rstrip()+"!")
    #输出：!python !
    print("!"+message.lstrip()+"!")
    #输出：!python!
    print("!"+message.strip()+"!")
    ```
  
  - 使用字符串时避免语法错误 
  
    - 在用单引号括起的字符串中， 如果包含撇号， 就将导致错误。 这是因为这会
      导致Python将第一个单引号和撇号之间的内容视为一个字符串， 进而将余下的文本视为Python代码， 从而引发错误。 

###数字

- 整数可进行 加(+) 减(-) 乘(*) 除(/) 乘方(**)

- 浮点数：2.0,1.2

- 使用函数str()避免类型错误

  ```python
  age = 23
  message = "Happy " + age + "rd Birthday"
  print(message)
  """
  Traceback (most recent call last):
  File "birthday.py", line 2, in <module>
  message = "Happy " + age + "rd Birthday!"
  TypeError: Can't convert 'int' object to str implicitly
  """
  # 类型出错
  # 第二句的正确写法: message = "Happy " + str(age) + "rd Birthday"
  ```


###注释

- 单行注释 

- 多行注释 

   ```python
    # 我是单行注释
    """
    我是多行注释
    """
   ```

## 列表简介

### 列表是什么

- **列表** 由一系列按特定顺序排列的元素组成 

    ```python
      bicycles = ['trek', 'cannondale', 'redline', 'specialized']
      print(bicycles)
      #输出:['trek', 'cannondale', 'redline', 'specialized']
    ```

    ```python
    # 列表中的元素的使用
    bicycles = ['trek', 'cannodale', 'redline', 'specialized']
    print(bicycles[0]) # 列表的索引从0开始，输出trek
    print(bicycles[-1]) # 表示列表的最后一个元素,输出specialized
    ```

### 修改添加和删除元素

- 修改列表元素

- 要修改列表元素， 可指定列表名和要修改的元素的索引， 再指定该元素的新值 

     ```python
     motorcycles = ['honda', 'yamaha', 'suzuki']
     # 输出：['honda', 'yamaha', 'suzuki']
     print(motorcycles)
     # 通过列表的索引修改该索引的值
     motorcycles[0] = 'ducati'
     # 输出：['ducati', 'yamaha', 'suzuki']
     print(motorycycles)
     ```

-   列表中添加元素

-  在列表末尾添加元素

    ```python
      motorcycle = ['honda', 'yamaha', 'suzuki']
      # 输出：['honda', 'yamaha', 'suzuki']
      print(motorcycles)
      # 在列表的末尾添加元素
      motorcycles.append('ducati')
      # ['honda', 'yamaha', 'suzuki', 'ducati']
      print(motorcycles)
    ```

- 在列表中插入元素

    ```python
    motorcycles = ['honda', 'yamaha', 'suzuki']
    # 在0号索引插入一个元素
    motorcycles.insert(0,'ducati')
    # 输出：['ducati', 'honda', 'yamaha', 'suzuki']
    print(motorcycles)
    ```

- 从列表中删除元素

  - 使用del语句删除元素

      ```python
      motorcycles = ['honda', 'yamaha', 'suzuki']
      # 输出:['honda', 'yamaha', 'suzuki']
      print(motorcycles)
      # 按照索引删除对应元素
      del motorcycles[0]
      # 输出：['yamaha', 'suzuki']
      print(motorcycles)
      ```

  - 使用方法pop()删除元素
  
    ```python
    montorcycles = ['handa', 'yamaha', 'suzuki']
    # 输出:['handa', 'yamaha', 'suzuki']
    print(motorcycles)
    # 类似出栈删除最后一个元素
    poped_motorcycle = motorcycles.pop()
    # 输出:['handa', 'yamaha']
    print(motorcycles)
    # 输出：suzuki
    print(poped_motorcycle)
    ```
  
  - 弹出列表中任何位置处的元素 
  
    ```python
    motorcycles = ['honda', 'yamaha', 'suzuki']
    # 按给定的索引删除元素，索引从零开始
    first_owned = motorcycles.pop(0)
    # The first motorcycle I owned was a Honda.
    print("The first motorcycle I owned was a " + first_owned.title() + ".")
    ```
  
- 使用原则

    - 如果你要从列表中删除一个元素， 且不再以任何方式使用它， 就使用del 语句；
    -  如果你要在删除元素后还能继续使用它， 就使用方法pop() 。

- 根据值删除元素

    ```python
    motorcycles = ['honda', 'yamaha', 'suzuki', 'ducati']
    # 输出: ['honda', 'yamaha', 'suzuki', 'ducati']
    print(motorcycles)
    # 删除指定的元素，若没有，则直接返回，若含有多个，只删除第一个
    motorcycles.remove('ducati')
    # 输出: ['honda', 'yamaha', 'suzuki']
    print(motorcycles)
    ```

### 组织列表

- 使用方法sort()对列表进行**永久性**排序

  ```python
  cars = ['bmw', 'audi', 'toyota', 'subaru']
  # 对列表按字母的顺序排序，而且是永久性的排序
  cars.sort()
  # 输出:['audi', 'bmw', 'subaru', 'toyota']
  print(carts)
  # reverse 反转 按照sort()的逆序输出
  cars.sort(reverse=True)
  # 输出：['toyota', 'subaru', 'bmw', 'audi']
  print(cars)
  ```

- 使用函数sorted() 对列表进行临时排序 

  ```python
  cars = ['bmw', 'audi', 'toyota', 'subaru']
  # 输出：Here is the original list:
  # ['bmw', 'audi', 'toyota', 'subaru']
  print("Here is the original list:")
  print(cars)
  # Here is the sorted list:
  # ['audi', 'bmw', 'subaru', 'toyota']
  print("\nHere is the sorted list:")
  # 排序是临时的
  print(sorted(cars))
  # 输出：Here is the original list again:
  # ['bmw', 'audi', 'toyota', 'subaru']
  print("\nHere is the original list again:")
  print(cars)
  ```

- 倒着打印列表

  ```python
  cars = ['bmw', 'audi', 'toyota', 'subaru']
  # 输出 ：['bmw', 'audi', 'toyota', 'subaru']
  print(cars)
  # 按照 字母的逆序修改列表的排列顺序（永久性）
  cars.reverse()
  # 输出:['subaru', 'toyota', 'audi', 'bmw']
  print(cars)
  ```

- 确定列表的长度

  ```python
  cars = ['bmw', 'audi', 'toyota', 'subaru']
  # 输出：4
  print(len(cars))
  ```


### 使用列表避免索引错误

- 不要试图获取不存在的索引的元素

- 每当需要访问最后一个列表元素时， 都可使用索引-1 ，但是当列表的长度为0的时候，就会报错

  ```python
  Traceback (most recent call last):
  File "motorcyles.py", line 3, in <module>
  print(motorcycles[-1])
  # 索引出错
  IndexError: list index out of range
  ```

## 操作列表

### 遍历整个列表

- 使用for循环遍历整个列表

  ```python
  magicians = ['alice', 'david', 'carolina']
  # 遍历列表 for item in item_list:
  for magician in magicians:
  	print(magician.title() + ", that was a great trick!")
  	print("I can't wait to see your next trick, " + magician.title() + ".\n")
  # for循环结束后执行的操作
  print("Thank you, everyone. That was a great magic show!")
  # 输出的结果:
  # Alice, that was a great trick!
  # I can't wait to see your next trick, Alice.
  # David, that was a great trick!
  # I can't wait to see your next trick, David.
  # Carolina, that was a great trick!
  # I can't wait to see your next trick, Carolina.
  # Thank you, everyone. That was a great magic show!
  ```

### 避免缩进错误

```python
File "hello_world.py", line 2
print(message)
^
IndentationError: unexpected indent
# 缩进出错
```

### 创建数值列表

- 使用函数range()

  ```python
  for value in range(1,5):
  	print(value)
  # 注意这里的5不会打印，包头不包尾
  # 输出:
  # 1
  # 2
  # 3
  # 4
  ```

- 使用range() 创建数字列表 

  ```python
  numbers = list(range(1,6))
  # 输出: [1, 2, 3, 4, 5]
  print(numbers)
  # range()方法的
  # 第一个参数表示从哪个数开始，包括在内
  # 第二个参数表示到哪个数结束，不包括在内
  # 第三个参数表示步长
  even_numbers = list(range(2,11,2))
  # 输出:[2, 4, 6, 8, 10]
  print(even_numbers)
  
  ```

- 对数字列表执行简单的统计计算 

  ```python
  digits = [1, 2, 3, 4, 5, 6, 7, 8, 9, 0]
  # 打印这个列表中最小值
  # 输出:0
  print(min(digits))
  # 打印这个列表中最大值
  # 输出:9
  print(max(digits))
  # 打印这个列表的和
  # 输出:45
  print(sum(digits))
  ```

- 列表解析(列表推导式)

  ```python
  squares = [value**2 for value in range(1,11)]
  # 输出: [1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
  print(squares)
  ```


### 使用列表的一部分

- 切片

  ```python
  players = ['charles', 'martina', 'michael', 'florence', 'eli']
  # 输出:['charles', 'martina', 'michael']
  # 从0-2的索引打印出来，包头不包尾
  print(players[0:3])
  # 输出：['charles', 'martina', 'michael', 'florence']
  # 没有指定起始索引，就从头开始
  print(players[:4])
  # 输出:['michael', 'florence', 'eli']
  # 没有指定结束索引，就直接到结尾
  print(players[2:])
  # 输出:['michael', 'florence', 'eli']
  # 负数索引返回离列表末尾相应距离的元素
  print(players[-3:])
  ```

- 遍历切片

  ```python
  players = ['charles', 'martina', 'michael', 'florence', 'eli']
  print("Here are the first three players on my team:")
  for player in players[:3]:
  	print(player.title())
  # 输出：
  # Here are the first three players on my team:
  # Charles
  # Martina
  # Michael
  ```

- 复制列表

  ```python
  my_foods = ['pizza', 'falafel', 'carrot cake']
  # 通过切边来进行复制
  friend_foods = my_foods[:]
  # 两个列表互不影响
  my_foods.append('cannoli')
  friend_foods.append('ice cream')
  print("My favorite foods are:")
  print(my_foods)
  print("\nMy friend's favorite foods are:")
  print(friend_foods)
  # 输出:
  # My favorite foods are:
  # ['pizza', 'falafel', 'carrot cake', 'cannoli']
  # My friend's favorite foods are:
  # ['pizza', 'falafel', 'carrot cake', 'ice cream']
  ```


### 元组

- 定义元组

  ```python
  # Python将不能修改的值称为不可变的 ， 而不可变的列表被称为元组 。
  dimensions = (200, 50)
  # 输出:200
  print(dimensions[0])
  # 输出：50
  print(dimensions[1])
  ```

- 修改元组中的值

  ```python
  # 由于试图修改元组的操作是被禁止的， 因此Python指出不能给元组的元素赋值
  Traceback (most recent call last):
  File "dimensions.py", line 3, in <module>
  dimensions[0] = 250
  TypeError: 'tuple' object does not support item assignment
  ```

- 遍历元组中的所有值

  ```python
  dimensions = (200, 50)
  for dimension in dimensions:
  	print(dimension)
  # 输出:
  # 200
  # 50
  ```

- 修改元组变量 

  ```python
  dimensions = (200, 50)
  print("Original dimensions:")
  for dimension in dimensions:
  	print(dimension)
  dimensions = (400, 100)
  print("\nModified dimensions:")
  for dimension in dimensions:
  	print(dimension)
  # 输出:
  # Original dimensions:
  # 200
  # 50
  # Modified dimensions:
  # 400
  # 100
  ```


## if 语句

### 一个简单示例

```python
cars = ['audi', 'bmw', 'subaru', 'toyota']
# 遍历列表
for car in cars:
    # 当子元素是'bmw' 输出大写的
 	if car == 'bmw':
		print(car.upper())
    # 当子元素不是'bmw' 输出首字母大写的
	else:
		print(car.title())
# 输出：
# Audi
# BMW
# Subaru
# Toyota
```

### 条件测试

- 检查是否相等

  ```python
  car = 'bmw'
  # 输出： True
  print(car == 'bmw')
  ```

- 检查是否相等时不考虑大小写

  ```python
  car = 'Audi'
  # 输出: True
  # 将car的字符串全部转成小写
  print(car.lower() == 'audi')
  ```

- 检查是否不相等 

  ```python
  requested_topping = 'mushrooms'
  if requested_topping != 'anchovies':
  	print("Hold the anchovies!")
  # 输出:Hold the anchovies!
  ```

- 比较数字 

  ```python
  age = 19
  # 输出：True
  print(age < 21)
  # 输出：True
  print(age <= 21)
  # 输出：False
  print(age > 21)
  # 输出：False
  print(age >= 21)
  ```

- 检查多个条件

  ```python
  age_0 = 22
  age_1 = 18
  # 输出：False
  # and 当两个条件都满足的时候才能返回true
  print（age_0 >= 21 and age_1 >= 21）
  age_1 = 22
  # 输出：True
  print(age_0 >= 21 and age_1 >= 21)
  ```

  ```python
  age_0 = 22
  age_1 = 18
  # 输出：True
  # or 当两个条件有一个满足的时候，就返回True
  print(age_0 >= 21 or age_1 >= 21)
  age_0 = 18
  # 输出: False
  print(age_0 >= 21 or age_1 >= 21)
  ```

- 检查特定值是否包含在列表中 

  ```python
  requested_toppings = ['mushrooms', 'onions', 'pineapple']
  # 输出: True
  # in 当指定的元素在列表中就返回True
  print('mushrooms' in requested_toppings）
  # 输出：False
  print('pepperoni' in requested_toppings)
  ```

- 检查特定值是否不包含在列表中 

  ```python
  banned_users = ['andrew', 'carolina', 'david']
  user = 'marie'
  # not in 当指定的元素不在列表中返回true
  if user not in banned_users:
  	print(user.title() + ", you can post a response if you wish.")
  # 输出:Marie, you can post a response if you wish.
  ```


### if语句

- 简单的if 语句 

  ```python
  age = 19
  # 当条件满足的情况下，输出下面的语句
  if age >= 18:
  	print("You are old enough to vote!")
  # 输出：You are old enough to vote!
  ```

- if-else 语句 

  ```python
  age = 17
  # 当条件满足的时候，执行下面两句打印语句
  if age >= 18:
  	print("You are old enough to vote!")
  	print("Have you registered to vote yet?")
  # 当条件不满足的时候，执行下面的两句打印语句
  else:
      print("Sorry, you are too young to vote.")
      print("Please register to vote as soon as you turn 18!")
  # 输出：
  # Sorry, you are too young to vote.
  # Please register to vote as soon as you turn 18!
  ```

- if-elif-else 结构 ：只有一个条件满足的情况下

  ```python
  age = 12
  # 当条件满足其中的一个，就执行下面的打印语句
  if age < 4:
  	print("Your admission cost is $0.")
  elif age < 18:
  	print("Your admission cost is $5.")
  # 当所有的条件都不满足的时候，执行下面的打印语句
  else:
  	print("Your admission cost is $10.")
  # 输出：Your admission cost is $5. 
  ```

- 使用多个elif 代码块 

  ```python
  age = 12
  if age < 4:
  	price = 0
  elif age < 18:
  	price = 5
  elif age < 65:
  	price = 10
  else:
  	price = 5
  print("Your admission cost is $" + str(price) + ".")
  # 输出 ：Your admission cost is $ 5.
  ```

- 省略else 代码块 

  ```python
  age = 12
  if age < 4:
  	price = 0
  elif age < 18:
  	price = 5
  elif age < 65:
  	price = 10
  elif age >= 65:
  	price = 5
  print("Your admission cost is $" + str(price) + ".")
  # 输出 ：Your admission cost is $ 5.
  ```


### 使用 if 语句处理列表

- 检查特殊元素

  ```python
  requested_toppings = ['mushrooms', 'green peppers', 'extra cheese']
  # 遍历列表
  for requested_topping in requested_toppings:
  	# 当列表中元素满足这个条件的时候，打印下面的语句
      if requested_topping == 'green peppers':
  		print("Sorry, we are out of green peppers right now.")
      # 当列表中元素不满足上面的条件的啥时候，打印下面的语句
  	else:
  		print("Adding " + requested_topping + ".")
  print("\nFinished making your pizza!")
  # 输出：
  # Adding mushrooms.
  # Sorry, we are out of green peppers right now.
  # Adding extra cheese.
  # Finished making your pizza!
  ```

- 确定列表不是空的 

  ```python
  requested_toppings = []
  # 当列表不为空的时候，执行下面的遍历
  if requested_toppings:
  	for requested_topping in requested_toppings:
  		print("Adding " + requested_topping + ".")
  	print("\nFinished making your pizza!")
  # 当列表为空的时候，执行下面的打印语句
  else:
  	print("Are you sure you want a plain pizza?")
  # 输出：Are you sure you want a plain pizza?
  ```


## 字典

### 一个简单的字典

- 字典 是一系列键—值对 。 

    ```python
    alien_0 = {'color': 'green', 'points': 5}
    print(alien_0['color'])
    print(alien_0['points'])
    # 输出：
    # green
    # 5
    ```

### 使用字典

- 访问字典中的值

  ```python
  alien_0 = {'color': 'green'}
  # 打印出字典中键为'color'的值
  # 输出：green
  print(alien_0['color'])
  ```

- 添加键—值对 

  ```python
  alien_0 = {'color': 'green', 'points': 5}
  # 输出：{'color': 'green', 'points': 5}
  print(alien_0)
  # 给指定字典添加一个键为'x_position'的值为0
  alien_0['x_position'] = 0
  # 给指定字典添加一个键为'y_position'的值为25
  alien_0['y_position'] = 25
  # 输出：{'color': 'green', 'points': 5, 'y_position': 25, 'x_position': 0}
  print(alien_0)
  ```

- 先创建一个空字典 

  ```python
  # 创建一个空的字典
  alien_0 = {}
  # 为字典添加两个键值对
  alien_0['color'] = 'green'
  alien_0['points'] = 5
  # 输出：{'color': 'green', 'points': 5}
  print(alien_0)
  ```

- 修改字典中的值 

  ```python
  alien_0 = {'color': 'green'}
  # 输出：The alien is green.
  print("The alien is " + alien_0['color'] + ".")
  # 给指定的字典中指定的键赋值新的内容
  alien_0['color'] = 'yellow'
  # 输出：The alien is now yellow.
  print("The alien is now " + alien_0['color'] + ".")
  ```

- 删除键—值对 

  ```python
  alien_0 = {'color': 'green', 'points': 5}
  # 输出：{'color': 'green', 'points': 5}
  print(alien_0)
  # 删除指定的字典的指定的键
  del alien_0['points']
  # 输出：{'color': 'green'}
  print(alien_0)
  ```

- 由类似对象组成的字典 

  ```python
  favorite_languages = {
  	'jen': 'python',
  	'sarah': 'c',
  	'edward': 'ruby',
  	'phil': 'python',
  }
  # 输出：Sarah's favorite language is C.
  print("Sarah's favorite language is " +
  favorite_languages['sarah'].title() +
  ".")
  ```


### 遍历字典

- 遍历所有的键—值对 

  ```python
  user_0 = {
  	'username': 'efermi',
  	'first': 'enrico',
  	'last': 'fermi',
  }
  # 字典的items方法，相当于取出字典每一项
  for key, value in user_0.items():
  	print("\nKey: " + key)
  	print("Value: " + value)
  # 输出：
  # Key: last
  # Value: fermi
  #
  # Key: first
  # Value: enrico
  #
  # Key: username
  # Value: efermi
  ```

- 遍历字典中的所有键 

  ```python
  favorite_languages = {
  	'jen': 'python',
  	'sarah': 'c',
  	'edward': 'ruby',
  	'phil': 'python',
  }
  # 字典的keys方法相当于获取字典中键
  for name in favorite_languages.keys():
  	print(name.title())
  # 输出：
  # Jen
  # Sarah
  # Phil
  # Edward
  ```

- 按顺序遍历字典中的所有键 

  ```python
  favorite_languages = {
  	'jen': 'python',
  	'sarah': 'c',
  	'edward': 'ruby',
  	'phil': 'python',
  }
  # 获取到字典中的所有的键然后排序，再遍历
  for name in sorted(favorite_languages.keys()):
  	print(name.title() + ", thank you for taking the poll.")
  # 输出：
  # Edward, thank you for taking the poll.
  # Jen, thank you for taking the poll.
  # Phil, thank you for taking the poll.
  # Sarah, thank you for taking the poll.
  ```

- 遍历字典中的所有值 

  ```python
  favorite_languages = {
  	'jen': 'python',
  	'sarah': 'c',
  	'edward': 'ruby',
  	'phil': 'python',
  }
  print("The following languages have been mentioned:")
  # 先通过调用字典的values()方法获取所有的值，然后在遍历
  for language in favorite_languages.values():
  	print(language.title())
  # 输出：
  # The following languages have been mentioned:
  # Python
  # C
  # Python
  # Ruby
  ```

- 去重调用set()方法

  ```python
  favorite_languages = {
  	'jen': 'python',
  	'sarah': 'c',
  	'edward': 'ruby',
  	'phil': 'python',
  }
  print("The following languages have been mentioned:")
  # 先从字典取出所有的值，然后在用set()方法去重，最后在遍历
  for language in set(favorite_languages.values()):
  	print(language.title())
  # 输出：
  # The following languages have been mentioned:
  # Python
  # C
  # Ruby
  ```


### 嵌套

- 字典列表

  ```python
  # 创建一个用于存储外星人的空列表
  aliens = []
  # 创建30个绿色的外星人
  for alien_number in range (0,30):
  	new_alien = {'color': 'green', 'points': 5, 'speed': 'slow'}
  	aliens.append(new_alien)
  for alien in aliens[0:3]:
  	if alien['color'] == 'green':
  		alien['color'] = 'yellow'
  		alien['speed'] = 'medium'
  		alien['points'] = 10
  # 显示前五个外星人
  for alien in aliens[0:5]:
  	print(alien)
  print("...")
  # 输出：
  # {'speed': 'medium', 'color': 'yellow', 'points': 10}
  # {'speed': 'medium', 'color': 'yellow', 'points': 10}
  # {'speed': 'medium', 'color': 'yellow', 'points': 10}
  # {'speed': 'slow', 'color': 'green', 'points': 5}
  # {'speed': 'slow', 'color': 'green', 'points': 5}
  # ...
  ```

- 在字典中存储列表 

  ```python
  # 存储所点比萨的信息
  pizza = {
  	'crust': 'thick',
  	'toppings': ['mushrooms', 'extra cheese'],
  }
  # 概述所点的比萨
  print("You ordered a " + pizza['crust'] + "-crust pizza " +
  "with the following toppings:")
  for topping in pizza['toppings']:
  	print("\t" + topping)
  # 输出：
  # You ordered a thick-crust pizza with the following toppings:
  # 	mushrooms
  # 	extra cheese
  ```

- 在字典中存储字典 

  ```python
  users = {
  	'aeinstein': {
  		'first': 'albert',
  		'last': 'einstein',
  		'location': 'princeton',
  		},
  	'mcurie': {
  		'first': 'marie',
  		'last': 'curie',
  		'location': 'paris',
  		},
  }
  for username, user_info in users.items():
  	print("\nUsername: " + username)
  	full_name = user_info['first'] + " " + user_info['last']
  	location = user_info['location']
  	print("\tFull name: " + full_name.title())
  	print("\tLocation: " + location.title())
  # 输出：
  # Username: aeinstein
  # 	Full name: Albert Einstein
  #	Location: Princeton
  # Username: mcurie
  #	Full name: Marie Curie
  #	Location: Paris
  ```

## 用户输入和while循环

### 函数input()的工作原理

- 编写清晰的程序

  ```python
  name = input("Please enter your name: ")
  print("Hello, " + name + "!")
  # 输出：
  # Please enter your name: Eric
  # Hello, Eric!
  ```

- 使用int() 来获取数值输入 

  ```python
  height = input("How tall are you, in inches? ")
  # 使用int()方法将输入的字符类型转成int类型
  height = int(height)
  if height >= 36:
  	print("\nYou're tall enough to ride!")
  else:
  	print("\nYou'll be able to ride when you're a little older.")
  # 输出：
  # How tall are you, in inches? 71
  # 
  # You're tall enough to ride!
  ```

- 求模运算符 

  ```python
  number = input("Enter a number, and I'll tell you if it's even or odd: ")
  number = int(number)
  # % 取模运算符，当结果等于0的时候就是偶数，否则是奇数
  if number % 2 == 0:
  	print("\nThe number " + str(number) + " is even.")
  else:
  	print("\nThe number " + str(number) + " is odd.")
  # 输出：
  # Enter a number, and I'll tell you if it's even or odd: 42
  # 
  # The number 42 is even.
  ```


### while循环简介

- 使用while 循环 

  ```python
  current_number = 1
  while current_number <= 5:
  	print(current_number)
  	current_number += 1
  # 输出：
  # 1
  # 2
  # 3
  # 4
  # 5
  ```

- 让用户选择何时退出 

  ```python
  prompt = "\nTell me something, and I will repeat it back to you:"
  prompt += "\nEnter 'quit' to end the program. "
  message = ""
  # 当用户输入'quit'程序退出
  while message != 'quit':
  	message = input(prompt)
  	if message != 'quit':
  		print(message)
  # 输出：
  # Tell me something, and I will repeat it back to you:
  # Enter 'quit' to end the program. Hello everyone!
  # Hello everyone!
  # 
  # Tell me something, and I will repeat it back to you:
  # Enter 'quit' to end the program. Hello again.
  # Hello again.
  # 
  # Tell me something, and I will repeat it back to you:
  # Enter 'quit' to end the program. quit
  ```

- 使用标志 

  ```python
  prompt = "\nTell me something, and I will repeat it back to you:"
  prompt += "\nEnter 'quit' to end the program. "
  active = True
  # 使用了标志
  while active:
  	message = input(prompt)
  	# 当输入的'quit'把标志位置为False，跳出循环
      if message == 'quit':
  		active = False
  	else:
  		print(message)
  # 输出：
  # Tell me something, and I will repeat it back to you:
  # Enter 'quit' to end the program. Hello everyone!
  # Hello everyone!
  # 
  # Tell me something, and I will repeat it back to you:
  # Enter 'quit' to end the program. Hello again.
  # Hello again.
  # 
  # Tell me something, and I will repeat it back to you:
  # Enter 'quit' to end the program. quit
  ```

- 使用break 退出循环 

  ```python
  prompt = "\nPlease enter the name of a city you have visited:"
  prompt += "\n(Enter 'quit' when you are finished.) "
  while True:
  	city = input(prompt)
  	if city == 'quit':
           # break结束当前循环
  		break
  	else:
  		print("I'd love to go to " + city.title() + "!")
  # 输出：
  # Please enter the name of a city you have visited:
  # (Enter 'quit' when you are finished.) New York
  # I'd love to go to New York!
  # 
  # Please enter the name of a city you have visited:
  # (Enter 'quit' when you are finished.) San Francisco
  # I'd love to go to San Francisco!
  # 
  # Please enter the name of a city you have visited:
  # (Enter 'quit' when you are finished.) quit
  ```

- 在循环中使用continue 

  ```python
  current_number = 0
  while current_number < 10:
  	current_number += 1
  	if current_number % 2 == 0:
          # continue跳过这次循环
  		continue
  	print(current_number)
  # 输出：
  # 1
  # 3
  # 5
  # 7
  # 9
  ```


### 使用while循环来处理列表

- 在列表之间移动元素 

  ```python
  # 首先， 创建一个待验证用户列表
  # 和一个用于存储已验证用户的空列表
  unconfirmed_users = ['alice', 'brian', 'candace']
  confirmed_users = []
  # 验证每个用户， 直到没有未验证用户为止
  # 将每个经过验证的列表都移到已验证用户列表中
  # 当列表不为空的时候，这个循环会一直进入
  while unconfirmed_users:
  	current_user = unconfirmed_users.pop()
  	print("Verifying user: " + current_user.title())
  	confirmed_users.append(current_user)
  # 显示所有已验证的用户
  print("\nThe following users have been confirmed:")
  for confirmed_user in confirmed_users:
  	print(confirmed_user.title())
  # 输出：
  # Verifying user: Candace
  # Verifying user: Brian
  # Verifying user: Alice
  # 
  # The following users have been confirmed:
  # Candace
  # Brian
  # Alice
  ```

- 删除包含特定值的所有列表元素 

  ```python
  pets = ['dog', 'cat', 'dog', 'goldfish', 'cat', 'rabbit', 'cat']
  print(pets)
  # 当指定的元素在循环中，我们就删除该元素
  while 'cat' in pets:
  	pets.remove('cat')
  print(pets)
  # 输出：
  # ['dog', 'cat', 'dog', 'goldfish', 'cat', 'rabbit', 'cat']
  # ['dog', 'dog', 'goldfish', 'rabbit']
  ```

- 使用用户输入来填充字典 

  ```python
  responses = {}
  # 设置一个标志， 指出调查是否继续
  polling_active = True
  while polling_active:
  	# 提示输入被调查者的名字和回答
  	name = input("\nWhat is your name? ")
  	response = input("Which mountain would you like to climb someday? ")
  	# 将答卷存储在字典中
  	responses[name] = response
  	# 看看是否还有人要参与调查
  	repeat = input("Would you like to let another person respond? (yes/ no) ")
      # 当用户输入'no'的时候，结束循环
  	if repeat == 'no':
  		polling_active = False
  # 调查结束， 显示结果
  print("\n--- Poll Results ---")
  for name, response in responses.items():
  	print(name + " would like to climb " + response + ".")
  # 输出：
  # What is your name? Eric
  # Which mountain would you like to climb someday? Denali
  # Would you like to let another person respond? (yes/ no) yes
  # 
  # What is your name? Lynn
  # Which mountain would you like to climb someday? Devil's Thumb
  # Would you like to let another person respond? (yes/ no) no
  # 
  # --- Poll Results ---
  # Lynn would like to climb Devil's Thumb.
  # Eric would like to climb Denali.
  ```

## 函数

### 定义函数

- 向函数传递信息

  ```python
  def greet_user(username):
  """显示简单的问候语"""
  	print("Hello, " + username.title() + "!")
  greet_user('jesse')
  # 输出：Hello, Jesse!
  ```


### 传递实参

- 位置实参 

  ```python
  def describe_pet(animal_type, pet_name):
  	"""显示宠物的信息"""
  	print("\nI have a " + animal_type + ".")
  	print("My " + animal_type + "'s name is " + pet_name.title() + ".")
  describe_pet('hamster', 'harry')
  # 输出：
  # I have a hamster.
  # My hamster's name is Harry.
  ```

- 调用函数多次 

  ```python
  def describe_pet(animal_type, pet_name):
  	"""显示宠物的信息"""
  	print("\nI have a " + animal_type + ".")
  	print("My " + animal_type + "'s name is " + pet_name.title() + ".")
  describe_pet('hamster', 'harry')
  describe_pet('dog', 'willie')
  # 输出：
  # I have a hamster.
  # My hamster's name is Harry.
  # 
  # I have a dog.
  # My dog's name is Willie.
  ```

- 关键字实参

  ```python
  def describe_pet(animal_type, pet_name):
  	"""显示宠物的信息"""
  	print("\nI have a " + animal_type + ".")
  	print("My " + animal_type + "'s name is " + pet_name.title() + ".")
  # 下面两个函数的调用的结果是一样的，通过参数名来指定参数名的值
  describe_pet(animal_type='hamster', pet_name='harry')
  describe_pet(pet_name='harry', animal_type='hamster')
  ```

- 默认值 

  ```python
  # 给定参数指定默认的值
  def describe_pet(pet_name, animal_type='dog'):
  	"""显示宠物的信息"""
  	print("\nI have a " + animal_type + ".")
  	print("My " + animal_type + "'s name is " + pet_name.title() + ".")
  describe_pet(pet_name='willie')
  # 当改变默认值的时候，用下面的方式调用函数
  describe_pet('harry', 'hamster')
  describe_pet(pet_name='harry', animal_type='hamster')
  describe_pet(animal_type='hamster', pet_name='harry')
  # 输出：
  # I have a dog.
  # My dog's name is Willie.
  # I have a hamster.
  # My hamster's name is Harry.
  # I have a hamster.
  # My hamster's name is Harry.
  # I have a hamster.
  # My hamster's name is Harry.
  ```

- 避免实参错误 

  ```python
  def describe_pet(animal_type, pet_name):
  	"""显示宠物的信息"""
  	print("\nI have a " + animal_type + ".")
  	print("My " + animal_type + "'s name is " + pet_name.title() + ".")
  describe_pet()
  # 报错：缺少必要的参数
  # Traceback (most recent call last):
  # File "pets.py", line 6, in <module>
  # describe_pet()
  # TypeError: describe_pet() missing 2 required positional arguments: 'animal_
  # type' and 'pet_name'
  ```


### 返回值

- 返回简单值 

  ```python
  def get_formatted_name(first_name, last_name):
  	"""返回整洁的姓名"""
  	full_name = first_name + ' ' + last_name
  	return full_name.title()
  musician = get_formatted_name('jimi', 'hendrix')
  print(musician)
  # 输出：Jimi Hendrix
  ```

- 让实参变成可选的 

  ```python
  # 根据输入的中间的名字，当中间的名字为空的时候，直接输入两个名字，当不为空的时候，输出三个名字
  def get_formatted_name(first_name, last_name, middle_name=''):
  	"""返回整洁的姓名"""
  	if middle_name:
  		full_name = first_name + ' ' + middle_name + ' ' + last_name
  	else:
  		full_name = first_name + ' ' + last_name
  	return full_name.title()
  musician = get_formatted_name('jimi', 'hendrix')
  # 输出：Jimi Hendrix
  print(musician)
  # 输出：John Lee Hooker
  musician = get_formatted_name('john', 'hooker', 'lee')
  print(musician)
  ```

- 返回字典 

  ```python
  def build_person(first_name, last_name, age=''):
  	"""返回一个字典， 其中包含有关一个人的信息"""
  	person = {'first': first_name, 'last': last_name}
  	if age:
  		person['age'] = age
  	return person
  musician = build_person('jimi', 'hendrix', age=27)
  print(musician)
  # 输出：{'first': 'jimi', 'last': 'hendrix', 'age': 27}
  ```

- 结合使用函数和while 循环 

  ```python
  def get_formatted_name(first_name, last_name):
  	"""返回整洁的姓名"""
  	full_name = first_name + ' ' + last_name
  	return full_name.title()
  while True:
  	print("\nPlease tell me your name:")
  	print("(enter 'q' at any time to quit)")
  	f_name = input("First name: ")
  	if f_name == 'q':
  		break
  	l_name = input("Last name: ")
  	if l_name == 'q':
  		break
  	formatted_name = get_formatted_name(f_name, l_name)
  	print("\nHello, " + formatted_name + "!")
  # 输出：
  # Please tell me your name:
  # (enter 'q' at any time to quit)
  # First name: eric
  # Last name: matthes
  # 
  # Hello, Eric Matthes!
  # 
  # Please tell me your name:
  # (enter 'q' at any time to quit)
  # First name: q
  ```


### 传递列表

- 在函数中修改列表 

  ```python
  def print_models(unprinted_designs, completed_models):
      """
      模拟打印每个设计， 直到没有未打印的设计为止
      打印每个设计后， 都将其移到列表completed_models中
      """
  	while unprinted_designs:
  		current_design = unprinted_designs.pop()
  		# 模拟根据设计制作3D打印模型的过程
  		print("Printing model: " + current_design)
  		completed_models.append(current_design)
  def show_completed_models(completed_models):
  	"""显示打印好的所有模型"""
  	print("\nThe following models have been printed:")
  	for completed_model in completed_models:
  		print(completed_model)
  unprinted_designs = ['iphone case', 'robot pendant', 'dodecahedron']
  completed_models = []
  print_models(unprinted_designs, completed_models)
  show_completed_models(completed_models)
  # 输出：
  # Printing model: dodecahedron
  # Printing model: robot pendant
  # Printing model: iphone caseThe 
  # following models have been printed:
  # dodecahedron
  # robot pendant
  # iphone case
  ```

- 禁止函数修改列表 

  ```python
  # 调用的时候，将切片传入进去。就是复制的切片
  function_name(list_name[:])
  ```


### 传递任意数量的实参

- 结合使用位置实参和任意数量实参 

  ```python
  # *参数，表示可以接受任意数量的参数
  def make_pizza(size, *toppings):
  	"""概述要制作的比萨"""
  	print("\nMaking a " + str(size) +
  			"-inch pizza with the following toppings:")
  	for topping in toppings:
  		print("- " + topping)
  make_pizza(16, 'pepperoni')
  make_pizza(12, 'mushrooms', 'green peppers', 'extra cheese')
  # 输出：
  # Making a 16-inch pizza with the following toppings:
  # - pepperoni
  # Making a 12-inch pizza with the following toppings:
  # - mushrooms
  # - green peppers
  # - extra cheese
  ```

- 使用任意数量的关键字实参 

  ```python
  # **参数，表示接受任意数量的关键字实参
  def build_profile(first, last, **user_info):
  	"""创建一个字典， 其中包含我们知道的有关用户的一切"""
  	profile = {}
  	profile['first_name'] = first
  	profile['last_name'] = last
  	for key, value in user_info.items():
  		profile[key] = value
  	return profile
  user_profile = build_profile('albert', 'einstein',
  							location='princeton',
  							field='physics')
  print(user_profile)
  # 输出：
  # {'first_name': 'albert', 'last_name': 'einstein',
  # 'location': 'princeton', 'field': 'physics'}
  ```


### 将函数存储在模块中

- 导入整个模块 

  ```python
  def make_pizza(size, *toppings):
  	"""概述要制作的比萨"""
  	print("\nMaking a " + str(size) +
  		"-inch pizza with the following toppings:")
  	for topping in toppings:
  		print("- " + topping)
  ```

  ```python
  # 导入上面的模块，所以通过模块名.方法名调用
  import pizza
  pizza.make_pizza(16, 'pepperoni')
  pizza.make_pizza(12, 'mushrooms', 'green peppers', 'extra cheese')
  # 输出：
  # Making a 16-inch pizza with the following toppings:
  # - pepperoni
  # Making a 12-inch pizza with the following toppings:
  # - mushrooms
  # - green peppers
  # - extra cheese
  ```

- 导入特定的函数 

  ```python
  # 导入模块中的方法，直接调用该方法，不需要输入模块名
  from pizza import make_pizza
  make_pizza(16, 'pepperoni')
  make_pizza(12, 'mushrooms', 'green peppers', 'extra cheese')
  # 输出：
  # Making a 16-inch pizza with the following toppings:
  # - pepperoni
  # Making a 12-inch pizza with the following toppings:
  # - mushrooms
  # - green peppers
  # - extra cheese
  ```

- 使用as 给函数指定别名 

  ```python
  # as 可以取一个别名，直接通过别名调用该方法
  from pizza import make_pizza as mp
  mp(16, 'pepperoni')
  mp(12, 'mushrooms', 'green peppers', 'extra cheese')
  # 输出：
  # Making a 16-inch pizza with the following toppings:
  # - pepperoni
  # Making a 12-inch pizza with the following toppings:
  # - mushrooms
  # - green peppers
  # - extra cheese
  ```

- 使用as 给模块指定别名 

  ```python
  # 给模块取一个别名
  import pizza as p
  p.make_pizza(16, 'pepperoni')
  p.make_pizza(12, 'mushrooms', 'green peppers', 'extra cheese')
  # 输出：
  # Making a 16-inch pizza with the following toppings:
  # - pepperoni
  # Making a 12-inch pizza with the following toppings:
  # - mushrooms
  # - green peppers
  # - extra cheese
  ```

- 导入模块中的所有函数 

  ```python
  # 用*号导入所有的函数
  from pizza import *
  make_pizza(16, 'pepperoni')
  make_pizza(12, 'mushrooms', 'green peppers', 'extra cheese')
  # 输出：
  # Making a 16-inch pizza with the following toppings:
  # - pepperoni
  # Making a 12-inch pizza with the following toppings:
  # - mushrooms
  # - green peppers
  # - extra cheese
  ```


### 函数编写指南

- 应给函数指定描述性名称， 且只在其中使用小写字母和下划线。  
- 每个函数都应包含简要地阐述其功能的注释， 该注释应紧跟在函数定义后面， 并采用文档字符串格式。 
- 给形参指定默认值时， 等号两边不要有空格 

## 类

### 创建和使用类

- 创建Dog类

  ```python
  class Dog():
  	"""一次模拟小狗的简单尝试"""
      # 当创建Dog类的时候，python会自动调用__init__方法
      # self参数，它是一个指向实例本身的引用，让实例能够访问类中的属性和方法，类中自己书写的方法必须携带这个参数
      # self.name = name 实例变量name称为属性，在本类中任何地方都可以通过self.name访问到
  	def __init__(self, name, age):
  		"""初始化属性name和age"""
  		self.name = name
  		self.age = age
  	def sit(self):
  		"""模拟小狗被命令时蹲下"""
  		print(self.name.title() + " is now sitting.")
  	def roll_over(self):
  		"""模拟小狗被命令时打滚"""
  		print(self.name.title() + " rolled over!")
  ```

- 根据类创建实例 

  ```python
  my_dog = Dog('willie', 6)
  # 访问属性：my_dog.name,通过实例化的类名.属性名，就可以访问到类中的属性
  print("My dog's name is " + my_dog.name.title() + ".")
  print("My dog is " + str(my_dog.age) + " years old.")
  # 访问方法：my_dog.sit(),通过实例化的类名.方法名，就可以调用类中的方法
  my_dog.sit()
  my_dog.roll_over()
  # 创建多个实例，是互相不影响的
  your_dog = Dog('lucy', 3)
  print("\nYour dog's name is " + your_dog.name.title() + ".")
  print("Your dog is " + str(your_dog.age) + " years old.")
  your_dog.sit()
  your_dog.roll_over()
  # 输出：
  # My dog's name is Willie.
  # My dog is 6 years old.
  # Willie is now sitting.
  # Willie rolled over!
  # Your dog's name is Lucy.
  # Your dog is 3 years old.
  # Lucy is now sitting.
  # Lucy rolled over!
  ```

### 使用类和实例

- Car 类 

  ```python
    class Car():
    	"""一次模拟汽车的简单尝试"""
    	def __init__(self, make, model, year):
    		"""初始化描述汽车的属性"""
    		self.make = make
    		self.model = model
    		self.year = year
    	def get_descriptive_name(self):
    		"""返回整洁的描述性信息"""
    		long_name = str(self.year) + ' ' + self.make + ' ' + self.model
    		return long_name.title()
    my_new_car = Car('audi', 'a4', 2016)
    print(my_new_car.get_descriptive_name())
    # 输出：2016 Audi A4
  ```

- 给属性指定默认值 

  ```python
    class Car():
    	"""一次模拟汽车的简单尝试"""
    	def __init__(self, make, model, year):
    		"""初始化描述汽车的属性"""
    		self.make = make
    		self.model = model
    		self.year = year
            # 给定一个属性一个默认值
             self.odometer_reading = 0
    	def get_descriptive_name(self):
    		"""返回整洁的描述性信息"""
    		long_name = str(self.year) + ' ' + self.make + ' ' + self.model
    		return long_name.title()
        def read_odometer(self):
    		"""打印一条指出汽车里程的消息"""
    		print("This car has " + str(self.odometer_reading) + " miles on 		it.")
    my_new_car = Car('audi', 'a4', 2016)
    print(my_new_car.get_descriptive_name())
    my_new_car.read_odometer()
    # 输出：
    # 2016 Audi A4
    # This car has 0 miles on it.
  ```

- 修改属性的值 

  - 直接修改属性的值 

    ```python
      my_new_car = Car('audi', 'a4', 2016)
      print(my_new_car.get_descriptive_name())
      # 将该类中odometer_reading属性修改为23
      my_new_car.odometer_reading = 23
      my_new_car.read_odometer()
      # 输出：
      # 2016 Audi A4
      # This car has 23 miles on it.
    ```

  - 通过方法修改属性的值 

    ```python
      class Car():
      	"""一次模拟汽车的简单尝试"""
      	def __init__(self, make, model, year):
      		"""初始化描述汽车的属性"""
      		self.make = make
      		self.model = model
      		self.year = year
              # 给定一个属性一个默认值
               self.odometer_reading = 0
      	def get_descriptive_name(self):
      		"""返回整洁的描述性信息"""
      		long_name = str(self.year) + ' ' + self.make + ' ' + self.model
      		return long_name.title()
          def read_odometer(self):
      		"""打印一条指出汽车里程的消息"""
      		print("This car has " + str(self.odometer_reading) + " miles          onit.")
          def update_odometer(self, mileage):
      		"""
      		将里程表读数设置为指定的值
      		禁止将里程表读数往回调
      		"""
      		if mileage >= self.odometer_reading:
      			self.odometer_reading = mileage
      		else:
      			print("You can't roll back an odometer!")
              
      my_new_car = Car('audi', 'a4', 2016)
      print(my_new_car.get_descriptive_name())
      
      my_new_car.update_odometer(23)
      my_new_car.read_odometer()
      # 输出：
      # 2016 Audi A4
      # This car has 23 miles on it.
    ```

  - 通过方法对属性的值进行递增 

    ```python
      class Car():
      	"""一次模拟汽车的简单尝试"""
      	def __init__(self, make, model, year):
      		"""初始化描述汽车的属性"""
      		self.make = make
      		self.model = model
      		self.year = year
              # 给定一个属性一个默认值
               self.odometer_reading = 0
      	def get_descriptive_name(self):
      		"""返回整洁的描述性信息"""
      		long_name = str(self.year) + ' ' + self.make + ' ' + self.model
      		return long_name.title()
          def read_odometer(self):
      		"""打印一条指出汽车里程的消息"""
      		print("This car has " + str(self.odometer_reading) + " miles          onit.")
          def update_odometer(self, mileage):
      		"""
      		将里程表读数设置为指定的值
      		禁止将里程表读数往回调
      		"""
      		if mileage >= self.odometer_reading:
      			self.odometer_reading = mileage
      		else:
      			print("You can't roll back an odometer!")
         	def increment_odometer(self, miles):
      		"""将里程表读数增加指定的量"""
      		self.odometer_reading += miles
          
      my_used_car = Car('subaru', 'outback', 2013)
      print(my_used_car.get_descriptive_name())
      
      my_used_car.update_odometer(23500)
      my_used_car.read_odometer()
      
      my_used_car.increment_odometer(100)
      my_used_car.read_odometer()
      
      # 输出：
      # 2013 Subaru Outback
      # This car has 23500 miles on it.
      # This car has 23600 miles on it.
    ```

### 继承

- 子类的方法__init__() 

  ```python
  class Car():
  	"""一次模拟汽车的简单尝试"""
  	def __init__(self, make, model, year):
  		self.make = make
  		self.model = model
  		self.year = year
  		self.odometer_reading = 0
  	def get_descriptive_name(self):
  		long_name = str(self.year) + ' ' + self.make + ' ' + self.model
           return long_name.title()
  	def read_odometer(self):
  		print("This car has " + str(self.odometer_reading) + " miles on it.")
  	def update_odometer(self, mileage):
  		if mileage >= self.odometer_reading:
  			self.odometer_reading = mileage
  		else:
  			print("You can't roll back an odometer!")
  	def increment_odometer(self, miles):
  		self.odometer_reading += miles
  # 括号中写的是继承的类，如果没有写，默认继承object        
  class ElectricCar(Car):
  	"""电动汽车的独特之处"""
  	def __init__(self, make, model, year):
  		"""初始化父类的属性"""
          # 继承的父类，首先先调用的是父类的__init__方法
          # 这儿的super()指向的父类的引用
  		super().__init__(make, model, year)
          
  my_tesla = ElectricCar('tesla', 'model s', 2016)
  # 默认是先查找子类的方法，如果查找不到的话，在查找父类的方法，如果没有直接报错
  print(my_tesla.get_descriptive_name())
  # 输出：2016 Tesla Model S
  ```

- 给子类定义属性和方法 

  ```python
  class Car():
  	--snip--
  class ElectricCar(Car):
  	"""Represent aspects of a car, specific to electric vehicles."""
  	def __init__(self, make, model, year):
  		"""
  		电动汽车的独特之处
  		初始化父类的属性， 再初始化电动汽车特有的属性
  		"""
  		super().__init__(make, model, year)
          # 子类特有的属性
  		self.battery_size = 70
  	# 子类特有的方法
      def describe_battery(self):
  		"""打印一条描述电瓶容量的消息"""
  		print("This car has a " + str(self.battery_size) + "-kWh battery.")
          
  my_tesla = ElectricCar('tesla', 'model s', 2016)
  print(my_tesla.get_descriptive_name())
  my_tesla.describe_battery()
  # 输出：
  # 2016 Tesla Model S
  # This car has a 70-kWh battery.
  ```

- 重写父类的方法 

  ```python
  class ElectricCar(Car):
  	--snip--
      # 假设父类中有一个方法fill_gas_tank方法，子类中方法签名和父类中某个方法的方法签名一样，就是重写父类的方法
  	def fill_gas_tank():
  		"""电动汽车没有油箱"""
  		print("This car doesn't need a gas tank!")
  ```

- 将实例用作属性 

  ```python
  class Car():
  	--snip--
      
  class Battery():
  	"""一次模拟电动汽车电瓶的简单尝试"""
  	def __init__(self, battery_size=70):
  		"""初始化电瓶的属性"""
  		self.battery_size = battery_size
  	def describe_battery(self):
  		"""打印一条描述电瓶容量的消息"""
  		print("This car has a " + str(self.battery_size) + "-kWh battery.")
      def get_range(self):
  		"""打印一条消息， 指出电瓶的续航里程"""
  		if self.battery_size == 70:
  			range = 240
  		elif self.battery_size == 85:
  			range = 270
  		message = "This car can go approximately " + str(range)
           message += " miles on a full charge."
  		print(message)
          
  class ElectricCar(Car):
  	"""电动汽车的独特之处"""
  	def __init__(self, make, model, year):
  		"""
  		初始化父类的属性， 再初始化电动汽车特有的属性
  		"""
  		super().__init__(make, model, year)
          # 将一个类的作为一个类的属性传入
  		self.battery = Battery()
          
  my_tesla = ElectricCar('tesla', 'model s', 2016)
  print(my_tesla.get_descriptive_name())
  # 拿到了电池的属性，电池属性是一个类，再调用就是电池中的属性
  my_tesla.battery.describe_battery()
  my_tesla.battery.get_range()
  
  # 输出：
  # 2016 Tesla Model S
  # This car has a 70-kWh battery.
  # This car can go approximately 240 miles on a full charge.
  ```


### 导入类

- 导入单个类 

  ```python
  """一个可用于表示汽车的类"""
  class Car():
  	"""一次模拟汽车的简单尝试"""
  	def __init__(self, make, model, year):
  		"""初始化描述汽车的属性"""
  		self.make = make
  		self.model = model
  		self.year = year
  		self.odometer_reading = 0
  	def get_descriptive_name(self):
  		"""返回整洁的描述性名称"""
  		long_name = str(self.year) + ' ' + self.make + ' ' + self.model
  		return long_name.title()
  	def read_odometer(self):
  		"""打印一条消息， 指出汽车的里程"""
  		print("This car has " + str(self.odometer_reading) + " miles on it.")
  	def update_odometer(self, mileage):
  		"""
  		将里程表读数设置为指定的值
  		拒绝将里程表往回拨
  		"""
  		if mileage >= self.odometer_reading:
  			self.odometer_reading = mileage
  		else:
  			print("You can't roll back an odometer!")
  	def increment_odometer(self, miles):
  		"""将里程表读数增加指定的量"""
  		self.odometer_reading += miles
  ```

  ```python
  # from 文件名 import 类名
  from car import Car
  
  my_new_car = Car('audi', 'a4', 2016)
  print(my_new_car.get_descriptive_name())
  
  my_new_car.odometer_reading = 23
  my_new_car.read_odometer()
  # 输出：
  # 2016 Audi A4
  # This car has 23 miles on it.
  ```

- 在一个模块中存储多个类 

  ```python
  """一组用于表示燃油汽车和电动汽车的类"""
  class Car():
  	--snip--
  class Battery():
  	"""一次模拟电动汽车电瓶的简单尝试"""
  	def __init__(self, battery_size=60):
  		"""初始化电瓶的属性"""
  		self.battery_size = battery_size
  	def describe_battery(self):
  		"""打印一条描述电瓶容量的消息"""
  		print("This car has a " + str(self.battery_size) + "-kWh battery.")
  	def get_range(self):
  		"""打印一条描述电瓶续航里程的消息"""
  		if self.battery_size == 70:
  			range = 240
  		elif self.battery_size == 85:
  			range = 270
  		message = "This car can go approximately " + str(range)
  		message += " miles on a full charge."
  		print(message)
  class ElectricCar(Car):
  	"""模拟电动汽车的独特之处"""
  	def __init__(self, make, model, year):
  		"""
  		初始化父类的属性， 再初始化电动汽车特有的属性
  		"""
  		super().__init__(make, model, year)
  		self.battery = Battery()
  ```

  ```python
  # 导入一个类的时候，会将它关联的类的都导入
  from car import ElectricCar
  
  my_tesla = ElectricCar('tesla', 'model s', 2016)
  print(my_tesla.get_descriptive_name())
  
  my_tesla.battery.describe_battery()
  my_tesla.battery.get_range()
  
  # 输出：
  # 2016 Tesla Model S
  # This car has a 70-kWh battery.
  # This car can go approximately 240 miles on a full charge.
  ```

- 从一个模块中导入多个类 

  ```python
  # 导入多个类的时候，用逗号分隔
  from car import Car, ElectricCar
  
  my_beetle = Car('volkswagen', 'beetle', 2016)
  print(my_beetle.get_descriptive_name())
  
  my_tesla = ElectricCar('tesla', 'roadster', 2016)
  print(my_tesla.get_descriptive_name())
  
  # 输出：
  # 2016 Volkswagen Beetle
  # 2016 Tesla Roadster
  ```

- 导入整个模块 

  ```python
  # import 文件名，就是导入这个模块
  import car
  
  my_beetle = car.Car('volkswagen', 'beetle', 2016)
  print(my_beetle.get_descriptive_name())
  
  my_tesla = car.ElectricCar('tesla', 'roadster', 2016)
  print(my_tesla.get_descriptive_name())
  ```

### 类编码风格

- 类名应采用**驼峰命名法** ， 即将类名中的每个单词的首字母都大写， 而不使用下划线。  
- 实例名和模块名都采用小写格式， 并在单词之间加上下划线。 

## 文件和异常

### 从文件中读取数据

- 读取整个文件 

  ```txt
  3.1415926535
  8979323846
  2643383279
  ```

  ```python
  # Python在当前执行的文件所在的目录中查找指定的文件。传入进去的是文件名
  # 关键字with 在不再需要访问文件后将其关闭。
  with open('pi_digits.txt') as file_object:
      #read() 到达文件末尾时返回一个空字符串， 而将这个空字符串显示出来时就是一个空行
  	contents = file_object.read()
  	print(contents.rstrip())
  # 输出：
  # 3.1415926535
  #   8979323846
  #   2643383279
  ```

- 文件路径 

  - 文件在计算机中的准确位置告诉Python， 这样就不用关心当前运行的程序存储在什么地方了。 这称为绝对文件路径  
  - 相对文件路径让Python到指定的位置去查找， 而该位置是相对于当前运行的程
    序所在目录的。 

- 逐行读取 

  ```python
  filename = 'pi_digits.txt'
  with open(filename) as file_object:
  	for line in file_object:
  		print(line.rstrip())
  # 输出：
  # 3.1415926535
  #   8979323846
  #   2643383279
  ```

- 创建一个包含文件各行内容的列表 

  ```python
  filename = 'pi_digits.txt'
  with open(filename) as file_object:
      # 将读到的内容读到内存中然后存储到列表中
  	lines = file_object.readlines()
      
  for line in lines:
  	print(line.rstrip())
  ```

- 使用文件的内容 

  ```python
  filename = 'pi_30_digits.txt'
  with open(filename) as file_object:
  	lines = file_object.readlines()
  pi_string = ''
  for line in lines:
      # 读到内容进行拼串
  	pi_string += line.strip()
  # 打印拼串好的内容
  print(pi_string)
  # 打印拼串好的内容的长度
  print(len(pi_string))
  # 输出：
  # 3.141592653589793238462643383279
  # 32
  ```

- 包含一百万位的大型文件 

  ```python
  filename = 'pi_million_digits.txt'
  
  with open(filename) as file_object:
  	lines = file_object.readlines()
      
  pi_string = ''
  for line in lines:
  	pi_string += line.strip()
  
  # 文件过长，只取其中的一部分 
  print(pi_string[:52] + "...")
  print(len(pi_string))
  # 输出：
  # 3.14159265358979323846264338327950288419716939937510...
  # 1000002
  ```


### 写入文件

- 写入空文件 

  ```python
  filename = 'programming.txt'
  with open(filename, 'w') as file_object:
  	file_object.write("I love programming.")
  ```


|      |         模式         |
| :--: | :------------------: |
| 'r'  |       读取模式       |
| 'w'  |       写入模式       |
| 'a'  |       附加模式       |
| 'r+' | 读取和写入文件的模式 |

- 写入多行 

  ```python
  filename = 'programming.txt'
  # 会将原来的文件的内容覆盖掉
  with open(filename, 'w') as file_object:
  	file_object.write("I love programming.\n")
  	file_object.write("I love creating new games.\n")
  ```

- 附加到文件 

  ```python
  filename = 'programming.txt'
  # 将新的内容追加到文件中
  with open(filename, 'a') as file_object:
  	file_object.write("I also love finding meaning in large datasets.\n")
  	file_object.write("I love creating apps that can run in a browser.\n")
  ```


### 异常

- 处理ZeroDivisionError 异常，使用try-except 代码块 

  ```python
  try:
      # 可能要出错的语句
  	print(5/0)
  # 捕获的异常    
  except ZeroDivisionError:
  	print("You can't divide by zero!")
  ```

- 使用异常避免崩溃 ，else 代码块 

  ```python
  print("Give me two numbers, and I'll divide them.")
  print("Enter 'q' to quit.")
  while True:
  	first_number = input("\nFirst number: ")
  	if first_number == 'q':
  		break
  	second_number = input("Second number: ")
  	try:
  		answer = int(first_number) / int(second_number)
  	except ZeroDivisionError:
  		print("You can't divide by 0!")
  	# 依赖于try 代码块成功执行的代码都放在else 代码块中； 
      # 在这个示例中,如果除法运算成功， 我们就使用else 代码块来打印结果
      # 如果try 代码块因除零错误而失败， 我们就打印一条友好的消息
      else:
  		print(answer)
  # 输出：
  # Give me two numbers, and I'll divide them.
  # Enter 'q' to quit.
  #
  # First number: 5
  # Second number: 0
  # You can't divide by 0!
  # 
  # First number: 5
  # Second number: 2
  # 2.5
  # 
  # First number: q
  ```

- 处理FileNotFoundError 异常 

  ```python 
  filename = 'alice.txt'
  try:
  	with open(filename) as f_obj:
  		contents = f_obj.read()
  except FileNotFoundError:
  	msg = "Sorry, the file " + filename + " does not exist."
  	print(msg)
  # 输出：Sorry, the file alice.txt does not exist. 
  ```

- 分析文本

  ```python
  filename = 'alice.txt'
  try:
  	with open(filename) as f_obj:
  		contents = f_obj.read()
  except FileNotFoundError:
  	msg = "Sorry, the file " + filename + " does not exist."
  	print(msg)
  else:
  	# 计算文件大致包含多少个单词
  	words = contents.split()
   	num_words = len(words)
  	print("The file " + filename + " has about " + str(num_words) + " words.")
  ```

- 使用多个文件 

  ```python
  def count_words(filename):
  	"""计算一个文件大致包含多少个单词"""
  	try:
  		with open(filename) as f_obj:
  		contents = f_obj.read()
  	except FileNotFoundError:
  		msg = "Sorry, the file " + filename + " does not exist."
  		print(msg)
  	else:
  		# 计算文件大致包含多少个单词
  		words = contents.split()
  		num_words = len(words)
  		print("The file " + filename + " has about " + str(num_words) +
  		" words.")
          
  filenames = ['alice.txt', 'siddhartha.txt', 'moby_dick.txt', 'little_women.txt']
  for filename in filenames:
  count_words(filename)
  
  # 文件不存在，不会影响程序的执行
  # 输出：
  # The file alice.txt has about 29461 words.
  # Sorry, the file siddhartha.txt does not exist.
  # The file moby_dick.txt has about 215136 words.
  # The file little_women.txt has about 189079 words.
  ```


### 存储数据

- 使用json.dump() 和json.load() 

  ```python
  import json
  numbers = [2, 3, 5, 7, 11, 13]
  filename = 'numbers.json'
  with open(filename, 'w') as f_obj:
  	json.dump(numbers, f_obj)
  ```

  ```python
  import json
  filename = 'numbers.json'
  with open(filename) as f_obj:
  	numbers = json.load(f_obj)
  	print(numbers)
  # 输出：[2, 3, 5, 7, 11, 13]
  ```

- 保存和读取用户生成的数据 

  ```python
  import json
  username = input("What is your name? ")
  filename = 'username.json'
  with open(filename, 'w') as f_obj:
  	json.dump(username, f_obj)
  	print("We'll remember you when you come back, " + username + "!")
  # 输出：
  # What is your name? Eric
  # We'll remember you when you come back, Eric!
  ```

  ```python
  import json
  filename = 'username.json'
  with open(filename) as f_obj:
  username = json.load(f_obj)
  print("Welcome back, " + username + "!")
  # 输出：Welcome back, Eric!
  ```

- 重构 

  ```python
  import json
  def get_stored_username():
  	"""如果存储了用户名， 就获取它"""
  	filename = 'username.json'
  	try:
  		with open(filename) as f_obj:
  			username = json.load(f_obj)
  	except FileNotFoundError:
  		return None
  	else:
  		return username
  def get_new_username():
  	"""提示用户输入用户名"""
  	username = input("What is your name? ")
  	filename = 'username.json'
  	with open(filename, 'w') as f_obj:
  		json.dump(username, f_obj)
  	return username
  def greet_user():
  	"""问候用户， 并指出其名字"""
  	username = get_stored_username()
  	if username:
  		print("Welcome back, " + username + "!")
  	else:
  		username = get_new_username()
  		print("We'll remember you when you come back, " + username + "!")
  greet_user()
  
  ```

## 测试代码

### 测试函数

- 单元测试和测试用例 

  - 单元测试 用于核实函数的某个方面没有问题； 测试用例 是一组单元测试， 这些单元测试一起核实函数在各种情形下的行为都符合要求。

- 可通过的测试 

  ```python
  import unittest
  from name_function import get_formatted_name
  class NamesTestCase(unittest.TestCase):
  	"""测试name_function.py"""
  	def test_first_last_name(self):
  		"""能够正确地处理像Janis Joplin这样的姓名吗？ """
  		formatted_name = get_formatted_name('janis', 'joplin')
          # 断言方法用来核实得到的结果是否与期望的结果一致。
  		self.assertEqual(formatted_name, 'Janis Joplin')
  # 这样调用
  unittest.main()
  ```


### 测试类

- 各种断言方法 

  |           方法            |          用途          |
  | :-----------------------: | :--------------------: |
  |     assertEqual(a, b)     |       核实a == b       |
  |   assertNotEqual(a, b)    |       核实a != b       |
  |       assertTrue(x)       |      核实x 为True      |
  |      assertFalse(x)       |     核实x 为False      |
  |  assertIn(item , list )   |  核实 item 在 list 中  |
  | assertNotIn(item , list ) | 核实 item 不在 list 中 |

  ```python
  class AnonymousSurvey():
  	"""收集匿名调查问卷的答案"""
      
  	def __init__(self, question):
  		"""存储一个问题， 并为存储答案做准备"""
  		self.question = question
  		self.responses = []
          
  	def show_question(self):
  		"""显示调查问卷"""
  		print(question)
          
  	def store_response(self, new_response):
  		"""存储单份调查答卷"""
  		self.responses.append(new_response)
          
  	def show_results(self):
  		"""显示收集到的所有答卷"""
  		print("Survey results:")
  		for response in responses:
  			print('- ' + response)
  ```

  ```python
from survey import AnonymousSurvey
  #定义一个问题， 并创建一个表示调查的AnonymousSurvey对象
  question = "What language did you first learn to speak?"
  my_survey = AnonymousSurvey(question)
  #显示问题并存储答案
  my_survey.show_question()
  print("Enter 'q' at any time to quit.\n")
  while True:
  	response = input("Language: ")
  	if response == 'q':
  		break
  	my_survey.store_response(response)
  # 显示调查结果
  print("\nThank you to everyone who participated in the survey!")
  my_survey.show_results()
  ```
  
  ```python
import unittest
  from survey import AnonymousSurvey

  class TestAnonmyousSurvey(unittest.TestCase):
  	"""针对AnonymousSurvey类的测试"""
      
      # 测试程序开始会运行它
      def setUp(self):
  		"""
  		创建一个调查对象和一组答案， 供使用的测试方法使用
  		"""
  		question = "What language did you first learn to speak?"
  		self.my_survey = AnonymousSurvey(question)
  		self.responses = ['English', 'Spanish', 'Mandarin']
      
  	def test_store_single_response(self):
  		"""测试单个答案会被妥善地存储"""
  		question = "What language did you first learn to speak?"
  		my_survey = AnonymousSurvey(question)
  		my_survey.store_response('English')
  		self.assertIn('English', my_survey.responses)
          
      def test_store_three_responses(self):
  		"""测试三个答案会被妥善地存储"""
  		question = "What language did you first learn to speak?"
  		my_survey = AnonymousSurvey(question)
  		responses = ['English', 'Spanish', 'Mandarin']
  		for response in responses:
  			my_survey.store_response(response)
  		for response in responses:
  			self.assertIn(response, my_survey.responses)
  unittest.main()
  ```
  
  