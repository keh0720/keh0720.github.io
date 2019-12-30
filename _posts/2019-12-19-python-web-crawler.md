---
title: 중고장터 알리미
layout: post
date: 2019-12-19
description: 웹 crawler 구현
categories: ["toyproject"]
---

# 개요
---

앱으로 제공되지 않는 사이트의 게시판에 원하는 키워드의 게시글이 올라올 경우 알림을 주는 프로그램

# 계획
---

- Python을 써볼까
- 알림은 Slack으로
- DB저장 없이 알림만 주고 Slack 메시지로 이력 관리
- target 사이트 : 클리앙 중고장터, 뽐뿌 중고장터

# Archithecture
---
![class_diagram](/img/2019-12-19-crawl-storealarm_class.png)
![seq_diagram](/img/2019-12-19-crawl-storealarm_seq.png)

# 구현
---
## 타겟 사이트 DOM 분석
클리앙 중고장터 크롤링을 위해서 DOM을 분석해야한다. list_content div 안에 list_item이 하나의 글로 구성되어 있다.
이 중 필요한 항목은 제목, 카테고리, 시간, 작성자, 게시글 링크이다.

|항목|Selector|property|
|:-------------|:------------------|:---------------|
|게시글 리스트|	.list_content > .list_item	||
|제목|	.list_content > .list_item > div.list_title > a > .subject_fixed	||
|카테고리|	.list_content > .list_item > div.list_title > a > .category_fixed	||
|시간|	.list_content > .list_item > .list_time > span > span	||
|작성자|	.list_content > .list_item| 	data-author-id |
|게시글 링크|	.list_content > .list_item > .list_title a|	href |

```html
<div class="list_content">
    <div class="list_item symph_row  " data-role="list-row" data-author-id="sowol" data-board-sn="14434495" data-comment-count="0">
        <span class="list_stare" style="display: none;" data-role="observeIcon"><i class="fa fa-eye"></i></span>
        <div class="list_symph view_symph lSymph01" data-role="list-like-count"><span>0</span></div>
        <div class="list_title" data-role="list-title" data-toggle-custom="dropdown">
            <a class="list_subject" href="/service/board/sold/14434495?od=T31&po=0&category=&groupCd=" data-role="cut-string">
                <span class="category_fixed" title="교환">교환</span>
                <span class="subject_fixed" data-role="list-title-text" title="스타벅스 프리퀀시 빨강2개을 흰색4개로 교환하고자 합니다">스타벅스 프리퀀시 빨강2개을 흰색4개로 교환하고자 합니다</span>
            </a>
        </div>
        <div class="list_author" data-role="list-author">
            <span class="memo none" data-role="list_memo"></span>
            <span class="nickname">
                <span>패드군</span>
            </span>
        </div>
        <div class="list_hit">
            <span class="hit">1</span>
        </div>
        <div class="list_time">
            <span class="time popover">
                15:31
                <span class="timestamp">2019-12-27 15:31:24</span>
            </span>
        </div>
    </div>
    <div class="list_item symph_row  " data-role="list-row" data-author-id="ghiwan" data-board-sn="14434493" data-comment-count="0">
        <span class="list_stare" style="display: none;" data-role="observeIcon"><i class="fa fa-eye"></i></span>
        <div class="list_symph view_symph lSymph01" data-role="list-like-count"><span>0</span></div>
        <div class="list_title" data-role="list-title" data-toggle-custom="dropdown">
            <a class="list_subject" href="/service/board/sold/14434493?od=T31&po=0&category=&groupCd=" data-role="cut-string">
                <span class="category_fixed" title="판매">판매</span>
                <span class="subject_fixed" data-role="list-title-text" title="에어팟2 팝니다.">에어팟2 팝니다.</span>
            </a>  
            <span class="icon_pic fa fa-picture-o"></span>
        </div>
        <div class="list_author" data-role="list-author">
            <span class="memo none" data-role="list_memo"></span>
            <span class="nickname">
                <span>이안프로</span>
            </span>
            </div>
        <div class="list_hit">
            <span class="hit">4</span>
        </div>
        <div class="list_time">
            <span class="time popover">
                15:31
                <span class="timestamp">2019-12-27 15:31:01</span>
            </span>
        </div>
    </div>
.
.
.
</div>
```

## Crawling
* BeautifulSoup 라이브러리 이용
* select 함수 이용하여 DOM 객체 획득. ResultSet으로 리턴되기 때문에 이에 대한 처리가 필요하다.
* StoreCrawler.py

