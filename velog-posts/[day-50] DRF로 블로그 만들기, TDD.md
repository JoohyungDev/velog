<p>DRF를 이용하여 간단한 블로그를 설계한다.</p>
<h2 id="url-설계">URL 설계</h2>
<table>
<thead>
<tr>
<th>URL</th>
<th>기능</th>
<th>비회원 접근</th>
<th>회원 접근</th>
<th>작성자 접근</th>
</tr>
</thead>
<tbody><tr>
<td>/notice</td>
<td>자유 게시판</td>
<td>R</td>
<td>R, C</td>
<td>-</td>
</tr>
<tr>
<td>/notice/int:post_pk</td>
<td>자유 게시물 상세보기</td>
<td>R</td>
<td>R</td>
<td>R, U, D</td>
</tr>
<tr>
<td>/blog</td>
<td>회원 게시판</td>
<td>-</td>
<td>R, C</td>
<td>-</td>
</tr>
<tr>
<td>/blog/int:post_pk</td>
<td>회원 게시물 상세보기</td>
<td>-</td>
<td>R</td>
<td>R, U, D</td>
</tr>
</tbody></table>
<p>기능 설명:</p>
<ul>
<li>R: Read (읽기)</li>
<li>C: Create (생성)</li>
<li>U: Update (수정)</li>
<li>D: Delete (삭제)</li>
</ul>
<h2 id="구현-과정">구현 과정</h2>
<h3 id="drf-설치-및-프로젝트-생성">DRF 설치 및 프로젝트 생성</h3>
<pre><code class="language-shell">mkdir drf
cd drf

python -m venv venv
# source venv/Scripts/activate
.\venv\Scripts\activate

pip install django
pip install djangorestframework

django-admin startproject drf_tutorial .

python manage.py startapp notice
python manage.py startapp blog

python manage.py migrate

python manage.py createsuperuser</code></pre>
<pre><code class="language-python"># drf_tutorial &gt; settings.py

INSTALLED_APPS = [
    &quot;django.contrib.admin&quot;,
    &quot;django.contrib.auth&quot;,
    &quot;django.contrib.contenttypes&quot;,
    &quot;django.contrib.sessions&quot;,
    &quot;django.contrib.messages&quot;,
    &quot;django.contrib.staticfiles&quot;,
    # third party apss
    &quot;rest_framework&quot;,
    # my apps
    &quot;notice&quot;,
    &quot;blog&quot;,
]

# ... 중략 ...

LANGUAGE_CODE = 'ko-kr'

TIME_ZONE = 'Asia/Seoul'

USE_I18N = True

USE_TZ = True

# ... 중략 ...

MEDIA_URL = '/media/'
MEDIA_ROOT = BASE_DIR / 'media'</code></pre>
<h3 id="모델-생성">모델 생성</h3>
<pre><code class="language-python"># blog &gt; models.py

from django.db import models
from django.conf import settings


class Post(models.Model):
    author = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.CASCADE)
    title = models.CharField(max_length=100)
    content = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)</code></pre>
<p>blog앱과 마찬가지로 같은 모델을 notice앱에도 생성한다. 클래스명만 Post와 Notice로 다르다.</p>
<h3 id="모델의-필드-직렬화">모델의 필드 직렬화</h3>
<pre><code class="language-python"># blog &gt; serializers.py
from rest_framework import serializers
from .models import Post

class PostSerializer(serializers.ModelSerializer):
    class Meta:
        model = Post
        fields = '__all__' # Test에서 사용하기 위해 모든 필드를 사용하도록 설정</code></pre>
<p>notice앱도 마찬가지로 정의하고 import하는 이름만 다르다.</p>
<h3 id="관리자-페이지에-모델-등록">관리자 페이지에 모델 등록</h3>
<pre><code class="language-python">from django.contrib import admin
from .models import Post

admin.site.register(Post)</code></pre>
<p>앞과 비슷하다.</p>
<h3 id="마이그레이션-후-서버-실행">마이그레이션 후 서버 실행</h3>
<pre><code class="language-python">python manage.py makemigrations
python manage.py migrate

python manage.py runserver</code></pre>
<p>접속한 후 후에 테스트를 위해 게시물을 3개 생성한다. 이후, 서버를 종료한 후 아직 정의안한 urls와 views를 정의한다.</p>
<h3 id="url-정의">url 정의</h3>
<pre><code class="language-python"># blog &gt; urls.py

from django.urls import path
from . import views

