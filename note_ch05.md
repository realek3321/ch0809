

# 5. 기존 앱 개선

# 5.0 표 형식 블로그

![첫 페이지 화면](ch0501_tabular_blog.png)

- page 크기 수정 (blog/views.py 파일에서 2개에서 5개로 수정)
  ```Python {.line-numbers}
  ...
  class PostLV(ListView) :
      model = Post
      # 기본값 'blog/post_list.html' 대신 지정 (3.1.4 URL 설계 항 참고)
      template_name = 'blog/post_all.html'
      # 컨텍스트 객체 이름을 기본값(object_list)와 다르게 지정했지만,
      # 기본값(object_list)도 여전히 사용 가능함
      context_object_name = 'posts'
      paginate_by = 5  # !!!페이지 당 5 개 객체를 처리하도록 지정!!!
  ...
  ```

- 템플릿 수정 (표형태로 html 태그 및 css 수정)
  ```Python {.line-numbers}
  ...
  <!DOCTYPE html>
  <html lang="ko">
  <head>
      <meta charset="UTF-8">
      <title>장고 블로그 리스트</title>
      <style>
          #tbl {
              font-family: "Trebuchet MS", Arial, Helvetica, sans-serif;
              border-collapse: collapse;
              width: 90%;
              margin: auto;
          }
  
          #tbl td, #tbl th {
              border: 1px solid #ddd;
              padding: 8px;
          }
  
          #tbl tr:nth-child(even){background-color: #f2f2f2;}
  
          #tbl tr:hover {background-color: #ddd;}
  
          #tbl th {
              padding-top: 12px;
              padding-bottom: 12px;
              text-align: left;
              background-color: #4CAF50;
              color: white;
          }
  
          .centered {
              text-align: center;
          }
      </style>
  </head>
  <body>
      <div id="content">
          <h1>블로그 목록 (표 형식)</h1>
          <table id="tbl" >
              <tr>
                  <th>번호</th>
                  <th>최종 수정일</th>
                  <th>제목</th>
                  <th>슬러그</th>
                  <th>요약</th>
              </tr>
              {% for post in posts %}
              <tr>
                  <td>{{ post.id }}</td>
                  <td>{{ post.modify_date }}</td>
                  <td><a href='{{ post.get_absolute_url }}'>{{ post.title }}</a></td>
                  <td>{{ post.slug }}</td>
                  <td><p>{{ post.description }}</p></td>
              </tr>
              {% endfor %}
          </table>
      </div>
  
      <br/>
  
      <!--이전/현재/다음 페이지 이동 -->
      <div class="centered"> <!--div 요소는 블록 요소-->
          <span>  <!--span 요소는 인라인 요소-->
              <!--page_obj는 장고 Page 객체가 들어있는 컨텍스트 변수이며
              has_previous, has_next, previous_page_number, next_page_number,
              number, pagenator.num_pages 속성을 가짐-->
              {% if page_obj.has_previous %}
                  <!--링크 형식 ?page=1에 주목-->
                  <a href="?page={{ page_obj.previous_page_number }}">이전 페이지</a>
              {% endif %}
  
              {{ page_obj.number }} / {{ page_obj.paginator.num_pages }}
  
              {% if page_obj.has_next %}
                  <!--링크 형식 ?page=3에 주목-->
                  <a href="?page={{ page_obj.next_page_number }}">다음 페이지</a>
              {% endif %}
          </span>
      </div>
  </body>
  </html>
  ```

***

- bookmark = 책갈피 = 바로가기  
- bog = web log = 웹에서 작성하는 일지 = 웹 게시판
- 프로젝트 첫 페이지
  - 홈 페이지 또는 첫 페이지
  - 개성 있는 UI, 일관성 있는 룩앤필
  - 기능보다는 디자인이 중요
  - HTML, 스타일시트, 자바스크립트
  - 4장에선 웹 사이트 첫 페이지 디자인 및 메뉴 구성
  - 5장에선 기존 웹 앱 페이지 개선 작업  

