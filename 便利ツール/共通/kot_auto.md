# kot_auto

notionでソースコードの行数出したい場合はこちらを参照：[https://br.atsit.in/ja/?p=98112](https://br.atsit.in/ja/?p=98112)

chromedriver_binaryのインストールはこれ？：[https://qiita.com/hanzawak/items/2ab4d2a333d6be6ac760](https://qiita.com/hanzawak/items/2ab4d2a333d6be6ac760)

pip3 install --upgrade pip

>chromedriver_binaryとwebdriver_manager入れると自動更新動くようになりました。

pip install chromedriver-binary

pip install webdriver_manager

```python
# coding: UTF-8
from selenium import webdriver
from selenium.webdriver.common.keys import Keys
from selenium.webdriver.chrome.options import Options
from selenium.webdriver.support import expected_conditions
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.common.by import By
from selenium.common.exceptions import NoSuchElementException
#エラーが起きたら下記「chromedriver_binary」「webdriver_manager」インストールしてください
import chromedriver_binary
from webdriver_manager.chrome import ChromeDriverManager
import time

# ********************　必読　********************

# ■　■　■　■　■　■　■　前提条件 ■　■　■　■　■　■　■　

# Python3がインストール済みであること
# ブラウザ「Google Chrome」がインストール済みであること

# コマンドラインで以下をインストール
#             pip install selenium
# brewをアップデート
# 　　　　　　　brew tap homebrew/cask
# chromedriverをインストール
# 　　　　　　　brew install chromedriver

# ■　■　■　■　■　■　■　手順 ■　■　■　■　■　■　■　

# 1:user_idにKING OF TIME の ID を入力

# 2:my_passにKING OF TIME の パスワード を入力

# 3:time_start_workからtime_end_restまで各項目を入力

# 4:ファイルを保存してターミナルを起動

# 5:python kot_auto.py　入力

# 6:自動的にchromeが起動される
# ※macはこのスクリプトの初回起動時にシステム環境設定＞セキュリティより許可が必要

# ********************個人設定********************

# KING OF TIME の ID を入力
user_id = ""

# KING OF TIME の パスワード を入力
my_pass = ""

# 勤務開始時間を入力 (10時なら1000),(9:30なら930)
time_start_work = 1000
# 勤務終了時間を入力 (19時なら1900),(18:30なら1830)
time_end_work = 1900

# 休憩開始時間を入力
time_start_rest = 1230
# 休憩終了時間を入力
time_end_rest = 1330

# 入力範囲 ※1日から入力する場合は0
range_start = 0
# 末日 ※31日の次に最終日文言を出したければ32
range_end = 31

# ********************コード********************

webdriver = webdriver.Chrome(ChromeDriverManager().install())
#webdriver = webdriver.Chrome()
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
    expected_conditions.presence_of_element_located((By.XPATH, "/html/body/div/div[2]/div/div[7]/div[1]/table/tbody/tr"))
)

def inputWorkTime():
    #入力画面表示まで待つ
    element = WebDriverWait(webdriver, 10).until(
        expected_conditions.presence_of_element_located((By.XPATH, "//*[@id=\"recording_type_code_1\"]"))
    )

    webdriver.find_element_by_xpath("//*[@id=\"recording_type_code_1\"]").click()
    webdriver.find_element_by_xpath("//*[@id=\"recording_type_code_1\"]/option[2]").click()
    webdriver.find_element_by_xpath("//*[@id=\"recording_timestamp_time_1\"]").send_keys(time_start_work)
    webdriver.find_element_by_xpath("//*[@id=\"recording_type_code_2\"]").click()
    webdriver.find_element_by_xpath("//*[@id=\"recording_type_code_2\"]/option[3]").click()
    webdriver.find_element_by_xpath("//*[@id=\"recording_timestamp_time_2\"]").send_keys(time_end_work)
    webdriver.find_element_by_xpath("//*[@id=\"recording_type_code_3\"]").click()
    webdriver.find_element_by_xpath("//*[@id=\"recording_type_code_3\"]/option[4]").click()
    webdriver.find_element_by_xpath("//*[@id=\"recording_timestamp_time_3\"]").send_keys(time_start_rest)
    webdriver.find_element_by_xpath("//*[@id=\"recording_type_code_4\"]").click()
    webdriver.find_element_by_xpath("//*[@id=\"recording_type_code_4\"]/option[5]").click()
    webdriver.find_element_by_xpath("//*[@id=\"recording_timestamp_time_4\"]").send_keys(time_end_rest)
    webdriver.find_element_by_xpath("//*[@id=\"recording_timestamp_time_4\"]").send_keys(Keys.ENTER)

for i in range(range_start,range_end):
    day_type_xpath = "/html/body/div/div[2]/div/div[7]/div[1]/table/tbody/tr[" + str(i+1) +"]"
    day_schedule_xpath = "/html/body/div/div[2]/div/div[7]/div[1]/table/tbody/tr[" + str(i+1) +"]/td[4]/p"
    day_xpath = "/html/body/div/div[2]/div/div[7]/div[1]/table/tbody/tr[" + str(i+1) +"]/td[2]/p"
    td6e_xpath = "/html/body/div/div[2]/div/div[7]/div[1]/table/tbody/tr[" + str(i+1) + "]/td[6]"
    td6_xpath = "/html/body/div/div[2]/div/div[7]/div[1]/table/tbody/tr[" + str(i+1) + "]/td[6]/p"
    point_time_xpath = "/html/body/div/div[2]/div/div[7]/div[1]/table/tbody/tr[" + str(i+1) +"]/td[1]/p/select/option[2]"
    
    try :
        day_type = webdriver.find_element_by_xpath(day_type_xpath).text
        day_schedule = webdriver.find_element_by_xpath(day_schedule_xpath).text
        day = webdriver.find_element_by_xpath(day_xpath).text
        td6e = webdriver.find_element_by_xpath(td6e_xpath).get_attribute("title")
        td6 = webdriver.find_element_by_xpath(td6_xpath).text
    except NoSuchElementException:
        print("最終日まで入力を完了しました。")
        break
    except :
        print("想定外のエラーが発生しました。")
        break
    if day not in ["土","日"] :
        if "エラー" in td6e:
            webdriver.find_element_by_xpath(point_time_xpath).click()
            inputWorkTime()
            # 一覧表示に戻るまで待つ
            element = WebDriverWait(webdriver, 10).until(
                expected_conditions.presence_of_element_located((By.XPATH, "/html/body/div/div[2]/div/div[7]/div[1]/table/tbody/tr"))
            )
            print(str(i+1)+"日に打刻を行いました")
        elif  "編" in td6:
            print(str(i+1)+ "日のレコードは編集済みです")
        elif "計画有休(全日)" in day_type:
            print(str(i+1) + "日は計画有休(全日)なので打刻しません")
        elif "--" != day_schedule and "平日" in day_type:
            print(str(i+1) + "日はスケジュール申請があるため打刻しません")
        elif "平日" in day_type:
            webdriver.find_element_by_xpath(point_time_xpath).click()
            inputWorkTime()
            # 一覧表示に戻るまで待つ
            element = WebDriverWait(webdriver, 10).until(
                expected_conditions.presence_of_element_located((By.XPATH, "/html/body/div/div[2]/div/div[7]/div[1]/table/tbody/tr"))
            )
            print(str(i+1)+"日に打刻を行いました")
        else :
            print(str(i+1) + "日は法定休日なので打刻しません")

webdriver.close()
webdriver.quit()
```

土日の回避

勤務日種別空欄の場合の対応

errorになる場合は下記インストール

jpholiday：日本の祝日判定ライブラリ

pip install jpholiday

```python
# coding: UTF-8
from selenium import webdriver
from selenium.webdriver.common.keys import Keys
from selenium.webdriver.chrome.options import Options
from selenium.webdriver.support import expected_conditions
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.common.by import By
from selenium.common.exceptions import NoSuchElementException
# エラーが起きたら下記「chromedriver_binary」「webdriver_manager」インストールしてください
import chromedriver_binary
from webdriver_manager.chrome import ChromeDriverManager
import time
#20200221 Add
import re
import jpholiday
import datetime

# ********************　必読　********************

# ■　■　■　■　■　■　■　前提条件 ■　■　■　■　■　■　■　

# Python3がインストール済みであること
# ブラウザ「Google Chrome」がインストール済みであること

# コマンドラインで以下をインストール
#             pip install selenium
# brewをアップデート
# 　　　　　　　brew tap homebrew/cask
# chromedriverをインストール
# 　　　　　　　brew install chromedriver

# ■　■　■　■　■　■　■　手順 ■　■　■　■　■　■　■　

# 1:user_idにKING OF TIME の ID を入力

# 2:my_passにKING OF TIME の パスワード を入力

# 3:time_start_workからtime_end_restまで各項目を入力

# 4:ファイルを保存してターミナルを起動

# 5:python kot_auto.py　入力

# 6:自動的にchromeが起動される
# ※macはこのスクリプトの初回起動時にシステム環境設定＞セキュリティより許可が必要

# ********************個人ごとに入力が必要********************

# KING OF TIME の ID を入力
user_id = ""

# KING OF TIME の パスワード を入力
my_pass = ""

# 勤務開始時間を入力 (10時なら1000),(9:30なら930)
time_start_work = 1000
# 勤務終了時間を入力 (19時なら1900),(18:30なら1830)
time_end_work = 1900

# 休憩開始時間を入力
time_start_rest = 1230
# 休憩終了時間を入力
time_end_rest = 1330

# 入力範囲 ※1日から入力する場合は0
range_start = 0
# 末日 ※31日の次に最終日文言を出したければ32
range_end = 31

# ********************以下設定不要********************

# ********************コード********************
# 20200221 Add start
# 平日参照用
weekday_pattern = re.compile(r'月|火|水|木|金')
# 休日参照用
holiday_pattern = re.compile(r'土|日')
# 年・月取得
currentDateTime = datetime.datetime.now()
date = datetime.date.today()
year = date.year
month = date.month
print(year)
print(month)
# 20200221 Add End

webdriver = webdriver.Chrome(ChromeDriverManager().install())
# webdriver = webdriver.Chrome()
webdriver.get("https://s2.kingtime.jp/admin/yC6jmVwSsiEOPtg6kcE7uf3XZpZIi32KF")
# 画面表示まで待つ
element = WebDriverWait(webdriver, 10).until(
    expected_conditions.presence_of_element_located(
        (By.XPATH, "/html/body/div/div[1]/div[1]/div/form/input[1]"))
)

webdriver.find_element_by_xpath(
    "/html/body/div/div[1]/div[1]/div/form/input[1]").send_keys(user_id)
webdriver.find_element_by_xpath(
    "/html/body/div/div[1]/div[1]/div/form/input[2]").send_keys(my_pass)
webdriver.find_element_by_xpath(
    "/html/body/div/div[1]/div[1]/div/form/input[6]").click()

# ログイン完了まで待つ
element = WebDriverWait(webdriver, 10).until(
    expected_conditions.presence_of_element_located(
        (By.XPATH, "/html/body/div/div[2]/div/div[7]/div[1]/table/tbody/tr"))
)

def inputWorkTime():
    # 入力画面表示まで待つ
    element = WebDriverWait(webdriver, 10).until(
        expected_conditions.presence_of_element_located(
            (By.XPATH, "//*[@id=\"recording_type_code_1\"]"))
    )
　  webdriver.find_element_by_xpath(
        "//*[@id=\"recording_type_code_1\"]").click()
    webdriver.find_element_by_xpath(
        "//*[@id=\"recording_type_code_1\"]/option[2]").click()
    webdriver.find_element_by_xpath(
        "//*[@id=\"recording_timestamp_time_1\"]").send_keys(time_start_work)
    webdriver.find_element_by_xpath(
        "//*[@id=\"recording_type_code_2\"]").click()
    webdriver.find_element_by_xpath(
        "//*[@id=\"recording_type_code_2\"]/option[3]").click()
    webdriver.find_element_by_xpath(
        "//*[@id=\"recording_timestamp_time_2\"]").send_keys(time_end_work)
    webdriver.find_element_by_xpath(
        "//*[@id=\"recording_type_code_3\"]").click()
    webdriver.find_element_by_xpath(
        "//*[@id=\"recording_type_code_3\"]/option[4]").click()
    webdriver.find_element_by_xpath(
        "//*[@id=\"recording_timestamp_time_3\"]").send_keys(time_start_rest)
    webdriver.find_element_by_xpath(
        "//*[@id=\"recording_type_code_4\"]").click()
    webdriver.find_element_by_xpath(
        "//*[@id=\"recording_type_code_4\"]/option[5]").click()
    webdriver.find_element_by_xpath(
        "//*[@id=\"recording_timestamp_time_4\"]").send_keys(time_end_rest)
    webdriver.find_element_by_xpath(
        "//*[@id=\"recording_timestamp_time_4\"]").send_keys(Keys.ENTER)

for i in range(range_start, range_end):
    day_type_xpath = "/html/body/div/div[2]/div/div[7]/div[1]/table/tbody/tr[" + str(
        i+1) + "]"
    day_schedule_xpath = "/html/body/div/div[2]/div/div[7]/div[1]/table/tbody/tr[" + str(
        i+1) + "]/td[4]/p"
    day_xpath = "/html/body/div/div[2]/div/div[7]/div[1]/table/tbody/tr[" + str(
        i+1) + "]/td[2]/p"
    td6e_xpath = "/html/body/div/div[2]/div/div[7]/div[1]/table/tbody/tr[" + str(
        i+1) + "]/td[6]"
    td6_xpath = "/html/body/div/div[2]/div/div[7]/div[1]/table/tbody/tr[" + str(
        i+1) + "]/td[6]/p"
    point_time_xpath = "/html/body/div/div[2]/div/div[7]/div[1]/table/tbody/tr[" + str(
        i+1) + "]/td[1]/p/select/option[2]"

    try:
        day_type = webdriver.find_element_by_xpath(day_type_xpath).text
        day_schedule = webdriver.find_element_by_xpath(day_schedule_xpath).text
        day = webdriver.find_element_by_xpath(day_xpath).text
        td6e = webdriver.find_element_by_xpath(
            td6e_xpath).get_attribute("title")
        td6 = webdriver.find_element_by_xpath(td6_xpath).text
    except NoSuchElementException:
        print("最終日まで入力を完了しました。")
        break
    except:
        print("想定外のエラーが発生しました。")
        break
    # 20200221 Add Start
    # 休日判定
    if not holiday_pattern.search(day):
        # 20200221 Add End
        if "エラー" in td6e:
            webdriver.find_element_by_xpath(point_time_xpath).click()
            inputWorkTime()
            # 一覧表示に戻るまで待つ
            element = WebDriverWait(webdriver, 10).until(
                expected_conditions.presence_of_element_located(
                    (By.XPATH, "/html/body/div/div[2]/div/div[7]/div[1]/table/tbody/tr"))
            )
            print(str(i+1)+"日に打刻を行いました")
        # 20200221 Add Start
        # 祝日判定
        elif jpholiday.is_holiday_name(datetime.date(year, month, (i+1))) != None:
            print(str(i+1) + "日は法定休日なので打刻しません")
        # 20200221 Add End
        elif "編" in td6:
            print(str(i+1) + "日のレコードは編集済みです")
        elif "計画有休(全日)" in day_type:
            print(str(i+1) + "日は計画有休(全日)なので打刻しません")
        elif "--" != day_schedule and "平日" in day_type:
            print(str(i+1) + "日はスケジュール申請があるため打刻しません")
        elif "平日" in day_type:
            webdriver.find_element_by_xpath(point_time_xpath).click()
            inputWorkTime()
            # 一覧表示に戻るまで待つ
            element = WebDriverWait(webdriver, 10).until(
                expected_conditions.presence_of_element_located(
                    (By.XPATH, "/html/body/div/div[2]/div/div[7]/div[1]/table/tbody/tr"))
            )
            print(str(i+1)+"日に打刻を行いました")
        # 20200221 Add Start
        elif "" == day_schedule and weekday_pattern.search(day):
            webdriver.find_element_by_xpath(point_time_xpath).click()
            inputWorkTime()
            # 一覧表示に戻るまで待つ
            element = WebDriverWait(webdriver, 10).until(
                expected_conditions.presence_of_element_located(
                    (By.XPATH, "/html/body/div/div[2]/div/div[7]/div[1]/table/tbody/tr"))
            )
            print(str(i+1)+"日に打刻を行いました")
        # 20200221 Add End
        else:
            print(str(i+1) + "日は法定休日なので打刻しません")
    # 20200221 Add Start
    else:
        if "土" in day:
            print(str(i+1) + "日は土曜日なので打刻しません")
        elif "日" in day:
            print(str(i+1) + "日は日曜日なので打刻しません")
    # 20200221 Add End

webdriver.close()
webdriver.quit()
```