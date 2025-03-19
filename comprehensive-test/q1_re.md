```python
import os
import re
from collections import Counter

# 資料夾路徑
script_dir = os.path.dirname(os.path.abspath(__file__))
file_path = os.path.join(script_dir, "words.txt")

# 讀取檔案內容
try:
    with open(file_path, "r", encoding="utf-8") as file:
        content = file.read()
except FileNotFoundError:
    print("找不到檔案，請確認 words.txt 是否在腳本所在的資料夾。")
    exit()

# 太小寫忽略不計
content = content.lower()

# 正規表達式查詢
words = re.findall(r'\b\w+\b', content)

if not words:
    print("檔案中找不到任何單字。")
else:
    # 統計次數
    word_counts = Counter(words)
    
    # 獲得出現次數最多的單字
    most_common = word_counts.most_common(1)
    if not most_common:
        print("找不到統計資料。")
    else:
        most_common_word, count = most_common[0]
        print(f"出現最多次的單字為：{most_common_word}，出現次數：{count}")
```
<font size=6 color=green face=粉圓體>預設</font><font size=6 color=red face=粉圓體>words.tx</font>檔案<font size=6 color=yellow face=粉圓體>在.py檔</font><font size=6 color=orange face=粉圓體>同一個</font><font size=6 color=green face=粉圓體>資料夾</font>