***

- GNB(global navigation bar) = 메뉴
- 기존 앱의 템플릿도 4장 base.html을 상속받아서 재 작성
- GNB에 앱 템플릿 연결
- 처음부터 base.html을 만들었다면, 이중 작업이 안되었겠지만, 
  이런 상황은 실제로 자주 발생하니, 감수해야 함

***

# 5.1 첫 페이지 설계

## 5.1.1 화면 UI 설계
- 첫 페이지 화면: 제목(header), 메뉴(menu), 본문(content), 바닥글(footer)
  ![첫 페이지 화면](ch0502_1stPage.png)

## 5.1.2 테이블 설계
  변경 사항 없음

## 5.1.3 URL 설계
  변경 사항 없음

## 5.1.4 작업/코딩 순서

|  <center>순서</center> |  <center>명령/파일</center>  |  <center>작업 내용</center> |
|--------            |:--------                |:--------                |
|**뼈대 만들기**      | startproject            |(ch02에서 이미 작업, 생략)|
| <center>"</center> | settings.py             |<center>"</center>|
| <center>"</center> | migrate                 |<center>"</center>|
| <center>"</center> | createsuperuser         |<center>"</center>|
| <center>"</center> | startapp                |*(앱 생성 없으므로 생략)*        |
| <center>"</center> | settings.py             |        |
|**모델 코딩**      | models.py                |*(테이블 생성 없으므로 생략)*        |
|<center>"</center>| admin.py                 |        |
|<center>"</center>| makemigrations           |        |
|<center>"</center>| migrate                  |        |
|**URLconf 코딩**  | urls.py                  |*URL 변경 없으므로 생략*        |
|**뷰 코딩**       | views.py                 |*뷰 변경 없으므로 생략*        |
|**템플릿 코딩**   | templates 폴더           |*base.html 메뉴 링크 수정*        |
|**템플릿 코딩**   | templates 폴더           |*home.html 링크 수정*        |
|**템플릿 코딩**   | templates 폴더           |*북마크 앱 템플릿 수정*        |
|**템플릿 코딩**   | templates 폴더           |*블로그 앱 템플릿 수정*        |
|**기타 코딩**    | static 폴더                |*없음*        |

***

# 5.2 개발 코딩
## 5.2.1 뼈대 만들기
- 신규 앱 생성이 없으므로 생략

## 5.2.2 모델 코딩
- DB 수정 사항 없으므로 생략

## 5.2.3 URLconf 코딩
- URLconf 수정 사항 없으므로 생략

## 5.2.4 뷰 코딩
- 뷰 수정 사항 없으므로 생략

## 5.2.5 템플릿 코딩 

- base.html 메뉴 링크 수정
  ```html {.line-numbers}
  ...
  <!--스타일시트 css/base.css 작성이 필요함-->
  <link href="https://fonts.googleapis.com/css?family=Sunflower:300" rel="stylesheet">
  ...  
  <div id="menu">                     <!--메뉴 영역-->
      <li><a href="{% url 'home' %}">홈</a></li>
      <li><a href="{% url 'bookmark:index' %}">책갈피</a></li>
      <li><a href="{% url 'blog:index' %}">게시판</a></li>
      <li><a href="#">사진첩</a></li>  # 7장에서 변경 예정
  ...
      <li><a href="{% url 'blog:post_archive' %}">저장소</a></li>
      <li><a href="#">검색</a></li> # 9장에서 변경 예정
      <li><a href="{% url 'admin:index' %}">관리자</a></li>
  </div>
  ...  
  ```

  |  <center>라인</center> |  <center>메뉴 버튼 이름</center>  |  <center>URL 패턴 이름</center>  |  <center>{% %} 반환값</center> |
  |--------             |:--------            |:--------    |:--------            |
  |**3**      | 홈      | 'home'              | /          |
  |**4**      | 책갈피  | 'bookmark:index'    | /bookmark/ |
  |**5**      | 게시판  | 'blog:index'        | /blog/ |
  |**8**      | 저장소  | 'blog:post_archive' | /blog/achive/ |
  |**10**     | 관리자  | 'admin:index'       | /admin/ |
   

