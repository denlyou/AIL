# 파이썬을 이용한 크롤링

## 기본 패키지로 파일 저장

```py
import urllib.request

url = "http://abcds.kr"

html = urllib.request.urlopen(url).read()
with open("abcds_kr.html", "wb+") as f:
    f.write(html)
```

## Beautiful Soup
- html을 dom을 객체 단위로 변환해서 접근 가능한 패키지
- 패키지 설치 필요 : `pip3 install beautifulsoup4`
- [공식 문서](https://www.crummy.com/software/BeautifulSoup/bs4/doc/)
> https://godoftyping.wordpress.com/2017/06/24/python-beautifulsoup/

## [scrapy](https://scrapy.org/)
- 패키지 설치 필요 : `pip3 install scrapy`
- [공식문서](https://docs.scrapy.org/en/latest/)
