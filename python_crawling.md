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

### 설치
```shell
$ pip install scrapy
$ scrapy
```
> 별도의 프로그램 처럼 쉘 명령어가 사용 가능해짐

- 결과 예
```shell
# scrapy
:0: UserWarning: You do not have a working installation of the service_identity module: 'cannot import name opentype'.  Please install it from <https://pypi.python.org/pypi/service_identity> and make sure all of its dependencies are satisfied.  Without the service_identity module, Twisted can perform only rudimentary TLS client hostname verification.  Many valid certificate/hostname mappings may be rejected.
Scrapy 1.5.0 - no active project

Usage:
  scrapy <command> [options] [args]

Available commands:
  bench         Run quick benchmark test
  fetch         Fetch a URL using the Scrapy downloader
  genspider     Generate new spider using pre-defined templates
  runspider     Run a self-contained spider (without creating a project)
  settings      Get settings values
  shell         Interactive scraping console
  startproject  Create new project
  version       Print Scrapy version
  view          Open URL in browser, as seen by Scrapy

  [ more ]      More commands available when run from project directory

Use "scrapy <command> -h" to see more info about a command
```

### 프로젝트 생성
```shell
$ scrapy startproject [프로젝트명]
```

- 실행 예

```shell
# scrapy startproject first
:0: UserWarning: You do not have a working installation of the service_identity module: 'cannot import name opentype'.  Please install it from <https://pypi.python.org/pypi/service_identity> and make sure all of its dependencies are satisfied.  Without the service_identity module, Twisted can perform only rudimentary TLS client hostname verification.  Many valid certificate/hostname mappings may be rejected.
New Scrapy project 'first', using template directory '/usr/lib64/python2.7/site-packages/scrapy/templates/project', created in:
    /home/denlyou/scrapy/first

You can start your first spider with:
    cd first
    scrapy genspider example example.com
```

- 기본 디렉터리

```shell
# tree
.
├── first
│   ├── __init__.py
│   ├── items.py
│   ├── middlewares.py
│   ├── pipelines.py
│   ├── settings.py
│   └── spiders
│       └── __init__.py
└── scrapy.cfg
```

- 아이템 설정하기

### 스파이더
