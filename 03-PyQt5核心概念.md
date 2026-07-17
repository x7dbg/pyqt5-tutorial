# PyQt5核心概念

在继续深入之前，我们需要先搞懂几个核心概念。这些概念就像是学开车前要先认识的仪表盘——不懂也能开，但懂了才能开得稳。

别被"概念"两个字吓到，我会用最接地气的方式给你讲明白。

---

## 1. PyQt5 模块结构

PyQt5 是个"大家伙"，里面装了很多模块。就像一个大工具箱，不同的工具放在不同的抽屉里。

### 1.1 核心模块速览

| 模块名 | 相当于工具箱里的哪个抽屉 | 常用工具 |
|--------|------|--------|
| `QtCore` | 基础工具层（螺丝刀、扳手） | 定时器、线程、信号 |
| `QtGui` | 美术用品（颜料、画笔） | 图标、图片、字体 |
| `QtWidgets` | 家具区（桌椅板凳） | 按钮、标签、窗口 |
| `QtSql` | 档案柜 | 数据库操作 |

> 💡 **小贴士**：90% 的时间你只需要用 `QtWidgets`，其他模块用到的时候再查就行，不用死记。

### 1.2 怎么导入？

```python
# 方式1：整个模块搬进来（不推荐，太占地方）
from PyQt5 import QtWidgets, QtCore, QtGui

# 方式2：只拿需要的（推荐，干净利落）
from PyQt5.QtWidgets import QApplication, QWidget, QPushButton
from PyQt5.QtCore import Qt, QTimer, pyqtSignal
from PyQt5.QtGui import QIcon, QPixmap
```

推荐方式2，就像去超市只买需要的东西，别把整个货架搬回家。

---

## 2. 程序基本结构

每个 PyQt5 程序都长得差不多，就像每栋房子都有地基、墙壁、屋顶一样。

### 标准模板（建议收藏）

```python
# -*- coding: utf-8 -*-
import sys
from PyQt5.QtWidgets import QApplication, QWidget

class MainWindow(QWidget):
    def __init__(self):
        super().__init__()
        self.initUI()
    
    def initUI(self):
        # 1. 设置窗口属性
        self.setWindowTitle('窗口标题')
        self.setGeometry(300, 300, 300, 200)
        
        # 2. 添加控件
        # ...
        
        # 3. 显示窗口
        self.show()

if __name__ == '__main__':
    # 1. 创建应用程序对象
    app = QApplication(sys.argv)
    
    # 2. 创建主窗口
    window = MainWindow()
    
    # 3. 进入主循环
    sys.exit(app.exec_())
```

### 程序是怎么跑起来的？

想象一下开餐厅的流程：

```
1. 开店（创建 QApplication）
   ↓
2. 装修（创建主窗口 MainWindow）
   ↓
3. 开门营业（调用 show()）
   ↓
4. 等待客人（进入 app.exec_() 主循环）
   ↓
5. 处理客人需求（监听事件并处理）
   ↓
6. 打烊关门（关闭窗口，退出主循环）
```

### 窗口位置和大小：resize() vs setGeometry()

代码里出现了两种设置窗口大小的方式，它们有什么区别？

```python
# 方式1：只设置大小
window.resize(300, 200)

# 方式2：同时设置位置和大小
window.setGeometry(300, 300, 300, 200)
#              ↑    ↑    ↑    ↑
#            x坐标 y坐标 宽度 高度
```

- **`resize(宽, 高)`**：只设置窗口大小，位置由系统决定
- **`setGeometry(x, y, 宽, 高)`**：同时设置窗口位置和大小

还有一个常用的 `move()` 方法：

```python
window.move(300, 300)  # 只移动位置，不改变大小
```

**三者关系**：`setGeometry(x, y, w, h)` = `move(x, y)` + `resize(w, h)`

### 固定窗口大小

如果你不想让用户拉伸窗口：

```python
window.setFixedSize(300, 200)  # 固定大小，不能拉伸
```

> 📌 **重点**：这个模板以后会反复出现，建议先混个脸熟，写多了就成肌肉记忆了。

---

## 3. 对象树机制

### 3.1 什么是对象树？

这是 PyQt5 里最重要的概念之一，理解了它，你就理解了一半的 PyQt5。

想象一个家族族谱：

```
窗口（QWidget / QMainWindow）
├── 菜单栏（QMenuBar）
├── 工具栏（QToolBar）
├── 状态栏（QStatusBar）
└── 中央区域（QWidget）
    ├── 按钮（QPushButton）
    ├── 标签（QLabel）
    └── 输入框（QLineEdit）
```

