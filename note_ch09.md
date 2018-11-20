![attitude](http://edukairos.co.kr/files/attach/images/371/393/006/07fa3a0d40e77687e478f7e0a03f2d23.jpg)

- 장고_자습서 <https://wikidocs.net/book/837>
- ch09 서버 모습 <http://logistex018.pythonanywhere.com/>

- 7장: 태그 기능
- 8장: 댓글 기능
- 9장: 검색 기능
  - 게시판 앱에 한정된 검색 기능
  - 전체 사이트에 대한 검색 기능에 도전해 보길!

# 9.1 애플리케이션 설계
- 게시판 앱에 한정한 검색 기능
  - 장고 자체의 Q 객체를 활용하여 구현
  - 장고 Q 객체는 테이블에 대한 복잡한 쿼리를 처리하는 객체
- 검색 기능의 구현
  - 검색어를 입력받는 폼
  - Q 객체로 게시글에서 검색
  - 검색 결과를 보여주는 기능

## 9.1.1 화면 UI 설계

- 게시글 검색 화면 <http://logistex018.pythonanywhere.com/blog/search/>

## 9.1.2 테이블 설계
- 테이블 관련 변경 없음

## 9.1.3 URL 설계
- 검색 관련 URL 한 개 추가

## 9.1.4 작업/코딩 순서

|  <center>순서</center> |  <center>명령/파일</center>  |  <center>작업 내용</center> |
|--------            |:--------                |:--------                |
|**뼈대 만들기**      | startproject            |(ch02에서 이미 작업, 생략)|
| <center>"</center> | settings.py             |<center>"</center>|
| <center>"</center> | migrate                 |<center>"</center>|
| <center>"</center> | createsuperuser         |<center>"</center>|
| <center>"</center> | startapp                |(앱 추가 불필요)        |
| <center>"</center> | settings.py             |        |
|**모델 코딩**      | models.py                |(변경 없음)        |
|<center>"</center>| admin.py                 |(변경 없음)        |
|<center>"</center>| makemigrations           |(변경 없음)        |
|<center>"</center>| migrate                  |(변경 없음)        |
|**URLconf 코딩**  | urls.py                  |*URL 정의 추가*        |
|**뷰 코딩**       | views.py                 |*뷰 로직 추가*        |
|**템플릿 코딩**   | templates 폴더           |*템플릿 추가/수정*        |
|**기타 코딩**    | forms.py               |*검색 폼 클래스 정의*        |

***

# 9.2 개발 코딩

## 9.2.1 뼈대 만들기
- 신규 앱 생성 및 오픈 소스 앱 추가가 아니므로 별도 작업 없음  

## 9.2.2 모델 코딩
- 테이블 수정 사항 없음

## 9.2.3 URLconf 코딩
- blog/urls.py 파일에 검색용 URL 1 개 추가
```python {.line-numbers}
...
# Example: /tag/tagname
url(r'^tag/(?P<tag>[^/]+(?u))/$', PostTOL.as_view(), name='tagged_object_list'),

# Example: /search/  # ch09 1/1
url(r'^search/$', SearchFormView.as_view(), name='search'),
]
```
## 9.2.4 뷰 코딩
- 검색
  - 검색 폼에서 검색어 입력
  - 입력된 검색어를 뷰에서 처리
  - 뷰 코딩에 앞서서 검색 폼을 먼저 작성

- blog/forms.py 작성
```python {.line-numbers}
# ch09 1/1 추가 시작
# 장고에서는 폼도 클래스를 활용하여 정의
from django import forms

# django.forms.Form 클래스를 상속받아 폼을 작성
class PostSearchForm(forms.Form):
    # 폼을 정의하는 방법은 테이블의 모델 클래스를 정의하는 방법과 유사함
    # CharField는 TextInput 위젯으로 표현됨
    # TextInput 위젯 압페 label이 표시되고,
    # 변수 search_word는 입력 필드에 대한 id 역할
    search_word = forms.CharField(label='검색어')
# ch09 1/1 추가 종료
```
- blog/views.py 파일 후미에 클래스형 뷰 SearchFormView 추가
```python {.line-numbers}
# ch09추가 1/1 시작
from django.views.generic.edit import FormView
from blog.forms import PostSearchForm
from django.db.models import Q                  # 검색 기능에 필요한 클래스
from django.shortcuts import render             # 단축 함수 render()

# FormView를 상속받아 SearchFormView 작성
# FormView는 GET 요청인 경우 폼을 화면에 보여주고 사용자 입력을 대기함
# 사용자가 폼에 데이터 입력 후 제출하면 이는 POST 요청으로 접수되고
# 입력 데이터에 대한 유효성 검사를 실시하여 유효하면
# form_valid() 함수를 실행한 후 적절한 URL로 리다이렉트 처리함
class SearchFormView(FormView):
    form_class = PostSearchForm             # 폼으로 사용할 클래스를 지정
    template_name = 'blog/post_search.html' # 템플릿 지정

    # 입력 데이터에 대한 유효성 검사를 실시하여 유효하면 실행될 함수
    def form_valid(self, form) :
	    # POST 요청의 id가 'search_word'인 값을 추출하여 변수에 저장
        schWord = '%s' % self.request.POST['search_word']
	    # filter() 메소드의 매칭 조건을 Q 객체로 다양하게 지정 가능함
	    # 각 조건에서 icontains 연산자는 대소문자 구별 없이
	    # 검색어가 포함되었는지 검사
	    # distinct() 메소드는 중복된 객체를 제외함
	    # 결국, Post 테이블의 모든 레코드에 대하여
	    # title, description, content, tag 필드에
	    # schWord가 포함된 레코드를 대소문자 구별 없이 검색해서 중복 없는 리스트로 저장
        post_list = Post.objects.filter(
	        Q(title__icontains=schWord) |
	        Q(description__icontains=schWord) |
	        Q(content__icontains=schWord) |
	        Q(tag__icontains=schWord)
        ).distinct()

        # 템플릿에 전달할 맥락 변수 context를 사전 형식으로 정의
        context = {}
        context['form'] = form  # 여기서 form은 PostSearchForm을 지칭함
        context['search_term'] = schWord
        context['object_list'] = post_list

		# 단축 함수 render()는 템플릿과 맥락 변수를 처리하여,
	    # 최종적으로 HttpResponse 객체를 반환
	    # 일반적으로 form_valid() 함수는 리다이렉트 처리를 위하여
	    # HttpResponseRedirect 객체를 반환하는데,
	    # 여기서는 render() 함수가 HttpResponse 객체를 반환하므로
	    # 리다이렉트 처리가 되지 않게됨.
        return render(self.request, self.template_name, context)
# ch09 추가 1/1 종료
```

## 9.2.5 템플릿 코딩

- 홈 페이지 상단 '검색' 메뉴에 'blog:search'로 링크 연결
- templates/base.html 수정 (댓글 처리를 위한 5줄 추가)
```html {.line-numbers}
...
  <li><a href="{% url 'blog:post_archive' %}">저장소</a></li>
  <li><a href="{% url 'blog:search' %}">검색</a></li>     <!--ch09 수정 1/1-->
  <li><a href="{% url 'admin:index' %}">관리자</a></li>
</div>...  
```
- blog/templates/blog/post_search.html 작성
```html {.line-numbers}
<!--ch09 작성 시작-->
{% extends "base.html" %}
{% block title %}장고 게시글 검색{% endblock %}
{% load staticfiles %}
{% block extrastyle %}{% static "css/tabular.css" %}{% endblock %}
{% block content %}
<div id="content">
    <!--blog.views.SearchFormView.form_valid() 함수와 함께 보면서 작업하라-->
    <h1 class="centered">게시글 검색</h1>
    <!--form 태그로 폼을 출력, 확인 누르면 POST 방식으로 요청을 동일한 URL로 전송-->
    <!--CSRF 공격(Cross Site Request Forgery) 출처: http://itstory.tk/entry/CSRF-공격이란-그리고-CSRF-방어-방법 [덕's IT Story]-->
    <!--csrf-token 태그를 써서 CSRF 공격을 방지-->
    <form action="." method="post" class="centered"> {% csrf_token %}
        <!--form은 뷰가 넘겨준 PostSearchForm을 의미함-->
        {{ form.as_table }}
        <input type="submit" value="확인" />
    </form>
    <br/><br/>
    {% if object_list and search_term %}
        <h2 class="centered">({{ search_term }})에 대한 검색 결과검색 결과</h2>
        <table id="tbl">
            <tr>
                <th>번호</th>
                <th>최종 수정일</th>
                <th>제목</th>
                <th>슬러그</th>
                <th>요약</th>
                <th>태그</th>
            </tr>
            {% for post in object_list %}
            <tr>
                <td>{{ post.id }}</td>
                <td>{{ post.modify_date|date:"Y년 m월 d일" }}</td>
                <td>
                    <a href='{{ post.get_absolute_url }}'>
                        <strong>{{ post.title }}</strong>
                    </a>
                </td>
                <td>{{ post.slug }}</td>
                <td>{{ post.description }}</td>
                <td>{{ post.tag }}</td>
            </tr>
            {% endfor %}
        </table>
    {% elif search_term %}
        <p class="centered">
            <strong><i>
                ({{ search_term }})에 대한 검색 결과가 없습니다!
            </i></strong>
        </p>
    {% endif %}
</div>
{% endblock %}
<!--ch09 작성 종료-->
```

# 9.3 github.com을 통하여 서버에 배포

- 로컬에서 runserver 작업하여 확인
- 로컬에서 git push 작업
  ```bash {.line-numbers}
  (myvenv) c:\work>git status
  (myvenv) c:\work>git add --all .
  (myvenv) c:\work>git status
  (myvenv) c:\work>git commit -m "... changed..."
  (myvenv) c:\work>git push
  ```
- 서버에서 git pull 작업
  ```bash {.line-numbers}
  $ cd ~
  $ cd work
  $ git pull
  ```
- Web 탭에서 큰 녹색 다시 불러오기(Reload) 버튼 클릭