```python
import requests
import StoreLogger
from bs4 import BeautifulSoup
from StoreItem import StoreItem
 
log = StoreLogger.log
 
 
def get_store_data(site, url, start, end):
    list_store_item = []
 
    for i in range(start, end):
        req = requests.get(url + str(i))
 
        html = req.text
 
        soup = BeautifulSoup(html, 'html.parser')
        list_content = soup.select('.list_content > .list_item')
 
        for item in list_content:
            status = item.select('div.list_title > a > .category_fixed')[0].text
            subject = item.select('div.list_title > a > span.subject_fixed')[0].text
            item_url = item.select('div.list_title > a')[0].get('href')
            author = item.get('data-author-id')
            time = item.select('.list_time > span > span')[0].text
 
            store_item = StoreItem(time, site, subject, author, status, item_url)
            list_store_item.append(store_item)
 
            log.debug("crawled item : " + store_item.to_string())
 
    return list_store_item
```

* 크롤링한 데이터를 재활용하기 편하도록 클래스를 구성했다. 초기 구현 단계에서는 크게 쓸 일은 없었지만 추후 확장성을 위해 두었다.
* StoreItem.py

```python
class StoreItem:
    def __init__(self, time, site, subject, author, status, url):
        self.time = time
        self.site = site
        self.subject = subject
        self.author = author
        self.status = status
        self.url = url
 
    def to_string(self):
        item = ""
        if self.time:
            item += self.time + " "
        if self.site:
            item += self.site + " "
        if self.subject:
            item += self.subject + " "
        if self.author:
            item += self.author + " "
        if self.status:
            item += self.status + " "
        if self.url:
            item += self.url
 
        return item
```

* 크롤링 성공

```
crawled item : 2019-12-30 14:32:10/clien/에어팟 프로 미개봉/josw0326/판매//service/board/sold/14443763?od=T31&po=0&category=&groupCd=
crawled item : 2019-12-30 14:32:03/clien/아이패드 미니5 와이파이 256기가 팝니다. ( 스그 )/sunshine6108/판매//service/board/sold/14443762?od=T31&po=0&category=&groupCd=
crawled item : 2019-12-30 14:31:39/clien/스타벅스 프리퀀시 빨강 3개 구입원합니다./symantec/구입//service/board/sold/14443757?od=T31&po=0&category=&groupCd=
crawled item : 2019-12-30 14:30:57/clien/킨들 오아시스 9세대 32g 3g + 알리발 커버케이스/neuropsy00/판매//service/board/sold/14443755?od=T31&po=0&category=&groupCd=
crawled item : 2019-12-30 14:30:09/clien/스타벅스 프리퀀시 완성본 팝니다/fenrir/판매//service/board/sold/14443751?od=T31&po=0&category=&groupCd=
crawled item : 2019-12-30 14:30:03/clien/롯데시네마 스위트콤보(팝콘L+탄산음료2)/콤보4천원 할인권/팝콘(중) 판매합니다./oddysey99/판매//service/board/sold/14443749?od=T31&po=0&category=&groupCd=
crawled item : 2019-12-30 14:29:01/clien/갤럭시s 10 5g 와 갤럭시탭a8.0으로 노트10+로 교환원합니다./chunggiki/교환//service/board/sold/14443746?od=T31&po=0&category=&groupCd=
```

## Slack으로 메시지 전송
* WebHook 기능을 이용하면 간단하게 slack으로 메시지를 보낼 수 있다.
* Slack workspace 생성 후 admin 페이지에서 Configure Apps 메뉴를 선택하면 webhoop app을 사용 할 수 있다.
![webhook_app](/img/2019-12-19-crawl-app.png){:width="100%" height="100%"}
* Incoming WebHook을 선택하고 Add to Slack 버튼을 클릭하면 사용 할 수 있게 된다.
* 사용법은 간단하다. WebHook URL로 정해진 포맷에 맞게 POST request를 보내면 Slack으로 메시지가 전송 된다.
* Postman으로 테스트 결과 메시지가 잘 전송된다.
![postman](/img/2019-12-19-crawl-postman.png){:width="100%" height="100%"}
* 상세설정으로 WebHook을 받았을 때 메시지를 게시한 채널과 이름, 아이콘 등을 설정 할 수 있다.
![seq_diagram](/img/2019-12-19-crawl-webhook_config.png){:width="100%" height="100%"}
* POST request만 보내면 되기 때문에 간단하게 requests 모듈을 이용하여 구현했다. 
* AlarmSender.py

```python
import requests
 
 
class AlarmSender:
    def __init__(self, url, body):
        self.url = url
        self.body = body
        self.header = {'Content-Type': 'application/json; charset=utf-8'}
 
    def set_url(self, url):
        self.url = url
 
    def set_body(self, body):
        self.body = body
 
    def send(self):
        res = requests.post(self.url, headers=self.header, data=self.body)
        return res
```

