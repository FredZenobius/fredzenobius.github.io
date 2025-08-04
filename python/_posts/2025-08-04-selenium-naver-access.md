---
layout: page
title: selenium 가상 드라이버 열기
description: >
  selenium 모듈로 웹 드라이버를 열고 네이버로 이동
author: 김성훈
noindex: true
---

selenium 모듈을 사용하면 크롬, 파이어폭스, 엣지 등의 웹 드라이버를 자동으로 실행, 제어할 수 있음

```bash
pip install selenium
```

```python
# selenium 모듈로 크롬 드라이버에서 네이버로 접속하는 코드
from selenium import webdriver

driver = webdriver.Chrome() # 드라이버 초기화

driver.get("https://naver.com")
```