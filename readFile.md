# Python 檔案操作：一步一步練習

這份教學適合 Python 初學者。完成後，你會知道如何建立、讀取、寫入和附加文字檔，也會學會使用路徑及處理常見錯誤。

> 範例適用於 Python 3。請把程式碼放進 `.py` 檔案執行，或在終端機逐段執行。範例中的檔案會建立在「目前工作目錄」；不知道目前目錄時，可以先使用本文「第 5 步」的程式查看。

## 第 1 步：建立並寫入第一個文字檔

先執行這段程式：

```python
with open("hello.txt", "w", encoding="utf-8") as file:
    file.write("你好，Python！\n")
    file.write("這是我的第一個文字檔。\n")
```

執行後，程式會在目前工作目錄建立 `hello.txt`。

- `open(...)` 開啟檔案。
- `"hello.txt"` 是檔案名稱。
- `"w"` 是寫入模式：檔案不存在時會建立；檔案已存在時會**清空舊內容再寫入**。
- `encoding="utf-8"` 指定文字編碼，能正確處理中文。
- `with` 會在程式離開區塊後自動關閉檔案，即使中途發生錯誤也能妥善處理。
- `file.write(...)` 寫入文字；`\n` 代表換行。

## 第 2 步：讀取整個檔案

在上一段之後執行：

```python
with open("hello.txt", "r", encoding="utf-8") as file:
    content = file.read()

print(content)
```

`"r"` 是讀取模式。它要求檔案已經存在；若檔案不存在，Python 會發生 `FileNotFoundError`。

`read()` 會一次讀取檔案剩餘的全部內容，並回傳一個字串。讀取較大的檔案時，通常不要一次把整個檔案載入記憶體，可以改用第 3 步的逐行讀取。

## 第 3 步：逐行讀取檔案

```python
with open("hello.txt", "r", encoding="utf-8") as file:
    for line in file:
        print(line, end="")
```

把檔案物件放進 `for` 迴圈，就可以一行一行讀取，適合較大的文字檔。這裡的 `end=""` 是為了避免 `print()` 再額外加一個換行，因為每一行通常已經包含換行字元。

另外，也可以只讀取一行：

```python
with open("hello.txt", "r", encoding="utf-8") as file:
    first_line = file.readline()

print(first_line, end="")
```

`readlines()` 會把所有行放進一個清單：

```python
with open("hello.txt", "r", encoding="utf-8") as file:
    lines = file.readlines()

print(lines)
```

例如結果可能是 `['你好，Python！\\n', '這是我的第一個文字檔。\\n']`。`readlines()` 也會一次讀入全部內容，因此大型檔案較適合使用 `for line in file`。

## 第 4 步：附加內容，而不清除舊內容

如果想在檔案最後加上內容，使用 `"a"`：

```python
with open("hello.txt", "a", encoding="utf-8") as file:
    file.write("這一行是後來附加的。\n")
```

`"a"` 是附加模式：檔案不存在時會建立；檔案已存在時，內容會加在最後，不會清除原有文字。

### 常用檔案模式

| 模式 | 用途 | 檔案不存在時 | 檔案已存在時 |
|---|---|---|---|
| `"r"` | 讀取 | 發生 `FileNotFoundError` | 讀取原內容 |
| `"w"` | 寫入 | 建立檔案 | **清除原內容**後寫入 |
| `"a"` | 附加 | 建立檔案 | 在原內容最後附加 |
| `"x"` | 新建檔案 | 建立檔案 | 發生 `FileExistsError`，避免覆蓋 |

初學時，請特別留意 `"w"` 會清除舊內容。如果不希望覆蓋已存在的檔案，可以使用 `"x"`。

## 第 5 步：使用 `pathlib` 處理檔案路徑

`pathlib` 是 Python 內建的路徑工具。使用它可以避免手動拼接路徑字串：

```python
from pathlib import Path

file_path = Path("hello.txt")

print("檔案路徑：", file_path.resolve())
print("檔案存在嗎？", file_path.exists())
```