在 PyQt5 里，每个控件都可以有"爸爸"和"儿子"。创建控件时指定了"爸爸"，它就自动成为"爸爸"的一部分。

> ⚠️ **注意**：`QApplication` 不是控件树的一部分，它是整个应用的"大管家"，管理程序生命周期和事件循环。真正的控件树是从窗口（`QWidget` 或 `QMainWindow`）开始的。

### 3.2 对象树有什么用？

**最大的好处：自动收拾烂摊子（内存管理）**

```python
# -*- coding: utf-8 -*-
import sys
from PyQt5.QtWidgets import QApplication, QWidget, QPushButton

class Example(QWidget):
    def __init__(self):
        super().__init__()
        
        # 按钮的"爸爸"是 self（这个窗口）
        # 窗口关闭时，按钮也会被自动清理掉
        btn = QPushButton('按钮', self)
        btn.move(50, 50)
        
        self.setWindowTitle('对象树示例')
        self.show()

if __name__ == '__main__':
    app = QApplication(sys.argv)
    ex = Example()
    sys.exit(app.exec_())
```

看到 `btn = QPushButton('按钮', self)` 这行没？那个 `self` 就是在认爸爸。

**如果没有对象树会怎样？**

想象你去餐厅吃饭，吃完后需要自己洗碗、擦桌子、倒垃圾……每个控件都要手动清理，写代码写到崩溃。

有了对象树，爸爸被清理时，儿子们自动跟着被清理。你只需要管好"爸爸"就行，不用操心每个"儿子"。

> 💡 **一句话总结**：创建控件时记得指定父对象，剩下的交给 PyQt5 自动处理。

### 3.3 怎么认爸爸？

有两种方式：

```python
# 方式1：创建时就认爸爸（推荐）
btn = QPushButton('按钮', parent)

# 方式2：创建后再认爸爸
btn = QPushButton('按钮')
btn.setParent(parent)
```

推荐方式1，一步到位，干净利落。

---

## 4. 信号与槽机制

### 4.1 这是什么鬼？

信号与槽，听起来很玄乎，其实特别简单。

**生活中的例子**：

你去餐厅吃饭：
- 你举手喊"服务员" → 这是**发信号**
- 服务员听到后走过来 → 这是**槽响应**
- 你和服务员之间的联系 → 这就是**信号与槽的连接**

```
你举手（发射信号） → 服务员听到（连接） → 走过来处理（执行槽函数）
```

### 4.2 代码里长什么样？

```python
# -*- coding: utf-8 -*-
import sys
from PyQt5.QtWidgets import QApplication, QWidget, QPushButton
from PyQt5.QtCore import QCoreApplication

class Example(QWidget):
    def __init__(self):
        super().__init__()
        self.initUI()
    
    def initUI(self):
        btn = QPushButton('退出', self)
        btn.move(50, 50)
        
        # 核心就这一行：按钮被点击 → 程序退出
        btn.clicked.connect(QCoreApplication.instance().quit)
        
        self.setWindowTitle('信号与槽示例')
        self.show()

if __name__ == '__main__':
    app = QApplication(sys.argv)
    ex = Example()
    sys.exit(app.exec_())
```

```python
btn.clicked.connect(QCoreApplication.instance().quit)
```

这行代码翻译成人话就是：
> "当按钮被点击时（`clicked`），执行退出程序的操作（`quit`）"

- `clicked` 是按钮发出的**信号**
- `quit` 是接收信号的**槽函数**
- `connect()` 把它们连在一起

### 4.3 信号与槽的三种玩法

```python
# 玩法1：连接到内置函数（最简单）
btn.clicked.connect(app.quit)

# 玩法2：连接到自定义函数（最常用）
btn.clicked.connect(self.my_function)

def my_function(self):
    print('按钮被点击了！')

# 玩法3：信号连信号（高级玩法）
signal1.connect(signal2)
```

### 4.4 带参数的信号与槽（实用技巧）

实际开发中，经常需要在点击按钮时传递参数。这时候要用 `lambda`：

```python
# 错误写法：这样会立即执行函数，而不是在点击时执行
btn.clicked.connect(self.my_function('参数'))

# 正确写法：用 lambda 包裹
btn.clicked.connect(lambda: self.my_function('参数'))

def my_function(self, text):
    print(f'按钮被点击了，参数是：{text}')
```

