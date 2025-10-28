---
layout: post
title: selenium 스크롤 동작
description: >
  selenium 모듈에서 스크롤하는 동작 취하는 방법
  2가지의 방법이 사용됨
author: 김성훈
noindex: false
---

네이버에서 검색 결과를 추가로 불러오기 위해서 스크롤을 해야한다.
selenium 을 통해 생성된 driver에서 마우스 스크롤을 진행하는 메서드는 별도로 존재하지 않으며 대신 다른 방법으로 우회해야한다.

## 1안. execute_script 통한 스크롤

크롬창에서 `F12`을 눌러 개발자 도구를 켰을때 `console` 부분에 아래 javascript 명령어를 입력하면 크롬창의 창 스크롤을 옮길 수 있다.

`window.scrollTo(x, y)`

위 명령어에서 x는 가로 스크롤, y는 세로 스크롤을 의미한다.
x의 최대는 `document.body.scrollWidth`, y의 최대는 `document.body.scrollHeight`로 사용할 수 있다.

이 자바스크립트 코드를 driver에서 실행하기 위해서는 `execute_script`를 사용한다.

```python
from selenium import webdriver

driver = webdriver.Chrome()

keyword = "검색어"

search_url = f"https://search.naver.com/search.naver?query={keyword}"

driver.get(search_url)

driver.execute_script("window.scrollTo(0, document.body.scrollHeight);")
```

## 2안. move_to_element 을 통한 화면 이동

selenium에는 ActionChains라는 모듈이 있는데, 이를 사용하여 driver의 동작 중 더 복잡한 동작을 수행할 수 있도록 한다.

네이버 블로그 검색 결과에서 다음 결과를 로딩하기 위해 `div[class="api_load"]`로 이동을 하면 다음 검색 결과를 불러올 수 있다.

```python
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver import ActionChains

driver = webdriver.Chrome()

keyword = "검색어"

search_url = f"https://search.naver.com/search.naver?ssc=tab.blog.all&sm=tab_jum&query={keyword}"

driver.get(search_url)

more_results = driver.find_element(By.CSS_SELECTOR, 'div[class="api_load"]')

while True:
    action = ActionChains(driver)
    try: action.move_to_element(more_results).perform()
    except: continue
```

## 3안.send_keys를 통한 화면 이동

스크롤 대신 키보드의 page_down 혹은 화살표 아래 키를 누르는 것으로도 화면의 이동이 가능하다.
단, `webdriver.Chrome()` 클래스는 `send_keys` 메서드가 없으므로 `body` 태그로 우회한다.

```python
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.common.keys import Keys

driver = webdriver.Chrome()

keyword = "검색어"

search_url = f"https://search.naver.com/search.naver?ssc=tab.blog.all&sm=tab_jum&query={keyword}"

driver.get(search_url)

body = driver.find_element(By.TAG_NAME, "body")

body.send_keys(Keys.PAGE_DOWN)
```