urlpatterns = [
    path('post/', views.post_list, name='post_list'),
    path('post/&lt;int:pk&gt;/', views.post_detail, name='post_detail'),
]</code></pre>
<h3 id="view-정의-blog">view 정의 (Blog)</h3>
<pre><code class="language-python"># blog &gt; views.py
from rest_framework.decorators import api_view, permission_classes
from rest_framework.permissions import IsAuthenticated, IsAuthenticatedOrReadOnly
from rest_framework.response import Response
from rest_framework import status
from .models import Post
from .serializers import PostSerializer


@api_view([&quot;GET&quot;, &quot;POST&quot;])
@permission_classes([IsAuthenticated])
def post_list(request):
    if request.method == &quot;GET&quot;:
        posts = Post.objects.all()
        serializer = PostSerializer(posts, many=True)
        return Response(serializer.data)

    elif request.method == &quot;POST&quot;:
        serializer = PostSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save(author=request.user)
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)


@api_view([&quot;GET&quot;, &quot;PUT&quot;, &quot;DELETE&quot;])
@permission_classes([IsAuthenticated, IsAuthenticatedOrReadOnly])
def post_detail(request, pk):
    try:
        post = Post.objects.get(pk=pk)
    except Post.DoesNotExist:
        return Response(status=status.HTTP_404_NOT_FOUND)

    if request.method == &quot;GET&quot;:
        serializer = PostSerializer(post)
        return Response(serializer.data)

    elif request.method == &quot;PUT&quot;:
        if post.author != request.user:
            return Response(status=status.HTTP_403_FORBIDDEN)
        serializer = PostSerializer(post, data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

    elif request.method == &quot;DELETE&quot;:
        if post.author != request.user:
            return Response(status=status.HTTP_403_FORBIDDEN)
        post.delete()
        return Response(status=status.HTTP_204_NO_CONTENT)</code></pre>
<p>DRF에 익숙하지 않으므로 먼저, FBV로 정의해주었다.</p>
<ul>
<li><code>IsAuthenticated</code> : 로그인한 사용자만 접근 가능</li>
<li><code>IsAuthenticatedOrReadOnly</code> : 게시글 상세보기(post_detail)는 로그인 여부와 상관없이 가능하지만, 수정/삭제는 작성자만 가능</li>
</ul>
<ol>
<li><code>post_list</code> 함수</li>
</ol>
<ul>
<li><code>GET</code> 요청: 모든 Post 객체를 조회하며 인증된 사용자만 접근할 수 있다.</li>
<li><code>POST</code> 요청: 새로운 Post 객체를 생성한다. 인증된 사용자만 접근할 수 있으며, <code>author</code> 필드에 현재 사용자를 자동으로 설정한다.</li>
</ul>
<ol start="2">
<li><code>post_detail</code> 함수</li>
</ol>
<ul>
<li><code>GET</code> 요청: 특정 Post 객체를 조회한다. 인증된 사용자와 인증되지 않은 사용자 모두 접근할 수 있다.</li>
<li><code>PUT</code> 요청: 특정 Post 객체를 수정한다. 인증된 사용자 중에서도 해당 Post의 <code>author</code>인 경우에만 접근할 수 있다.</li>
<li><code>DELETE</code> 요청: 특정 Post 객체를 삭제한다. 인증된 사용자 중에서도 해당 Post의 <code>author</code>인 경우에만 접근할 수 있다.</li>
</ul>
<h3 id="view정의-notice">view정의 (Notice)</h3>
<pre><code class="language-python">from rest_framework.decorators import api_view, permission_classes
from rest_framework.permissions import IsAuthenticatedOrReadOnly
from rest_framework.response import Response
from rest_framework import status
from .models import Notice
from .serializers import NoticeSerializer


@api_view([&quot;GET&quot;, &quot;POST&quot;])
def notice_list(request):
    if request.method == &quot;GET&quot;:
        notices = Notice.objects.all()
        serializer = NoticeSerializer(notices, many=True)
        return Response(serializer.data)

    elif request.method == &quot;POST&quot;:
        if not request.user.is_authenticated:
            return Response(status=status.HTTP_401_UNAUTHORIZED)
        serializer = NoticeSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save(author=request.user)
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)


