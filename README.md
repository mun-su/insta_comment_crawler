# 설정
## chrome driver
사용중인 환경의 크롬 브라우저와 동일한 버전의 driver 세팅 필요.

다운로드 주소 : [ChromeDriver](https://chromedriver.chromium.org/downloads)

해당 버전을 찾아 상세 링크로 이동시 아래와 같은 파일 목록 확인 가능
- chromedriver_linux64.zip : linux 계열
- chromedriver_mac64.zip : intel mac
- chromedriver_mac_arm64.zip : arm mac (m1, m2)
- chromedriver_win32.zip : windows

위의 system, platform, processor에 맞는 파일을 다운로드.

압축을 해제하고 `./bin` directory에 추가하여 `chromedriver`로 이름 변경 필요하며

windows는 `chromedriver.exe`로 변경 해주세요.

## crawler.yaml 파일의 값 변경
- instagram.login.username : 인스타그램 접속할 아이디
- instagram.login.password : 인스타그램 접속할 비밀번호
- collect.items.post.recentCount : 수집할 최근 post 개수
- collect.items.comment.recentCount : 수집할 최근 comment 개수
- collect.profiles : 수집할 profiles

## 추가 옵션
### headless 옵션
/core/chrome_driver.py 의 __load_chrome_option function에서
```python
# chrome_options.add_argument("--headless")
```
크롤링은 기본적으로 브라우저가 보이지 않는 형태로 진행되며
해당 옵션의 앞에 "#" 을 붙여 주석 처리하는 경우, 브라우저 보이는 형태로 진행.  

# 환경
- python 3
- pip

# 실행 방법
```shell
pip install -r requirements.txt
```
의존성 설치가 정상적이지 않은 경우

```shell
pip install async-generator
pip install attrs
pip install beautifulsoup4
pip install certifi
pip install charset-normalizer
pip install decorator
pip install exceptiongroup
pip install fake-useragent
pip install h11
pip install html5lib
pip install idna
pip install lxml
pip install numpy
pip install outcome
pip install pandas
pip install py
pip install PySocks
pip install python-dateutil
pip install pytz
pip install PyYAML
pip install requests
pip install retry
pip install selenium
pip install six
pip install sniffio
pip install sortedcontainers
pip install soupsieve
pip install tqdm
pip install trio
pip install trio-websocket
pip install urllib3
pip install webencodings
pip install wsproto
pip install XlsxWriter
```
직접 설치

```shell
python crawler.py
or
py crawler.py
```
실행

# 생성되는 파일
```text
ex : 20221030030459_instagram.csv
```
년월일시분초_instagram.csv

# 로직
- collect.profiles 해당 profile 접근
- collect.items.post.recentCount 개수 만큼의 post 주소 추출
- 추출된 전체 post 의 comment 주소에 접근
- post comment 페이지 에서 more 버튼 클릭 하여 comment 개수가 collect.items.comment.recentCount 만큼 진행
- post comment 페이지 전체 comment 를 load 하였으나 collect.items.comment.recentCount 개수 보다 적은 경우 pass
- 모든 post 의 comment 추출 완료시 csv 생성

# 로그 확인 필요사항
## 로그 패턴
```text
[INFO][    InstagramCrawler][          move_to_profile_page][2022-10-30 03:20:05,808] : profile : test
```
- `로그 레벨` -> INFO
- `class 파일` -> InstagramCrawler
- `실행 function` -> move_to_profile_page
- `시간` -> 2022-10-30 03:20:05,808
- `메시지` -> profile : test


## Case 1
```text
[Throws an exception ans passes] message : Message: element click intercepted: Element 
<button class="_abl-" type="button">...</button> is not clickable at point
```
- 스크롤 다운 후 인스타그램 자체의 more 버튼이 정상적으로 로드되지 않아 버튼을 클릭할 수 없는 경우 또는 모든 코멘트를 로드 하였지만 다른 버튼과 겹친 경우.
- 결과 : skip.

## Case 2
```text
 └─ profile(test) not found username is pass
```
- `test` profile에 해당하는 인스타그램이 존재하지 않음.
- 결과 : skip.

## Case 3
```text
 └─ post({post}) not found comments is pass
```
- post 의 comment 가 존재하지 않음.
- 결과 : skip.