- 클리앙의 경우 약30분정도마다 1페이지의 글이 올라온다. 이는 유동적이기 때문에 우선 1분에 2페이지씩 크롤링하도록 한다. 이 과정에서 중복되는 글이 발생하기 때문에 이에 대한 처리가 필요하다.
  *-* 1분마다 크롤링 해 온 데이터에서 원하는 키워드의 글이 있을 경우 Slack으로 전송하고, 성공적으로 전송했을 경우 내부 DB에 저장한다.
  - sqlite를 이용하여 내부 DB를 구성하고, Slack으로 메시지를 보내기 전에 DB를 확인해서 중복된 내용이 있을 경우 메시지를 보내지 않는다.
  - 구동 시 테이블이 존재하지 않을 경우 생성한다. Insert, Select, Delete를 래핑하여 확정 할 경우를 대비했다. 현재는 데이터가 계속 쌓이기만 하기 때문에 추후 삭제하는 부분도 추가해야 한다.
  - DataStorer.py

```python
import sqlite3 as sql
import StoreLogger
 
log = StoreLogger.log
 
 
class DataStorer:
    def __init__(self):
        print("DataStorer init")
        self.conn = sql.connect('store_data')
        self.cursor = self.conn.cursor()
        self.cursor.execute('''CREATE TABLE IF NOT EXISTS store_data
                                (time text , site text, subject text,
                                author text, status text, url test, isAlarm bool)''')
        store_data = self.cursor.execute("SELECT * FROM store_data")
        for item in store_data:
            log.debug("Sended item " + item[0] + "/" + item[1] + "/" + item[2] + "/" + item[3] +
                      "/" + item[4] + "/" + item[5])
 
    def insert(self, target, data):
        for item in data:
            self.cursor.execute("INSERT INTO store_data VALUES (?,?,?,?,?,?,?)",
                                (item.time, target, item.subject, item.author, item.status, item.url, False))
            log.debug("insert item " + item.time + "/" + target + "/" + item.subject + "/" + item.author + "/" +
                      item.status + "/" + item.url + "/" + str(False))
            self.conn.commit()
 
    def select_all(self):
        store_data = self.cursor.execute("SELECT time, site, subject, author FROM store_data")
        list_store_data = {}
        for item in store_data:
            list_store_data[item[0] + '/' + item[1] + '/' + item[3]] = item[2]
        return list_store_data
 
    def update_status(self, time, site):
        self.cursor.execute("UPDATE store_data SET isAlarm = 1 WHERE time=? AND site=?", (time, site))
        log.debug("update status " + time + "/" + site)
        self.conn.commit()
 
    def __del__(self):
        print("DataStorer destroy")
        self.cursor.close()
        self.conn.close()
```

## 1차 구현 완료
- 실행 시 원하는 키워드를 파라미터로 입력하도록 하여 1분마다 크롤링을 수행한다.
- Slack으로 전송 시 포스팅 된 메시지에 링크를 걸 수 있어 클릭하면 바로 게시글로 이동한다.
![slack](/img/2019-12-19-crawl-slack.png){:width="100%" height="100%"}
- StoreAlarm.py

```python
import errno
import sys
import time
import json
 
import StoreCrawler
import StoreLogger
from AlarmSender import AlarmSender
from DataStorer import DataStorer
 
log = StoreLogger.log
 
try:
    if sys.argv[1]:
        keyword = sys.argv[1]
except IndexError:
    log.error("please input keyword")
    sys.exit(errno.EINVAL)
 
log.info("Started StoreAlarm application")
 
url_clien = "https://www.clien.net"
url_clien_used = "https://www.clien.net/service/board/sold?&od=T31&po="
url_slack = "https://hooks.slack.com/services/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"
site_clien = "clien"
isPage = True
 
storer = DataStorer()
 
while True:
    clien = StoreCrawler.get_store_data(site_clien, url_clien_used, 0, 2)
    data = storer.select_all()
 
    for item in clien:
        if not data.get(item.time + '/' + item.site + '/' + item.author):
            if keyword in item.subject:
                storer.insert(site_clien, [item])
 
                str_body = item.site + " / " + item.time + " / [" + item.status + \
                    "] <" + url_clien + item.url + "|" + item.subject + ">"
                body = {"text": str_body}
                sender = AlarmSender(url_slack, json.dumps(body).encode('utf-8'))
                res = sender.send()
                log.debug("Found new item : " + item.subject)
                if res.status_code == 200:
                    log.debug("Success send message to Slack")
                    storer.update_status(item.time, item.site)
 
    time.sleep(60)
```
