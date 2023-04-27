# kot_勤務時間スプレッドシート反映

## 1か月分の勤務時間を取得する

### 実行手順

※事前準備は[こちら](kot_%E5%8B%A4%E5%8B%99%E6%99%82%E9%96%93%E3%82%B9%E3%83%95%E3%82%9A%E3%83%AC%E3%83%83%E3%83%88%E3%82%99%E3%82%B7%E3%83%BC%E3%83%88%E5%8F%8D%E6%98%A0%20dc6660f080ab4625ac603d52465ae4a0.md)です

1. user_idにKING OF TIME の ID を入力する
2. my_passにKING OF TIME の パスワード を入力する
3. range_startとrange_endの各項目を入力する（月初から月末の場合は変更なしでOK）
4. 事前準備で用意したApps ScriptのURLを入力する
5. ファイルを保存してターミナルを起動し、以下のコマンドを実行する
    
    ```python
    python working_hours.py
    ```
    
6. 自動的にGoogleChromeが起動し、スプレッドシートへの書き込みが行われる

---

### 実行ファイル

```python
# coding: UTF-8
import datetime
import calendar
import re
import requests
from selenium import webdriver
from selenium.webdriver.common.keys import Keys
from selenium.webdriver.chrome.options import Options
from selenium.webdriver.support import expected_conditions
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.common.by import By
from selenium.common.exceptions import NoSuchElementException
from webdriver_manager.chrome import ChromeDriverManager

# ********************個人設定********************

# KING OF TIMEのIDを入力する
user_id = ""

# KING OF TIMEのパスワードを入力する
my_pass = ""

# 取得範囲 ※1日から取得する場合は0を指定する
range_start = 0
# 末日を指定する
today = datetime.date.today()
lastday = calendar.monthrange(today.year, today.month)
range_end = lastday[1]

# Apps ScriptのURLを入力する
url = ""

# ********************コード********************

def getInputValue(path):
    """
    KING OF TIMEの勤務時間入力欄から値を取得する

    Parameters
    ----------
    path : str
        勤務時間入力欄のHTMLでのパス

    Returns
    -------
    inputValue : str
        勤務時間入力欄の値
    """
    return webdriver.find_element_by_xpath(path).text

def changeInputValue(input_value):
    """
    勤務時間入力欄の値を 00:00 の形式にする

    Parameters
    ----------
    input_value : str
        勤務時間入力欄の値

    Returns
    -------
    work_time : str
        00:00 形式の勤務時間
    """
    return re.sub("^\D*", "", input_value)

webdriver = webdriver.Chrome(ChromeDriverManager().install())
webdriver.get("https://s2.kingtime.jp/admin/yC6jmVwSsiEOPtg6kcE7uf3XZpZIi32KF")
# 画面表示まで待つ
element = WebDriverWait(webdriver, 10).until(
    expected_conditions.presence_of_element_located((By.XPATH, "/html/body/div/div[1]/div[1]/div/form/input[1]"))
)

webdriver.find_element_by_xpath("/html/body/div/div[1]/div[1]/div/form/input[1]").send_keys(user_id)
webdriver.find_element_by_xpath("/html/body/div/div[1]/div[1]/div/form/input[2]").send_keys(my_pass)
webdriver.find_element_by_xpath("/html/body/div/div[1]/div[1]/div/form/input[6]").click()

# ログイン完了まで待つ
element = WebDriverWait(webdriver, 10).until(
    expected_conditions.presence_of_element_located((By.XPATH, "/html/body/div/div[2]/div/div[5]/div[1]/table/tbody/tr"))
)

# 勤務時間を格納する（日付、スケジュール、出勤、退勤、休憩開始、休憩終了）
working_hours_list = {}

for i in range(range_start,range_end):
    day_xpath = "/html/body/div/div[2]/div/div[5]/div[1]/table/tbody/tr[" + str(i+1) +"]/td[2]/p"
    day_schedule_xpath = "/html/body/div/div[2]/div/div[5]/div[1]/table/tbody/tr[" + str(i+1) +"]/td[4]/p"
    start_work_xpath = "/html/body/div/div[2]/div/div[5]/div[1]/table/tbody/tr[" + str(i+1) + "]/td[6]/p"
    end_work_xpath = "/html/body/div/div[2]/div/div[5]/div[1]/table/tbody/tr[" + str(i+1) + "]/td[7]/p"
    start_break_xpath = "/html/body/div/div[2]/div/div[5]/div[1]/table/tbody/tr[" + str(i+1) + "]/td[8]/p"
    end_break_xpath = "/html/body/div/div[2]/div/div[5]/div[1]/table/tbody/tr[" + str(i+1) + "]/td[9]/p"
    
    try :
        day = getInputValue(day_xpath)
        day_schedule = getInputValue(day_schedule_xpath)
        start_work = getInputValue(start_work_xpath)
        end_work = getInputValue(end_work_xpath)
        start_break = getInputValue(start_break_xpath)
        end_break = getInputValue(end_break_xpath)

        working_hours_list[i] = {"day" : day, "day_schedule" : day_schedule}
        working_hours_list[i]["start_work"] = changeInputValue(start_work)
        working_hours_list[i]["end_work"] = changeInputValue(end_work)
        working_hours_list[i]["start_break"] = changeInputValue(start_break)
        working_hours_list[i]["end_break"] = changeInputValue(end_break)
    except :
        print("勤務時間の取得でエラーが発生しました。")
        break

webdriver.close()
webdriver.quit()

# スプレッドシートへの書き込み
for working_hour in working_hours_list.values():
    try:
        response = requests.post(url, data=working_hour)
        print(working_hour["day"] + "の入力が完了しました。")
    except:
        print("スプレッドシートへの書き込みでエラーが発生しました。")
        break
```