- templates/home.html 수정

  ```html {.line-numbers}
  ... 
    <table id="applist">
        <tr>
        <td><b><i><a href="{% url 'bookmark:index' %}">책갈피</a></i><b></td>
        <td>당신의 책갈피를 등록하고 다른 사람들과 공유할 수 있습니다. 어쩌구 저쩌구. 중얼중얼.</td>
        <td id="Edit"><i><a href="#">등록</a></i></td>
        <td id="Edit"><i><a href="#">편집</a></i></td>
        </tr>

        <tr>
        <td><b><i><a href="{% url 'blog:index' %}">게시판</a></i><b></td>
  ... 
  ```
  |  <center>라인</center> |  <center>링크 텍스트</center>  |  <center>URL 패턴 이름</center>  |  <center>{% %} 반환값</center> |
  |--------             |:--------            |:--------    |:--------            |
  |**4**       | 책갈피 | 'bookmark:index'    | /bookmark/ |
  |**11**      | 게시판 | 'blog:index'        | /blog/     |
  
- bookmark/templates/bookmark/bookmark_list.html 수정

  ```html {.line-numbers}
  {% extends "base.html" %}  <!--반드시 첫 줄에 있어야 함-->
  {% block title %}장고 책갈피 목록{% endblock %}
  {% load staticfiles %}
  {% block extrastyle %}{% static "css/tabular.css" %}{% endblock %}
  {% block content %} <!--콘텐츠 블록 재정의-->
      <div id="content"> <!--base.css 내부의 div#contents 스타일 적용-->
          <h1>바로가기 모음</h1>
          <table id="tbl" >
              <tr>
                  <th>id</th>
                  <th>title</th>
                  <th>url</th>
              </tr>
              {% for bookmark in object_list %} <!-- 디폴트 컨텍스트 변수가 objecct_list -->
                  <tr>
                      <td>{{ bookmark.id }}</td>
                      <td>{{ bookmark.title }}</td>
                      <td>
                          <!-- bookmark 이름공간의 detail이라는 name을 가지는 url로-->
                          <!-- 연결하면서 bookmark.id를 매개변수로 전달-->
                          <a href="{% url 'bookmark:detail' bookmark.id %}">
                              {{ bookmark.url }}
                          </a>
                      </td>
                  </tr>
              {% endfor %}
          </table>
      </div>
  {% endblock %}
  ```
  
- bookmark/templates/bookmark/bookmark_detail.html 수정

  ```html {.line-numbers}
  {% extends "base.html" %}  <!--반드시 첫 줄에 있어야 함-->
  {% block title %}장고 책갈피 상세보기{% endblock %}
  {% block content %}
      <div id="content"> <!--base.css 내부의 div#contents 스타일 적용-->
          <h1>{{ object.title }}</h1>
          <ul>
              <li>
                  URL: <a href="{{ object.url }}">{{ object.url }}</a>
              </li>
          </ul>
      </div>
  {% endblock %}
  ```

