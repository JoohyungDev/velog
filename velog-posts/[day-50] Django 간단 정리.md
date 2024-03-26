<h2 id="mpa-spa">MPA, SPA</h2>
<ul>
<li>MPA : 멀티 페이지 어플리케이션(여러개의 HTML 파일)<ul>
<li>장점 : 아키텍처가 간단하여 초급자가 쉽게 개발할 수 있다. </li>
<li>단점 : 사용자 경험이 떨어진다. 매 페이지 요청마다 새로고침(리로딩)이 발생한다. 서버 렌더링에 따른 부하가 발생한다.</li>
</ul>
</li>
<li>SPA : 싱글 페이지 어플리케이션(단 하나의 HTML)<ul>
<li>장점 : 자연스러운 사용자 경험, 반복되는 패턴을 컴포넌트로 만들어 재사용이 가능하다.</li>
<li>단점 : 초급자가 많은 프로젝트의 경우 도입이 힘들다.</li>
</ul>
</li>
</ul>
<h2 id="아키텍쳐와-mtv-패턴">아키텍쳐와 MTV 패턴</h2>
<h3 id="아키텍쳐">아키텍쳐</h3>
<ul>
<li>모놀리식 : 백엔드와 프론트엔드가 하나의 서버에서 하나의 아키텍쳐에서 개발</li>
<li>마이크로식 : 백엔드와 프론트엔드가 다른 서버에서 다른 아키텍쳐로 개발</li>
</ul>
<h3 id="mtv-패턴">MTV 패턴</h3>
<ul>
<li><p>Django</p>
<ul>
<li>Model <ul>
<li>DB에 저장되는 데이터를 정의 및 관리</li>
<li>ORM으로 DB 조작</li>
</ul>
</li>
<li>Template<ul>
<li>프론트엔드 화면을 정의</li>
<li>백엔드 데이터를 템플릿에 주입하여(템플릿 문법) 표현 가능</li>
</ul>
</li>
<li>View <ul>
<li>클라이언트 요청을 처리하고, 모델과 템플릿을 연결</li>
<li>모델에서 데이터를 가져와 템플릿에 전달</li>
<li>사용자 인증, 폼처리 등의 작업 수행</li>
<li>비즈니스 로직 구현</li>
</ul>
</li>
</ul>
</li>
<li><p>Spring</p>
<ul>
<li>Model <ul>
<li>Django의 Model과 유사</li>
<li>DB에 매핑되는 도메인 객체 정의</li>
</ul>
</li>
<li>View<ul>
<li>Django의 Template과 유사</li>
</ul>
</li>
<li>Controller<ul>
<li>Django의 View와 유사</li>
</ul>
</li>
</ul>
<h2 id="fe-프레임워크와-결합">FE 프레임워크와 결합</h2>
<ul>
<li>Django + React (대세, 스타트업은 거의 공식과 유사)</li>
<li>Django + View</li>
<li>Django + tailwind : 디자인대로 개발 가능. 클래스를 외워야 함</li>
<li>Django + Bootstrap : 빠르게 개발 가능. 커스터마이징 힘듬</li>
</ul>
</li>
</ul>
<h2 id="모델과-db의-관계">모델과 DB의 관계</h2>
<ul>
<li>1:1<ul>
<li>models.OneToOneField</li>
</ul>
</li>
<li>1:N<ul>
<li>models.ForeignKey()의 인자로 N을 넣음</li>
</ul>
</li>
<li>N:M<ul>
<li>models.ManyToManyField()의 인자로 둘중 하나를 넣음</li>
</ul>
</li>
</ul>
<h2 id="url-라우팅">URL 라우팅</h2>
<pre><code class="language-python">from django.urls import path, include
from django.conf.urls import url
from blog import views

urlpatterns = [
    path('a/', include('a.urls')),
    path('b/', include('b.urls')),
    path('c/', include('c.urls')),
    path('d/', include('d.urls')),
    path('e/', views.index),
    url(r'^book/(?P&lt;book_name&gt;\w+)/$', views.bookinfo), # 정규표현식을 사용할 수도 있다!
]</code></pre>
<p>위 코드처럼 정규표현식으로도 표현이 가능하지만 보통은 사용하지 않는다.</p>
<h2 id="fbv-cbv">FBV, CBV</h2>
<h3 id="fbv">FBV</h3>
<p>함수 기반으로 뷰를 구성한다. 간단한 기능을 만들 때 사용한다.</p>
<h3 id="cbv">CBV</h3>
<p>클래스 기반으로 뷰를 구성한다. 제네릭 뷰도 클래스 뷰에 속한다. </p>
<p>실무에서는 섞어서 사용하며 보통 CBV를 사용한다.</p>
<h2 id="drfdjango-rest-framework">DRF(Django Rest Framework)</h2>
<p>JSON 형식으로 데이터를 직렬화하여 클라이언트와 서버 간에 통신을 진행한다. </p>