---

### 事前準備

1. GoogleChromeをインストールする
2. 勤務時間を反映させたいスプレッドシートを作成する
    - スプレッドシートの「拡張機能」から「Apps Script」を開く
        
        ![sheet_1.png](kot_%E5%8B%A4%E5%8B%99%E6%99%82%E9%96%93%E3%82%B9%E3%83%95%E3%82%9A%E3%83%AC%E3%83%83%E3%83%88%E3%82%99%E3%82%B7%E3%83%BC%E3%83%88%E5%8F%8D%E6%98%A0%20dc6660f080ab4625ac603d52465ae4a0/sheet_1.png)
        
    - 既存の myFunction() を削除し以下のコードを入力する
        
        ```jsx
        function doPost(e) {
        		// スプレッドシートへの書き込み
        		var id = "スプレッドシートID"
        		var ss = SpreadsheetApp.openById(id)
        		var spread = SpreadsheetApp.getActiveSpreadsheet();
        		var lastRow = spread.getSheets()[0].getLastRow();
        		spread.getSheets()[0].getRange((lastRow + 1), 1).setValue(e.parameter.day);
        		spread.getSheets()[0].getRange((lastRow + 1), 2).setValue(e.parameter.day_schedule);
        		spread.getSheets()[0].getRange((lastRow + 1), 3).setValue(e.parameter.start_work);
        		spread.getSheets()[0].getRange((lastRow + 1), 4).setValue(e.parameter.end_work);
        		spread.getSheets()[0].getRange((lastRow + 1), 5).setValue(e.parameter.start_break);
        		spread.getSheets()[0].getRange((lastRow + 1), 6).setValue(e.parameter.end_break);
        }
        ```
        
    - コード内の「スプレッドシートID」を2.で作成したスプレッドシートのIDに変更する
        - https://docs.google.com/spreadsheets/d/**[ここにIDが存在]**/edit
    - 変更が完了したら保存する
        
        ![sheet_2.png](kot_%E5%8B%A4%E5%8B%99%E6%99%82%E9%96%93%E3%82%B9%E3%83%95%E3%82%9A%E3%83%AC%E3%83%83%E3%83%88%E3%82%99%E3%82%B7%E3%83%BC%E3%83%88%E5%8F%8D%E6%98%A0%20dc6660f080ab4625ac603d52465ae4a0/sheet_2.png)
        
    - デプロイから新しいデプロイを選択する
        
        ![sheet_3.jpg](kot_%E5%8B%A4%E5%8B%99%E6%99%82%E9%96%93%E3%82%B9%E3%83%95%E3%82%9A%E3%83%AC%E3%83%83%E3%83%88%E3%82%99%E3%82%B7%E3%83%BC%E3%83%88%E5%8F%8D%E6%98%A0%20dc6660f080ab4625ac603d52465ae4a0/sheet_3.jpg)
        
    - 歯車マークからウェブアプリを選択し、「アクセスできるユーザー」を「全員」に変更してデプロイを押下する
        
        ![sheet_4.jpg](kot_%E5%8B%A4%E5%8B%99%E6%99%82%E9%96%93%E3%82%B9%E3%83%95%E3%82%9A%E3%83%AC%E3%83%83%E3%83%88%E3%82%99%E3%82%B7%E3%83%BC%E3%83%88%E5%8F%8D%E6%98%A0%20dc6660f080ab4625ac603d52465ae4a0/sheet_4.jpg)
        
    - 表示されたURLをコピーして「実行手順4」に入力する
        
        ![sheet_5.jpg](kot_%E5%8B%A4%E5%8B%99%E6%99%82%E9%96%93%E3%82%B9%E3%83%95%E3%82%9A%E3%83%AC%E3%83%83%E3%83%88%E3%82%99%E3%82%B7%E3%83%BC%E3%83%88%E5%8F%8D%E6%98%A0%20dc6660f080ab4625ac603d52465ae4a0/sheet_5.jpg)
        
3. 下記を参考にPython3系をインストールする
    
    ※[こちら](kot_auto%20362185788c974e65bd88983ea0c0aa7f.md)の資料も参考になります
    
    - windowsの場合（どちらかを参考にする）
        - [https://www.python.jp/install/windows/install.html](https://www.python.jp/install/windows/install.html)
        - [http://netsu-n.mep.titech.ac.jp/~Kawaguchi/python/install-win/](http://netsu-n.mep.titech.ac.jp/~Kawaguchi/python/install-win/)
    - macの場合
        - [http://netsu-n.mep.titech.ac.jp/~Kawaguchi/python/install-mac/](http://netsu-n.mep.titech.ac.jp/~Kawaguchi/python/install-mac/)
4. コマンドプロンプトで以下を実行する（1行ずつ実行する）
    
    ```bash
    pip install selenium
    pip install chromedriver-binary
    pip install webdriver_manager
    pip install requests
    ```