- blog/templates/blog/post_all.html 수정

  ```html {.line-numbers}
  {% extends "base.html" %}
  {% block title %}장고 게시글 목록{% endblock %}
  {% load staticfiles %}
  {% block extrastyle %}{% static "css/tabular.css" %}{% endblock %}
  {% block content %}
      <!--div#content 내부로 전체 콘텐츠 이동-->
      <div id="content">
          <h1>블로그 목록 (표 형식)</h1>
          <table id="tbl">
              <tr>
                  <th>번호</th>
                  <th>최종 수정일</th>
                  <th>제목</th>
                  <th>슬러그</th>
                  <th>요약</th>
              </tr>
              {% for post in posts %}
              <tr>
                  <td>{{ post.id }}</td>
                  <td>{{ post.modify_date }}</td>
                  <td><a href='{{ post.get_absolute_url }}'>{{ post.title }}</a></td>
                  <td>{{ post.slug }}</td>
                  <!--<td><p>{{ post.description }}</p></td>-->
                  <td>{{ post.description }}</td>
              </tr>
              {% endfor %}
          </table>
          <br/>
          <!--이전/현재/다음 페이지 이동 -->
          <div class="centered"> <!--div 요소는 블록 요소-->
              <span>  <!--span 요소는 인라인 요소-->
                  {% if page_obj.has_previous %}
                      <a href="?page={{ page_obj.previous_page_number }}">이전 페이지</a>
                  {% endif %}
  
                  {{ page_obj.number }} / {{ page_obj.paginator.num_pages }}
  
                  {% if page_obj.has_next %}
                      <a href="?page={{ page_obj.next_page_number }}">다음 페이지</a>
                  {% endif %}
              </span>
          </div>
      </div>
  {% endblock %}
  ```


- blog/templates/blog/post_detail.html 수정

  ```html {.line-numbers}
  {% extends "base.html" %}
  {% block title %}장고 게시글 보기{% endblock %}
  {% load staticfiles %}
  {% block extrastyle %}{% static "css/tabular.css" %}{% endblock %}
  {% block content %}
      <!--div#content 내부로 전체 콘텐츠 이동-->
      <div id="content" class="centered">
          <h2>{{ object.title }}</h2>
          <p class="other_posts">
              {% if object.get_previous_by_modify_date %}
                  <a href="{{ object.get_previous_post.get_absolute_url }}" title="View previous post">
                      &laquo;-- {{ object.get_previous_post }}
                  </a>
              {% endif %}
  
              {% if object.get_next_by_modify_date %}
                  &nbsp; |
                  <a href="{{ object.get_next_post.get_absolute_url }}" title="View next post">
                      {{ object.get_next_post }} --&raquo;
                  </a>
              {% endif %}
          </p>
          <p class="date">{{ object.modify_date|date:"j F Y" }}</p>
          <br/>
  
          <div class="body">
              {{ object.content|linebreaks }}
          </div>
      </div>
  {% endblock %}}
  ```

- static/tabular.css 수정

  ```css {.line-numbers}
  #tbl {
      border-collapse: collapse;
      width: 90%;
      margin: auto;
  }
  
  #tbl td, #tbl th {
      border: 1px solid #ddd;
      padding: 8px;
  }
  
  #tbl tr:nth-child(even){background-color: #f2f2f2;}
  
  #tbl tr:hover {background-color: #ddd;}
  
  #tbl th {
      padding-top: 12px;
      padding-bottom: 12px;
      text-align: left;
      background-color: #4CAF50;
      color: white;
  }
  
  .centered { text-align: center;}
  .leftpadding30percent { padding-left: 30%; }
  ```
  
- blog/templates/blog/post_archive.html 수정

  ```html {.line-numbers}
  {% extends "base.html" %}
  {% block title %}장고 게시글 저장소{% endblock %}
  {% load staticfiles %}
  {% block extrastyle %}{% static "css/tabular.css" %}{% endblock %}
  {% block content %}
      <!--div#content 내부로 전체 콘텐츠 이동-->
      <div id="content">
          <h1 class="centered">{% now "Y년 m월 d일" %}까지 게시글 모음</h1>
          <ul class="centered">
              {% for date in date_list %}
                  <li style="display: inline;">
                      <a href="{% url 'blog:post_year_archive' date|date:'Y' %}">
                          {{ date|date:"Y" }}년
                      </a>
                  </li>&nbsp;&nbsp;
              {% endfor %}
          </ul>
          <br/>
          <div class="leftpadding30percent">
              <ul>
                  {% for post in object_list %}
                      <li>
                          {{ post.modify_date|date:"Y-m-d" }}&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                          <a href="{{ post.get_absolute_url }}">
                              <strong>{{ post.title }}</strong>
                          </a>
                      </li>
                  {% endfor %}
              </ul>
          </div>
      </div>
  {% endblock %}
  ```

