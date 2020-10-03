---
date: 2020-10-02
title: "[Python] import 概念"
description: import 看似簡單卻大有玄機，但弄懂流程其實也不難
permalink: /:year/:month/:day/python/import-concept.html
categories:
- Python
tags:
- Python
- concept
---

<blockquote class="blockquote-center">以下的測試環境<br>Python: 3.8.3</blockquote>

<br>

# 基本介紹
- package (套件/包) : 資料夾，含有 `__init__.py`
- module (模塊) : 檔案
- import 的方式有兩種 : 絕對路徑 / 相對路徑
- sys.modules : 是個 dictionary，用於存放已經 import 過的 modules
- sys.path : 是個 list，用於收尋 import 的 module 的各種路徑

<br>

# import 流程
雖然 import 的**方式**有兩種 : 絕對路徑 / 相對路徑  
但是 import 的**流程**是相同的

`import xxxModule`

1. 檢查 `xxxModule` 是否存在於 `sys.modules`
2. 若存在，則直接從 `sys.modules` 取出使用即可
3. 若不存在，則依據 import 的方式來收尋 `xxxModule.py` 的檔案位置
4. 接著生成 `xxxModule`
5. 再來放入 `sys.modules`
6. 最後執行 `xxxModule.py` 裡面的 source code (以剛生成的 `xxxModule` 作為 scope 來執行)

<br>

# syntax 比較
```python
# 絕對路徑
import xxxModule
from xxxModule import xxxMethod

# 相對路徑
from . import xxxModule # 同一層目錄
from .. import xxxModule # 上一層目錄
from ... import xxxModule # 上上層目錄
from .xxxModule import xxxMethod

# 錯誤寫法
import .xxxModule # . 只能出現在 from 後面 
```

<br>

# 範例 (基礎 / 絕對路徑)
有了以上的概念後，接著我們利用範例來實際操作下 (<a href="https://github.com/hochun836/python_absolute_import" target="_blank">Github</a>)
- [基本練習 1](#基本練習-1)
- [基本練習 2](#基本練習-2)
- [基本練習 3](#基本練習-3)
- [基本練習 4](#基本練習-4)
- [基本練習 5](#基本練習-5)

<br>

## 基本練習 1
- 執行 `D:\hochun\example\python_absolute_import>python app1.py`

```python
# 檔案結構
python_absolute_import
│  app1.py
│
└─packageA
  │  moduleA.py
  │  __init__.py
  │
  └─packageB
      │  moduleB.py
      └─  __init__.py
```

```python
# packageA/__init__.py
print('& packageA')

# packageA/moduleA.py
print('& moduleA')

# packageA/packageB/__init__.py
print('& packageB')

# packageA/packageB/moduleB.py
print('& moduleB')
```

```python
# app1.py
import sys
for idx, path in enumerate(sys.path):
    print(f'sys.path[{idx}]: {path}')

print('========== phase1 ==========')
print('"packageA" in sys.modules:', 'packageA' in sys.modules)
print('"packageA.moduleA" in sys.modules:', 'packageA.moduleA' in sys.modules)
print('"packageA.packageB" in sys.modules:', 'packageA.packageB' in sys.modules)
print('"packageA.packageB.moduleB" in sys.modules:', 'packageA.packageB.moduleB' in sys.modules)

print('========== phase2 ==========')
from packageA.packageB import moduleB
print('"packageA" in sys.modules:', 'packageA' in sys.modules)
print('"packageA.moduleA" in sys.modules:', 'packageA.moduleA' in sys.modules)
print('"packageA.packageB" in sys.modules:', 'packageA.packageB' in sys.modules)
print('"packageA.packageB.moduleB" in sys.modules:', 'packageA.packageB.moduleB' in sys.modules)

print('========== phase3 ==========')
from packageA import moduleA
print('"packageA" in sys.modules:', 'packageA' in sys.modules)
print('"packageA.moduleA" in sys.modules:', 'packageA.moduleA' in sys.modules)
print('"packageA.packageB" in sys.modules:', 'packageA.packageB' in sys.modules)
print('"packageA.packageB.moduleB" in sys.modules:', 'packageA.packageB.moduleB' in sys.modules)

print('========== phase4 ==========')
import packageA
print('packageA:', packageA)
```

<br>

- 輸出

```python
& app1.py
sys.path[0]: D:\hochun\example\python_absolute_import #sys.path[0] 是當前路徑
sys.path[1]: C:\ProgramData\Anaconda3\python38.zip
sys.path[2]: C:\ProgramData\Anaconda3\DLLs
sys.path[3]: C:\ProgramData\Anaconda3\lib
sys.path[4]: C:\ProgramData\Anaconda3
sys.path[5]: C:\ProgramData\Anaconda3\lib\site-packages
sys.path[6]: C:\ProgramData\Anaconda3\lib\site-packages\win32
sys.path[7]: C:\ProgramData\Anaconda3\lib\site-packages\win32\lib
sys.path[8]: C:\ProgramData\Anaconda3\lib\site-packages\Pythonwin
========== phase1 ==========
"packageA" in sys.modules: False
"packageA.moduleA" in sys.modules: False
"packageA.packageB" in sys.modules: False
"packageA.packageB.moduleB" in sys.modules: False
========== phase2 ==========
& packageA # from packageA.packageB import moduleB 先執行 packageA.py
& packageB # from packageA.packageB import moduleB 再執行 packageB.py
& moduleB # from packageA.packageB import moduleB 最後執行 moduleB.py
"packageA" in sys.modules: True # 從 False 改變為 True
"packageA.moduleA" in sys.modules: False
"packageA.packageB" in sys.modules: True # 從 False 改變為 True
"packageA.packageB.moduleB" in sys.modules: True # 從 False 改變為 True
========== phase3 ==========
& moduleA # 由於 sys.modules 已經有了 packageA，所以不會再執行 packageA.py
"packageA" in sys.modules: True
"packageA.moduleA" in sys.modules: True # 從 False 改變為 True
"packageA.packageB" in sys.modules: True
"packageA.packageB.moduleB" in sys.modules: True
========== phase4 ==========
packageA: <module 'packageA' from 'D:\\hochun\\example\\python_absolute_import\\packageA\\__init__.py'>
```

- 觀察



<br>

# absolute import

<br>

# relative import

<br>

# 參考文章

<br>