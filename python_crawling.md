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

#### 프로젝트 생성
```shell
$ scrapy startproject [프로젝트명]
```

- 실행 예

```shell
# scrapy startproject first
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

#### 아이템 설정하기

- 크롤링에서 가져온 페이지 데이터를 추출하여 저장할 항목들을 설정 : `items.py` 파일
> 참고 문서 : https://doc.scrapy.org/en/latest/topics/items.html

```python
import scrapy

class ScrapItem(scrapy.Item):
    # 아이탬을 아래와 같은 형식으로 하나씩 정의한다!
    name = scrapy.Field()
    pass
```

#### 스파이더

- 실제 정보를 수집할 스크랩하는 객체(?)

- 생성 : `scrapy genspider 스파이더명`

```shell
# scrapy genspider
Usage
=====
  scrapy genspider [options] <name> <domain>

Generate new spider using pre-defined templates

Options
=======
--help, -h              show this help message and exit
--list, -l              List available templates
--edit, -e              Edit spider after creating it
--dump=TEMPLATE, -d TEMPLATE
                        Dump template to standard output
--template=TEMPLATE, -t TEMPLATE
                        Uses a custom template.
--force                 If the spider already exists, overwrite it with the
                        template

Global Options
--------------
--logfile=FILE          log file. if omitted stderr will be used
--loglevel=LEVEL, -L LEVEL
                        log level (default: DEBUG)
--nolog                 disable logging completely
--profile=FILE          write python cProfile stats to FILE
--pidfile=FILE          write process ID to FILE
--set=NAME=VALUE, -s NAME=VALUE
                        set/override setting (may be repeated)
--pdb                   enable pdb on failure
```

- 템플릿 옵션 `-t`
  - basic : 기본적 크롤러. &lt;domain>에 설정한 페이지만 크롤링
  - crawl : 설정한 규칙에 맞는 링크를 재귀적으로 탐색하는 크롤링 (많이 사용됨)
  - xmlfeed : xml 피드 링크를 크롤링
  - csvfeed : csv 크롤링, xml과 비교해 행 단위로 클롤링을 진행

- 사용 예

```shell
# scrapy genspider -t crawl google google.com
Created spider 'google' using template 'crawl' in module:
  first.spiders.google
```

- 생성된 스파이더 내용 : `spiders/google.py`

```python
import scrapy
from scrapy.linkextractors import LinkExtractor
from scrapy.spiders import CrawlSpider, Rule

class GoogleSpider(CrawlSpider):
    name = 'google'
    allowed_domains = ['google.com']
    start_urls = ['http://google.com/']

    rules = (
        Rule(LinkExtractor(allow=r'Items/'), callback='parse_item', follow=True),
    )

    def parse_item(self, response):
        i = {}
        #i['domain_id'] = response.xpath('//input[@id="sid"]/@value').extract()
        #i['name'] = response.xpath('//div[@id="name"]').extract()
        #i['description'] = response.xpath('//div[@id="description"]').extract()
        return i
```

> 스파이더 규칙 설정 (+ XPath의 이해)

#### 크롤링 실행

```shell
$ scrapy crawl google -o google.csv -t csv
```

- 지원 파일 형식 : json, xml, csv 등등