- blog/templates/blog/post_archive_year.html 수정

  ```html {.line-numbers}
  {% extends "base.html" %}
  {% block title %}장고 게시글 목록{% endblock %}
  {% load staticfiles %}
  {% block extrastyle %}{% static "css/tabular.css" %}{% endblock %}
  {% block content %}
      <!--div#content 내부로 전체 콘텐츠 이동-->
      <div id="content">
          <h1 class="centered">{{ year|date:"Y" }}년 게시글 모음</h1>
          <ul class="centered">
              {% for date in date_list %}
              <li style="display: inline">
                  <a href="{% url 'blog:post_month_archive' year|date:'Y' date|date:'b' %}">
                      <!--월 제목을 "F" 형태(예: July)로 출력 -->
                      <!--{{ date|date:"F" }}-->
                      {{ date|date:"m" }}월
                  </a>
                  &nbsp; &nbsp;
              </li>
              {% endfor %}
          </ul>
          <br/>
          <div class="leftpadding30percent">
              <ul>
                  {% for post in object_list %}
                  <li>
                      {{ post.modify_date|date:"Y-m-d" }}&nbsp;&nbsp;&nbsp;&nbsp;
                      <a href="{{ post.get_absolute_url }}">
                          <strong>{{ post.title }}</strong>
                      </a>
                  </li>
                  {% endfor %}
              </ul>
          </div>
      </div>
  {% endblock %}
  ```
 
- blog/templates/blog/post_archive_month.html 수정

  ```html {.line-numbers}
  {% extends "base.html" %}
  {% block title %}장고 게시글 저장소{% endblock %}
  {% load staticfiles %}
  {% block extrastyle %}{% static "css/tabular.css" %}{% endblock %}
  {% block content %}
      <!--div#content 내부로 전체 콘텐츠 이동-->
      <div id="content">
          <h1 class="centered">{{ month|date:"Y년 m월" }} 게시글 모음</h1>
  
          <div class="leftpadding30percent">
              <ul>
                  {% for post in object_list %}
                  <li>
                      {{ post.modify_date|date:"Y-m-d" }}&nbsp;&nbsp;&nbsp;
                      <a href="{{ post.get_absolute_url }}">
                          <strong>{{ post.title }}</strong>
                      </a>
                  </li>
                  {% endfor %}
              </ul>
          </div>
      </div>
  {% endblock %}
  ``` 
  
- blog/templates/blog/post_archive_day.html 수정

  ```html {.line-numbers}
  {% extends "base.html" %}
  {% block title %}장고 게시글 저장소{% endblock %}
  {% load staticfiles %}
  {% block extrastyle %}{% static "css/tabular.css" %}{% endblock %}
  {% block content %}
      <!--div#content 내부로 전체 콘텐츠 이동-->
      <div id="content">
          <h1 class="centered">{{ day|date:"Y년 m월 d일"}} 게시글 모음</h1>
          <div class="leftpadding30percent">
              <ul>
                  {% for post in object_list %}
                  <li>
                      {{ post.modify_date|date:"Y-m-d" }}&nbsp;&nbsp;&nbsp;
                      <a href="{{ post.get_absolute_url }}">
                          <strong>{{ post.title }}</strong>
                      </a>
                  </li>
                  {% endfor %}
              </ul>
          </div>
      </div>
  {% endblock %}
  ``` 