> 🎮 **动手试试**：把上面的代码跑起来，点击"退出"按钮，看看程序是不是关掉了？

---

## 5. 窗口类型

PyQt5 提供了三种主要的窗口类型，选对窗口类型是写好界面的第一步。

### 5.1 QWidget —— 最基础的画布

最基本的窗口类型，所有可视控件的基类。

```python
from PyQt5.QtWidgets import QWidget

window = QWidget()
window.setWindowTitle('QWidget 窗口')
window.resize(300, 200)
window.show()
```

**什么时候用**：简单的对话框、自定义控件、不需要菜单栏的轻量级窗口。

### 5.2 QMainWindow —— 功能完整的主窗口

带有菜单栏、工具栏、状态栏的主窗口，内部有预设的布局结构。

```python
from PyQt5.QtWidgets import QMainWindow

window = QMainWindow()
window.setWindowTitle('QMainWindow 主窗口')
window.resize(500, 400)
window.show()
```

**什么时候用**：需要菜单栏、工具栏、状态栏的完整应用程序（如文本编辑器、图片查看器）。

### 5.3 QDialog —— 临时弹出的对话框

对话框窗口，通常用于短期任务，会阻塞主窗口。

```python
from PyQt5.QtWidgets import QDialog

dialog = QDialog()
dialog.setWindowTitle('对话框')
dialog.resize(200, 150)
dialog.show()
```

**什么时候用**：确认弹窗、设置窗口、登录界面等需要用户交互后关闭的场景。

### 5.4 三种窗口怎么选？

| 场景 | 推荐窗口类型 |
|------|-------------|
| 简单小工具、自定义控件 | `QWidget` |
| 完整的桌面应用（有菜单、工具栏） | `QMainWindow` |
| 弹窗、设置页、登录框 | `QDialog` |

> 💡 **新手建议**：不确定用哪个的时候，先用 `QWidget`，够用就行，后面需要再加功能。

---

## 6. 事件循环

PyQt5 应用是事件驱动的。事件循环不断监听并处理事件。

### 6.1 创建应用程序对象

```python
app = QApplication(sys.argv)
```

这行代码创建了应用程序对象，它是整个程序的"心脏"，内部包含了事件循环机制。

> 📌 **注意**：每个 PyQt5 应用必须有且只有一个 `QApplication` 对象，`sys.argv` 是命令行参数。

### 6.2 启动事件循环

```python
sys.exit(app.exec_())
```

`app.exec_()` 启动事件循环，程序进入"等待状态"，开始处理用户的各种操作。

### 6.3 事件循环是怎么工作的？

```python
# 事件循环伪代码
while True:
    event = get_next_event()
    if event is None:
        break
    dispatch_event(event)
```

简单理解：
1. 程序进入 `app.exec_()` 后，就像一个待命的服务员
2. 不断监听有没有"客人"（用户操作）
3. 有事件来了，就分发给对应的控件处理
4. 窗口关闭时，事件循环结束，程序退出

**常见事件类型**：
- 鼠标事件：点击、移动、滚轮
- 键盘事件：按键、释放
- 窗口事件：显示、隐藏、关闭
- 定时器事件：定时触发

---

## 7. Qt 命名空间速查

`Qt` 命名空间提供了很多常用常量，后续章节会频繁用到。

### 7.1 对齐方式

```python
from PyQt5.QtCore import Qt

label.setAlignment(Qt.AlignCenter)    # 居中对齐
label.setAlignment(Qt.AlignLeft)      # 左对齐
label.setAlignment(Qt.AlignRight)     # 右对齐
```

### 7.2 窗口标志

```python
from PyQt5.QtCore import Qt

# 设置窗口固定大小（不能拉伸）
window.setWindowFlags(Qt.WindowCloseButtonHint | Qt.WindowMinimizeButtonHint)

# 让窗口始终置顶
window.setWindowFlags(Qt.WindowStaysOnTopHint)
```

### 7.3 常用按键

```python
# 在键盘事件中判断按键
if event.key() == Qt.Key_Enter:
    print('回车键被按下')
if event.key() == Qt.Key_Escape:
    print('Esc键被按下')
```

---

## 8. 常用基础类速查

### 8.1 QApplication

应用程序对象，每个 PyQt5 应用必须有且只有一个。

```python
app = QApplication(sys.argv)
```

### 8.2 QWidget

所有可视控件的基类。

