---
layout: ost
title: request와 beautifulsoup
description: >
  동작이 상대적으로 느린 selenium 대신 사용할 수 있는 속도가 빠른 방법
author: 김성훈
noindex: false
---

selenium은 직접 인터넷 브라우저를 열어 원하는 페이지의 내용을 추출할 수 있다.
하지만, 리소스 로드 등의 동작으로 인해 실제 시간이 약간 소요되는 단점이 존재한다.

## selenium의 title 출력 시간 분석

```python
from selenium import webdriver
from selenium.webdriver.chrome.options import Options
import time

start = time.time()

options = Options()
options.add_argument("--disable-gpu")
driver = webdriver.Chrome(options=options)
driver.get("https://naver.com")
print(driver.title)
driver.quit()

end = time.time()
print("time:", end - start) # time: 3.6629281044006348
```

# requests와 BeautifulSoup

requests 모듈은 URL로부터 GET 요청 혹은 POST 요청을 하고 해당 결과를 받는 모듈이다.

requests를 통해 받은 응답을 BeautifulSoup를 사용하여 soup로 전환하여 selenium과 비슷한 동작(요소의 내용 추출에 한하여)을 취할 수 있다.

```python
import requests 
from bs4 import BeautifulSoup

start = time.time()
response = requests.get("https://naver.com")
response.raise_for_status()
soup = BeautifulSoup(response.text, "html.parser")
print(soup.title.string)

end = time.time()
print("time:", end - start) # time: 0.20133614540100098
```

# BeautifulSoup 상세

selenium 과 다르게 BeautifulSoup에서는 send_keys와 같은 상호 동작은 취할 수 없으나, select와 같은 동작으로 요소를 추출하는 것은 가능하다.

```python
from bs4 import BeautifulSoup
import requests

url = "https://dhlottery.co.kr/common.do?method=main"
response = requests.get(url)

soup = BeautifulSoup(response.text, 'html.parser')

# 1등 조 추출
group = soup.select_one('div[class="group"] span[class="num"]')
print("1등 조:", group.get_text())

# 1등 조 번호 출력
numbers = soup.select('span[class*="num al720_color"]')

# 1 ~ 6 번째 숫자는 당첨
win_numbers = [num.get_text() for num in numbers[:6]]
print("당첨 번호:", ''.join(win_numbers))

# 보너스 번호
bonus_numbers = [num.get_text() for num in numbers[-6:]]
print("보너스 번호:", ''.join(bonus_numbers))
```