@api_view([&quot;GET&quot;, &quot;PUT&quot;, &quot;DELETE&quot;])
@permission_classes([IsAuthenticatedOrReadOnly])
def notice_detail(request, pk):
    try:
        notice = Notice.objects.get(pk=pk)
    except Notice.DoesNotExist:
        return Response(status=status.HTTP_404_NOT_FOUND)

    if request.method == &quot;GET&quot;:
        serializer = NoticeSerializer(notice)
        return Response(serializer.data)

    elif request.method == &quot;PUT&quot;:
        if not request.user.is_authenticated or notice.author != request.user:
            return Response(status=status.HTTP_403_FORBIDDEN)
        serializer = NoticeSerializer(notice, data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

    elif request.method == &quot;DELETE&quot;:
        if not request.user.is_authenticated or notice.author != request.user:
            return Response(status=status.HTTP_403_FORBIDDEN)
        notice.delete()
        return Response(status=status.HTTP_204_NO_CONTENT)</code></pre>
<ol>
<li><code>notice_list</code> 함수</li>
</ol>
<ul>
<li><code>GET</code> 요청: 인증 여부와 상관없이 모든 사용자가 접근할 수 있다.</li>
<li><code>POST</code> 요청: 인증된 사용자만 접근할 수 있으며, <code>author</code> 필드에 현재 사용자를 자동으로 설정한다. 인증되지 않은 사용자가 접근하면 <code>HTTP_401_UNAUTHORIZED</code> 응답을 반환한다.</li>
</ul>
<ol start="2">
<li><code>notice_detail</code> 함수</li>
</ol>
<ul>
<li><code>GET</code> 요청: 인증된 사용자와 인증되지 않은 사용자 모두 접근할 수 있다.</li>
<li><code>PUT</code> 요청: 인증된 사용자 중에서도 해당 Notice의 <code>author</code>인 경우에만 접근할 수 있다. 그렇지 않은 경우 <code>HTTP_403_FORBIDDEN</code> 응답을 반환한다.</li>
<li><code>DELETE</code> 요청: 인증된 사용자 중에서도 해당 Notice의 <code>author</code>인 경우에만 접근할 수 있다. 그렇지 않은 경우 <code>HTTP_403_FORBIDDEN</code> 응답을 반환한다.</li>
</ul>
<h2 id="접근-권한-테스트">접근 권한 테스트</h2>
<p>thunder client로 테스트를 해 보았다.</p>
<ol>
<li>자유 게시판(/notice) 테스트</li>
</ol>
<ul>
<li><a href="http://127.0.0.1:8000/notice/post/">http://127.0.0.1:8000/notice/post/</a><ul>
<li><code>GET</code> 요청: 비회원도 접근 가능하므로 200(OK) 응답</li>
<li><code>POST</code> 요청 (비회원): 비회원은 작성 권한이 없으므로 401(Unauthorized) 응답</li>
<li><code>POST</code> 요청 (회원):<ul>
<li>인증 방식: Basic Authentication</li>
<li>사용자 이름: 이름</li>
<li>비밀번호: 비밀번호</li>
<li>JSON 데이터: <code>{&quot;title&quot;: &quot;test title&quot;, &quot;content&quot;: &quot;test content&quot;, &quot;author&quot;: 1}</code></li>
<li>회원 계정으로 인증하면 201(Created) 응답</li>
</ul>
</li>
</ul>
</li>
<li>참고: <code>package.json</code>은 Thunder Client에서 요청 본문(request body)을 저장하는 데 사용된다.</li>
</ul>
<ol start="2">
<li>회원 게시판(/blog) 테스트</li>
</ol>
<ul>
<li><a href="http://127.0.0.1:8000/blog/post/">http://127.0.0.1:8000/blog/post/</a><ul>
<li>GET 요청 (비회원): 비회원은 접근 권한이 없으므로 401(Unauthorized) 응답</li>
</ul>
</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/jhp21c/post/5dbf548a-a82c-4b4b-9b86-7846b8c55c47/image.png" />
<img alt="" src="https://velog.velcdn.com/images/jhp21c/post/197af2cc-881c-4768-b8dc-04c150fd8e51/image.png" /></p>
<h2 id="tdd">TDD</h2>
<p>thunder client로 테스트 했던 것을 코드 형태로 짜보았다. 보통의 경우 이런 테스트 코드는 직접 코드를 짜기 전에 작성한다.</p>
<p>규모가 어느정도 커지면 테스트 코드의 장점이 극대화되며 작은 경우는 안쓰는게 좋을 수 있다.</p>
<pre><code class="language-python"># blog &gt; tests.py
from django.test import TestCase
from rest_framework.test import APIClient
from django.contrib.auth.models import User
from blog.models import Post


class BlogTest(TestCase):
    def setUp(self):
        print(&quot;-- main app 테스트 BEGIN --&quot;)
        self.client = APIClient()
        self.user = User.objects.create_user(
            username=&quot;hojun&quot;,
            password=&quot;dlghwns1234!&quot;,
        )
        self.user.save()

        self.blog = Post.objects.create(
            title=&quot;test blog title setup&quot;,
            content=&quot;test blog content setup&quot;,
            author=self.user,
        )
        self.blog.save()

        print(&quot;-- main app 테스트 END --&quot;)

    def test_blog_read(self):
        &quot;&quot;&quot;
        blog list Read 가능 테스트
        &quot;&quot;&quot;
        print(&quot;-- blog read 테스트 BEGIN --&quot;)
        print(&quot;-- 비회원 읽기 테스트 --&quot;)
        response = self.client.get(&quot;/blog/post/&quot;)
        self.assertEqual(response.status_code, 403)

        print(&quot;-- 회원 읽기 테스트 --&quot;)
        self.client.login(username=&quot;hojun&quot;, password=&quot;dlghwns1234!&quot;)
        response = self.client.get(&quot;/blog/post/&quot;)
        self.assertEqual(response.status_code, 200)
        print(&quot;--// blog read 테스트 END --&quot;)

    def test_blog_create(self):
        &quot;&quot;&quot;
        blog Create 가능 테스트
        &quot;&quot;&quot;
        print(&quot;-- blog create 테스트 BEGIN --&quot;)
        print(&quot;-- 비회원 작성 테스트 --&quot;)
        response = self.client.post(
            &quot;/blog/post/&quot;,
            {
                &quot;title&quot;: &quot;test blog title create&quot;,
                &quot;content&quot;: &quot;test blog content create&quot;,
                &quot;author&quot;: self.user.id,
            },
            format=&quot;json&quot;,
        )
        self.assertEqual(response.status_code, 403)

        print(&quot;-- 회원 작성 테스트 --&quot;)
        self.client.login(username=&quot;hojun&quot;, password=&quot;dlghwns1234!&quot;)
        response = self.client.post(
            &quot;/blog/post/&quot;,
            {
                &quot;title&quot;: &quot;test blog title create&quot;,
                &quot;content&quot;: &quot;test blog content create&quot;,
                &quot;author&quot;: self.user.id,
            },
            format=&quot;json&quot;,
        )
        self.assertEqual(response.status_code, 201)
        posts = Post.objects.all()
        for i in posts:
            print(i.title)
        print(&quot;--// blog create 테스트 END --&quot;)</code></pre>
<p><code>setUp()</code> 메서드에서 테스트에 필요한 사용자와 블로그 게시글을 미리 생성하여 초기화한다.</p>
<p><code>APIClient</code>는 실제 HTTP 요청을 보내고 응답을 받을 수 있다.</p>
<p>마지막의 반복문은 명확하게 보기 위함이다.</p>
<pre><code class="language-python"># notice &gt; tests.py
from django.test import TestCase
from rest_framework.test import APIClient
from django.contrib.auth.models import User
from notice.models import Notice as Post


class NoticeTest(TestCase):
    def setUp(self):
        print(&quot;-- main app 테스트 BEGIN --&quot;)
        self.client = APIClient()
        self.user = User.objects.create_user(
            username=&quot;hojun&quot;,
            password=&quot;dlghwns1234!&quot;,
        )
        self.user.save()

        self.notice = Post.objects.create(
            title=&quot;test notice title setup&quot;,
            content=&quot;test notice content setup&quot;,
            author=self.user,
        )
        self.notice.save()

        print(&quot;-- main app 테스트 END --&quot;)

    def test_notice_read(self):
        &quot;&quot;&quot;
        notice list Read 가능 테스트
        &quot;&quot;&quot;
        print(&quot;-- notice read 테스트 BEGIN --&quot;)
        print(&quot;-- 비회원 읽기 테스트 --&quot;)
        response = self.client.get(&quot;/notice/post/&quot;)
        self.assertEqual(response.status_code, 200)

    def test_notice_create(self):
        &quot;&quot;&quot;
        notice Create 가능 테스트
        &quot;&quot;&quot;
        print(&quot;-- notice create 테스트 BEGIN --&quot;)
        print(&quot;-- 비회원 작성 테스트 --&quot;)
        response = self.client.post(
            &quot;/blog/post/&quot;,
            {
                &quot;title&quot;: &quot;test blog title create&quot;,
                &quot;content&quot;: &quot;test blog content create&quot;,
                &quot;author&quot;: self.user.id,
            },
            format=&quot;json&quot;,
        )
        self.assertEqual(response.status_code, 403)

        print(&quot;-- 회원 작성 테스트 --&quot;)
        self.client.login(username=&quot;hojun&quot;, password=&quot;dlghwns1234!&quot;)
        response = self.client.post(
            &quot;/blog/post/&quot;,
            {
                &quot;title&quot;: &quot;test blog title create&quot;,
                &quot;content&quot;: &quot;test blog content create&quot;,
                &quot;author&quot;: self.user.id,
            },
            format=&quot;json&quot;,
        )
        self.assertEqual(response.status_code, 201)
        posts = Post.objects.all()
        for i in posts:
            print(i.title)
        print(&quot;--// notice create 테스트 END --&quot;)
</code></pre>