# 4.3 Git 및 PythonAnywhere 작업
## 4.3.1 Git 작업
- ch02에서 수행한 아래 작업은 최초 한번만 수행하면 되므로 생략
  - Git 설치
  - Git 저장소 초기화
    ```shell {.line-numbers}
    (myvenv) C:\work>git init
    # git 계정이 여러 개 있다면 아래에서 user.name을 적절하게 입력해야 함
    (myvenv) C:\work>git config --global user.name "Your Name"
    # git 계정이 여러 개 있다면 아래에서 user.email을 적절하게 입력해야 함
    (myvenv) C:\work>git config --global user.email you@example.com  
    ```
  - work\\.gitignore 파일 수정(db.sqlite3 파일 및 static 폴더에 관한 행을 삭제)
  - Git 저장소 상태 확인 및 등록
    ``` {.line-numbers}
    (myvenv) C:\work>git status
    (myvenv) C:\work>git add --all .
    (myvenv) C:\work>git commit -m "ch05"
    ```
  - https://github.com(무료 온라인 Git 저장소) 최초 배포
    - 계정 로그인
    - 화면 우측 상단의 + pull-down 메뉴에서 new repository 클릭, "ch05" 입력, Create repository 클릭

  - Git add 및 push
    ``` {.line-numbers}
    # fatal: remote origin already exists 오류가 발생한다면 rm origin 작업 수행
    (myvenv) C:\work>git remote rm origin
    (myvenv) C:\work>git remote add origin https://github.com/<your-github-username>/ch05.git
    (myvenv) C:\work>git push -u origin master
    ```
## 4.3.2 pythonanywhere 작업
- ch02에서 수행한 아래 작업은 최초 한번만 수행하면 되므로 생략
  - http://www.pythonanywhere.com 로그인  
  - bash console에서 git clone 작업, 가상환경 생성 및 활성화
    ```bash {.line-numbers}
    $ cd ~          # 홈 디렉토리로 이동
    $ ls            # 홈 디렉토리의 리스트 보기(blog 폴더 없음을 확인)
    $ git clone https://github.com/<your-github-username>/ch04.git
    $ ls            # 홈 디렉토리의 리스트 보기(blog 폴더 생성을 확인)
    $ cd work
    $ virtualenv --python=python3.6 myvenv
    $ source myvenv/bin/activate
    (myvenv) $ pip install -r lstModule.txt
    # (myvenv) $ python manage.py migrate         # db.sqlite3를 git clone 했으므로 생략
    # (myvenv) $ python manage.py createsuperuser # db.sqlite3를 git clone 했으므로 생략
    ```  
- 웹 탭으로 이동하여 블로그 웹 앱을 배포
  - Code 섹션에서 Source code 폴더를 work로 수정
  - WSGI 파일 설정 수정
    - Code 섹션에서, WSGI configuration file 링크 클릭하고
      아래와 같이 4번 행을 수정
    - 13번 행의 주석도 유의
    ```python {.line-numbers}
    import os
    import sys

    path = '/home/<your-PythonAnywhere-username>/work'  # 해당 계정 및 폴더 확인
    if path not in sys.path:
        sys.path.append(path)

    os.environ['DJANGO_SETTINGS_MODULE'] = 'mysite.settings'

    from django.core.wsgi import get_wsgi_application
    from django.contrib.staticfiles.handlers import StaticFilesHandler
    application = StaticFilesHandler(get_wsgi_application())
    # 코드 마지막 줄에서 개행문자가 안들어가면 오류 발생함
    ```
  - 저장(Save) 후, Web 탭에서 큰 녹색 다시 불러오기(Reload) 버튼 클릭
  - 페이지 최상단 링크로 브라우징


## 4.3.3 로컬에서 소스 코드 수정 후 재 배포 작업
- 로컬에서 소스 코드 일부 수정
- 로컬에서 runserver 작업하여 수정되었음을 확인
- 수정 후 로컬 git push 작업
  ```bash {.line-numbers}
  (myvenv) c:\work>git status
  (myvenv) c:\work>git add --all .
  (myvenv) c:\work>git status
  (myvenv) c:\work>git commit -m "... changed..."
  (myvenv) c:\work>git push
  ```
- 수정 후 서버 git pull 작업
  ```bash {.line-numbers}
  $ cd ~
  $ cd ch04
  $ git pull
  ```
- Web 탭에서 큰 녹색 다시 불러오기(Reload) 버튼 클릭 후, 페이지 최상단 링크로 브라우징
- 서버에 접속하여 제목이 수정된 결과 확인