`Path("hello.txt")` 是相對於目前工作目錄的路徑。`resolve()` 可以顯示完整路徑，方便確認檔案實際位置。

也可以透過 `pathlib` 讀寫文字：

```python
from pathlib import Path

file_path = Path("hello.txt")

# 寫入文字：若檔案已存在，會覆蓋原內容
file_path.write_text("使用 pathlib 寫入文字。\n", encoding="utf-8")

# 讀取全部文字
content = file_path.read_text(encoding="utf-8")
print(content)
```

`read_text()` 適合讀取較小的文字檔；大型檔案仍建議用 `open()` 逐行處理。

## 第 6 步：建立資料夾並把檔案放進去

```python
from pathlib import Path

folder = Path("data")
folder.mkdir(exist_ok=True)

file_path = folder / "message.txt"
file_path.write_text("這個檔案放在 data 資料夾中。\n", encoding="utf-8")

print(file_path.resolve())
```

- `mkdir()` 建立資料夾。
- `exist_ok=True` 表示資料夾已存在時不會因此發生錯誤。
- 使用 `/` 可以把資料夾和檔名組合成路徑；`pathlib` 會處理作業系統之間的路徑格式差異。

## 第 7 步：處理常見錯誤

讀取不存在的檔案會發生 `FileNotFoundError`。你可以先檢查檔案是否存在：

```python
from pathlib import Path

file_path = Path("notes.txt")

if file_path.exists():
    content = file_path.read_text(encoding="utf-8")
    print(content)
else:
    print("找不到 notes.txt，請確認檔名和檔案位置。")
```

若希望程式明確處理讀取錯誤，也可以使用 `try` 和 `except`：

```python
try:
    with open("notes.txt", "r", encoding="utf-8") as file:
        content = file.read()
except FileNotFoundError:
    print("找不到 notes.txt，請先建立檔案或確認路徑。")
else:
    print(content)
```

不要用空白內容假裝讀取成功；遇到錯誤時，應清楚告訴使用者發生什麼事。

## 第 8 步：完整小練習——儲存並讀取待辦事項

以下程式會請使用者輸入三項待辦事項，將它們儲存到 `todos.txt`，然後再讀出來：

```python
from pathlib import Path

file_path = Path("todos.txt")

with file_path.open("w", encoding="utf-8") as file:
    for number in range(1, 4):
        todo = input(f"請輸入第 {number} 項待辦事項：")
        file.write(todo + "\n")

print("\n已儲存的待辦事項：")
with file_path.open("r", encoding="utf-8") as file:
    for line in file:
        print("-", line.strip())
```

`strip()` 會移除字串前後的空白及換行字元，所以輸出時不會因為原本的換行而多出空行。

## 動手練習

試著自己完成以下任務；完成後再對照提示：

1. 建立 `journal.txt`，寫入今天的心情。
2. 讀取 `journal.txt` 並印出內容。
3. 不清除原內容，在檔案最後附加一行新的心情。
4. 使用 `Path.exists()` 檢查 `journal.txt` 是否存在。
5. 把檔案改放到 `data` 資料夾中。

### 練習提示

```python
from pathlib import Path

folder = Path("data")
folder.mkdir(exist_ok=True)
file_path = folder / "journal.txt"

# 建立或覆蓋檔案：write_text(...)
# 附加內容：open(..., "a", encoding="utf-8")
# 讀取內容：read_text(...)
# 檢查是否存在：exists()
```

## 快速複習

- 讀取文字：`open("檔名", "r", encoding="utf-8")`
- 寫入文字（會覆蓋）：`open("檔名", "w", encoding="utf-8")`
- 附加文字（不覆蓋）：`open("檔名", "a", encoding="utf-8")`
- 優先使用 `with` 開啟檔案，讓檔案自動關閉。
- 中文文字檔明確使用 `encoding="utf-8"`。
- 相對路徑是相對於目前工作目錄；可用 `Path("檔名").resolve()` 查看完整路徑。