```python
widget = QWidget()
widget.setWindowTitle('窗口标题')
widget.resize(300, 200)
widget.show()
```

### 8.3 QLabel

显示文本或图片的标签。

```python
label = QLabel('文本内容', parent)
label.move(50, 50)
```

### 8.4 QPushButton

可点击的按钮。

```python
btn = QPushButton('按钮文本', parent)
btn.move(50, 100)
btn.clicked.connect(callback_function)
```

---

## 9. 新手常见错误

### 9.1 忘记创建 QApplication

```python
# 错误：直接创建控件会报错
btn = QPushButton('按钮')

# 正确：先创建 QApplication
app = QApplication(sys.argv)
btn = QPushButton('按钮')
```

### 9.2 忘记调用 show()

```python
window = QWidget()
window.setWindowTitle('窗口')
# 忘记写 window.show()，运行后什么都看不到！
```

> 控件创建后默认是隐藏的，必须调用 `show()` 才会显示。

### 9.3 忘记指定父对象导致控件不显示

```python
# 错误：按钮没有父对象，不会出现在窗口上
btn = QPushButton('按钮')
btn.move(50, 50)

# 正确：指定父对象
btn = QPushButton('按钮', self)
btn.move(50, 50)
```

### 9.3 忘记指定父对象导致控件变成独立窗口

```python
# 错误：按钮没有父对象，会变成一个独立的小窗口
btn = QPushButton('按钮')
btn.move(50, 50)

# 正确：指定父对象，按钮才会出现在窗口内部
btn = QPushButton('按钮', self)
btn.move(50, 50)
```

> ⚠️ **注意**：没有父对象的控件不会报错，而是会作为独立窗口显示，这往往不是我们想要的效果。

### 9.4 信号连接时加了括号

```python
# 错误：这样会立即执行函数，而不是在点击时执行
btn.clicked.connect(self.my_function())

# 正确：不要加括号
btn.clicked.connect(self.my_function)
```

---

## 10. 动手练习

学完核心概念后，试试写一个完整的程序来巩固一下。

### 练习：做一个简单的计算器界面

要求：
1. 窗口标题显示"简单计算器"
2. 窗口大小 300x200，位置在屏幕 (400, 300)
3. 放一个标签显示"结果："
4. 放两个按钮："加"和"减"
5. 点击按钮时在控制台打印"点击了加/减"

参考答案：

```python
# -*- coding: utf-8 -*-
import sys
from PyQt5.QtWidgets import QApplication, QWidget, QLabel, QPushButton

class Calculator(QWidget):
    def __init__(self):
        super().__init__()
        self.initUI()
    
    def initUI(self):
        # 设置窗口
        self.setWindowTitle('简单计算器')
        self.setGeometry(400, 300, 300, 200)
        
        # 添加标签
        label = QLabel('结果：', self)
        label.move(50, 30)
        
        # 添加按钮
        btn_add = QPushButton('加', self)
        btn_add.move(50, 80)
        btn_add.clicked.connect(lambda: self.on_click('加'))
        
        btn_sub = QPushButton('减', self)
        btn_sub.move(150, 80)
        btn_sub.clicked.connect(lambda: self.on_click('减'))
        
        self.show()
    
    def on_click(self, action):
        print(f'点击了{action}')

if __name__ == '__main__':
    app = QApplication(sys.argv)
    calc = Calculator()
    sys.exit(app.exec_())
```

> 🎮 **挑战任务**（选做）：
> 1. 把窗口改成固定大小，不能拉伸
> 2. 让按钮点击后弹出提示框
>
> ---
>
> 💡 **提示**（先自己试试，想不出来再看）：
> - 任务1：用 `self.setFixedSize(300, 200)` 替换 `setGeometry()`
> - 任务2：导入 `QMessageBox`，使用 `QMessageBox.information(self, '标题', '内容')`

---

## 本章小结

学完这一章，你应该掌握了：

- PyQt5 的模块结构和导入方式
- 程序的标准模板和运行流程
- 窗口位置和大小设置（resize、move、setGeometry、setFixedSize）
- 对象树机制（父子关系和内存管理）
- 信号与槽机制（事件驱动的核心，包括 lambda 传参）
- 三种窗口类型的选择
- 事件循环的工作原理
- Qt 命名空间常用常量

理解这些核心概念后，我们就可以开始创建更复杂的界面了。下一章学习菜单和工具栏的使用，它们是主窗口应用的标准组件。