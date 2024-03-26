<p>DRF를 활용하여 간단한 인스타 클론 코딩을 해보자.</p>
<h2 id="url-설계">URL 설계</h2>
<pre><code class="language-python"># api
GET     /api/schema/
POST    /api/token/ # 토큰 발급 - 모두 공개
POST    /api/token/refresh/ # 리프레시 토큰 발급(갱신) - 로그인한 사용자


# posts
GET     /posts/ # 포스트리스트 - 로그인한 사용자
GET     /posts/{id}/ # 포스트 상세(댓글, 좋아요) - 로그인한 사용자
GET     /posts/{post_id}/comments/ # 해당 포스트에 댓글 리스트 - 로그인한 사용자
POST    /posts/{post_id}/like/ # 좋아요 생성 - 로그인한 사용자
DELETE  /posts/{post_id}/like/ # 좋아요 삭제 - 로그인한 사용자
POST    /posts/comments/ # 댓글 생성 - 로그인한 사용자
POST    /posts/write/ # 포스트 작성 - 로그인한 사용자


# user
POST    /users/signup/ # 회원 가입 - 모두 공개
GET     /users/user/{id}/ # 유저 프로필 - 로그인한 사용자에게 전체 공개</code></pre>
<h2 id="모델-설계">모델 설계</h2>
<pre><code class="language-python"># DB 설계

# users &gt; models.py
class User(AbstractUser):
    bio = models.TextField(blank=True)
    profile_picture = models.ImageField(upload_to='profile_pics/', blank=True)


# posts &gt; models.py
class Post(models.Model):
    author = models.ForeignKey(User, on_delete=models.CASCADE, related_name='posts')
    caption = models.TextField()
    image = models.ImageField(upload_to='post_images/')
    created_at = models.DateTimeField(auto_now_add=True)


class Comment(models.Model):
    post = models.ForeignKey(Post, on_delete=models.CASCADE, related_name='comments')
    author = models.ForeignKey(User, on_delete=models.CASCADE, related_name='comments')
    text = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)


class Like(models.Model):
    post = models.ForeignKey(Post, on_delete=models.CASCADE, related_name='likes')
    user = models.ForeignKey(User, on_delete=models.CASCADE, related_name='likes')
    created_at = models.DateTimeField(auto_now_add=True)</code></pre>
<p><code>bio</code>는 요약문? 같은 느낌이라 생각하면 된다. <code>Caption</code>은 본문이다.</p>
<h2 id="프로젝트-준비">프로젝트 준비</h2>
<h3 id="설치-및-앱-추가">설치 및 앱 추가</h3>
<pre><code class="language-shell">pip install django
pip install djangorestframework
pip install djangorestframework-simplejwt 
pip install drf-spectacular
pip install django-cors-headers 
pip install setuptools</code></pre>
<p>이후 사용할 앱을 선언한다.</p>
<h3 id="세팅">세팅</h3>
<pre><code class="language-python">ALLOWED_HOSTS = ['*']

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    # custom app
    'posts',
    'users',
    # install app
    'rest_framework',
    'rest_framework_simplejwt',
    'drf_spectacular',
    'corsheaders',
]

##

LANGUAGE_CODE = 'ko-kr'

TIME_ZONE = 'Asia/Seoul'

##

MEDIA_URL = '/media/'
MEDIA_ROOT = BASE_DIR / 'media'

##

REST_FRAMEWORK = {
    'DEFAULT_PERMISSION_CLASSES': [
        'rest_framework.permissions.IsAuthenticated',
    ],
    'DEFAULT_AUTHENTICATION_CLASSES': (
        'rest_framework_simplejwt.authentication.JWTAuthentication',
    ),
    'DEFAULT_SCHEMA_CLASS': 'drf_spectacular.openapi.AutoSchema',
}

##

# 개발할 때에는 5분으로 해놓고 refresh 토큰이 제대로 작동되는지 확인 필요.
from datetime import timedelta

SIMPLE_JWT = {
    'ACCESS_TOKEN_LIFETIME': timedelta(minutes=60),  # 액세스 토큰 만료 시간을 60분으로 설정
    # ... 기타 SIMPLE_JWT 설정 ...
}</code></pre>
<p><code>'DEFAULT_PERMISSION_CLASSES'</code>를 설정함으로서 인증된 사용자만 API를 사용하게 하였다. </p>
<p><code>DEFAULT_AUTHENTICATION_CLASSES</code> 는 JWT를 사용하여 사용자 인증을 처리하겠다는 이야기이다. </p>
<p><code>DEFAULT_SCHEMA_CLASS</code> 를 설정하여 API의 명세서를 자동으로 생성해주고자 하였다.</p>
<h2 id="프로젝트-시작">프로젝트 시작</h2>
<h3 id="urls-정의">urls 정의</h3>
<pre><code class="language-python"># project &gt; urls.py

from django.contrib import admin
from django.urls import path, include
from rest_framework_simplejwt.views import (
    TokenObtainPairView,
    TokenRefreshView,
)
from drf_spectacular.views import (
    SpectacularAPIView, 
    SpectacularRedocView, 
    SpectacularSwaggerView
)
from django.conf import settings
from django.conf.urls.static import static

urlpatterns = [
    path('users/', include('users.urls')),
    path('posts/', include('posts.urls')),
    path('admin/', admin.site.urls), # admin 페이지(일반적인 drf에서 사용하진 않습니다.)
    path('api/token/', TokenObtainPairView.as_view(), name='token_obtain_pair'), # jwt 토큰 발급
    path('api/token/refresh/', TokenRefreshView.as_view(), name='token_refresh'), # jwt 토큰 갱신
    path('api/schema/', SpectacularAPIView.as_view(), name='schema'), # 스키마
    path('api/schema/swagger-ui/', SpectacularSwaggerView.as_view(url_name='schema'), name='swagger-ui'), # 스웨거
    path('api/schema/redoc/', SpectacularRedocView.as_view(url_name='schema'), name='redoc'), # 문서화
]

urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
</code></pre>
<p><code>users</code> <code>posts</code> <code>admin</code>을 제외하고 나머지 모두는 그 기능을 사용하기 위해 필수적으로 저 경로로 사용해야 한다.</p>
<p><code>DRF</code>에서는 <code>admin</code>을 보통 정의하지 않는데 , <code>admin</code>은 주로 DB 모델의 생성, 수정, 삭제와 같은 관리 작업을 위해 사용된다. </p>
<p>그렇지만 <code>DRF</code>는 주로 <code>API</code> 개발에 집중되어 있기에 이러한 <code>admin</code> 인터페이스와는 직접적인 연관이 없기 때문이다.</p>
<h3 id="user-app-작성">user app 작성</h3>
<ul>
<li>model<pre><code class="language-python"># users &gt; models.py
</code></pre>
</li>
</ul>
<p>from django.contrib.auth.models import AbstractUser
from django.db import models</p>
<h1 id="abstractuser와-abstractbaseuser의-차이점은">abstractuser와 abstractbaseuser의 차이점은</h1>
<h1 id="abstractuser는-username-password-first_name-last_name-email-is_staff-is_active-is_superuser-last_login-date_joined를-기본으로-가지고-있고">abstractuser는 username, password, first_name, last_name, email, is_staff, is_active, is_superuser, last_login, date_joined를 기본으로 가지고 있고,</h1>
<h1 id="abstractbaseuser는-password-last_login-is_superuser-username을-기본으로-가지고-있습니다">abstractbaseuser는 password, last_login, is_superuser, username을 기본으로 가지고 있습니다.</h1>
<h1 id="초급자에게-권하는-방법은-abstractuser를-사용하는-것입니다-abstractbaseuser는-너무-많은-것을-구현해야-하기-때문입니다">초급자에게 권하는 방법은 abstractuser를 사용하는 것입니다. abstractbaseuser는 너무 많은 것을 구현해야 하기 때문입니다.</h1>
<p>class User(AbstractUser):
    # 추가 필드 예시
    bio = models.TextField(blank=True) # 사용자 기분 정보
    profile_picture = models.ImageField(upload_to='profile_pics/', blank=True)</p>
<pre><code>
- url</code></pre><h1 id="users--urlspy-생성-후-저장">users &gt; urls.py (생성 후 저장)</h1>
<p>from django.urls import path
from .views import (
    UserCreateView,
    UserDetailView,
)</p>
<h1 id="로그인은-jwt를-사용하므로-회원가입과-유저-정보-조회만-구현">로그인은 JWT를 사용하므로, 회원가입과 유저 정보 조회만 구현</h1>
<p>urlpatterns = [
    path('signup/', UserCreateView.as_view(), name='signup'),
    path('profile/<a href="">int:pk</a>/', UserDetailView.as_view(), name='user-detail'),
]</p>
<pre><code>
- view
```python
# users &gt; views.py

# Basic Django Modules
from django.contrib.auth import get_user_model

# Rest Framework Modules
from rest_framework import generics
from rest_framework import permissions

# Models
from .serializers import UserSerializer

User = get_user_model()


class UserCreateView(generics.CreateAPIView):
    # CreateAPIView는 post요청을 받아서 새로운 user를 만들어주는 역할을 합니다.
    # CreateAPIView는 post요청을 받으면 201 Created를 반환합니다.
    # CreateAPIView는 get, put, patch, delete 등 허용되지 않은 요청을 받으면 405 Method Not Allowed를 반환합니다.
    queryset = User.objects.all()
    serializer_class = UserSerializer
    # 모든 사용자 접근 가능
    # permission_classes = [permissions.IsAuthenticated, ]
    # permission_classes = [permissions.IsAdminUser, ]
    # permission_classes = [permissions.IsAuthenticatedOrReadOnly, ]
    # 모든 사용자가 접근 가능하도록 설정합니다.
    # settings.py에 REST_FRAMEWORK의 DEFAULT_PERMISSION_CLASSES를 덮어쓰기 하기 위해서는 아래와 같이 설정합니다.
    permission_classes = [permissions.AllowAny]


class UserDetailView(generics.RetrieveAPIView):
    queryset = User.objects.all()
    serializer_class = UserSerializer</code></pre><p>뷰에서 settings.py에 선언한 권한 설정을 변경할 수도 있다.</p>
<ul>
<li>serializer<pre><code class="language-python"># users &gt; serializers.py (생성 후 저장)
</code></pre>
</li>
</ul>
<p>from rest_framework import serializers
from django.contrib.auth import get_user_model</p>
<p>User = get_user_model()</p>
<p>class UserSerializer(serializers.ModelSerializer):
    class Meta:
        model = User
        fields = ('id', 'username', 'email', 'password', 'bio', 'profile_picture')
        extra_kwargs = {'password': {'write_only': True}}</p>
<pre><code>def create(self, validated_data):
    user = User.objects.create_user(**validated_data)
    return user</code></pre><pre><code>
`get_user_model` 함수는 프로젝트 설정에서 지정된 사용자 모델을 반환한다. 이를 통해 User 모델에 독립적으로 접근이 가능하다.

직렬화/역직렬화를 위한 코드이다. fields부분은 `all`로 하지 말고 위 코드처럼 작성하자.

`extra_kwargs`로 비밀번호 필드에 대한 추가 설정을 하였다. 

`write_only`는 API로 사용자 데이터를 생성하거나 업데이트 시에는 비밀번호 필드에 값을 제공할 수 있지만, 정보를 조회하는 과정에서는 비밀번호 필드가 응답 데이터에 포함되지 않도록 하는 코드이다.

settings.py에 `AUTH_USER_MODEL = 'users.User'`를 추가하면 위에서 `get_user_model`을 했을 때 직접 정의한 유저 모델을 사용하겠다는 의미이다.

`AUTH_USER_MODEL = 'users.User'`를 settings.py에 추가하자.

### posts app 작성
users app을 작성할때와 비슷하게 흘러간다.

```python
# posts &gt; models.py

from django.db import models
from users.models import User  # User 모델 임포트

class Post(models.Model):
    author = models.ForeignKey(User, on_delete=models.CASCADE, related_name='posts')
    caption = models.TextField()
    image = models.ImageField(upload_to='post_images/')
    created_at = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        # self.author와 self.author.username은 같은 값을 반환합니다.
        # 다만 시리얼라이저에서는 self.author.username을 사용해야 합니다.
        # 시리얼라이저에서 author는 pk 값을 보여줍니다. 
        return f'{self.author} - {self.caption[:10]}'


class Comment(models.Model):
    post = models.ForeignKey(Post, on_delete=models.CASCADE, related_name='comments')
    author = models.ForeignKey(User, on_delete=models.CASCADE, related_name='comments')
    text = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return f'{self.author.username} - {self.text}'


class Like(models.Model):
    post = models.ForeignKey(Post, on_delete=models.CASCADE, related_name='likes')
    user = models.ForeignKey(User, on_delete=models.CASCADE, related_name='likes')
    created_at = models.DateTimeField(auto_now_add=True)

    class Meta:
        unique_together = ('post', 'user')

    def __str__(self):
        return f'{self.user.username} likes {self.post.caption}'
</code></pre><p>사진을 사용하므로 pillow 라이브러리를 설치해주어야 한다.
<code>pip install pillow</code></p>
<p><code>post = models.ForeignKey(Post, on_delete=models.CASCADE, related_name='comments')</code> 를 대표로 살펴보면, 역참조시 사용할 이름을 설정해 주었다. </p>
<p>이는 <code>Post</code> 모델에서 해당 <code>Post</code>에 속한 <code>Comment</code> 객체들을 가져올 때 <code>post.comments.all()</code> 과 같이 사용하게끔 한다.</p>
<pre><code class="language-python"># posts &gt; urls.py (생성 후 저장)

from django.urls import path
from .views import (
    PostListView,
    PostDetailView,
    PostCreateView,
    CommentCreateView,
    CommentListView,
    LikeView,
)

urlpatterns = [
    path('list/', PostListView.as_view(), name='post-list'), # 게시물 리스트
    path('&lt;int:pk&gt;/', PostDetailView.as_view(), name='post-detail'), # 게시물 상세보기
    path('create/', PostCreateView.as_view(), name='post-create'), # 게시물 생성
    path('comments/', CommentCreateView.as_view(), name='comment-create'), # 댓글 생성
    path('&lt;int:post_id&gt;/comments/', CommentListView.as_view(), name='comment-list'), # 댓글 리스트
    path('&lt;int:post_id&gt;/like/', LikeView.as_view(), name='post-like'),# 게시물에 대한 좋아요
]</code></pre>
<p>CBV를 사용하였다.</p>
<pre><code class="language-python"># posts &gt; views.py

# Basic Django Modules
from django.shortcuts import get_object_or_404

# Rest Framework Modules
from rest_framework import generics, views, status, response, permissions
from rest_framework.permissions import IsAuthenticated

# Models
from .serializers import PostSerializer, CommentSerializer
from .models import Comment, Post, Like


class PostListView(generics.ListAPIView):
    queryset = Post.objects.all()
    serializer_class = PostSerializer
    permission_classes = [permissions.IsAuthenticated]  # 인증된 사용자만 접근 가능


class PostDetailView(generics.RetrieveAPIView):
    queryset = Post.objects.all()
    serializer_class = PostSerializer


class PostCreateView(generics.CreateAPIView):
    queryset = Post.objects.all()
    serializer_class = PostSerializer
    permission_classes = [IsAuthenticated]  # 인증된 사용자만 접근 가능

    def perform_create(self, serializer):
        serializer.save(author=self.request.user)


class CommentCreateView(generics.CreateAPIView):
    queryset = Comment.objects.all()
    serializer_class = CommentSerializer
    permission_classes = [permissions.IsAuthenticated]

    def perform_create(self, serializer):
        serializer.save(author=self.request.user)


class CommentListView(generics.ListAPIView):
    queryset = Comment.objects.all()
    serializer_class = CommentSerializer

    def get_queryset(self):
        return self.queryset.filter(post_id=self.kwargs['post_id'])


class LikeView(views.APIView):
    permission_classes = [permissions.IsAuthenticated]

    def post(self, request, post_id):
        post = get_object_or_404(Post, id=post_id)
        like, created = Like.objects.get_or_create(post=post, user=request.user)

        # 좋아요를 검색한 후 좋아요가 없으면 생성(like 생성된 객체, created가 생성 여부 판단)
        # created == True : 좋아요가 클릭이 안되어 있어서 새로 생성했다.
        # created == False : 좋아요가 클릭이 되어서 생성하지 못했다.

        if not created:
            # 이미 좋아요가 존재하는 경우, 409 Conflict 반환
            return response.Response(status=status.HTTP_409_CONFLICT)

        # 좋아요가 생성되었으면 201 응답
        return response.Response(status=status.HTTP_201_CREATED)

    def delete(self, request, post_id):
        post = get_object_or_404(Post, id=post_id) # 게시물이 존재하지 않으면 404 에러
        like = get_object_or_404(Like, post=post, user=request.user) # 좋아요가 존재하지 않으면 404 에러
        like.delete()
        return response.Response(status=status.HTTP_204_NO_CONTENT) # 좋아요가 삭제되었으면 204 응답
</code></pre>
<p><code>APIView</code>를 사용하였으며 기존의 모놀리식 코드와 다른점은 <code>serializer</code>가 추가되어 <code>JSON</code> 형식으로 통신한다는 점이다.</p>
<p>그리고 Form.py를 사용하지 않는다는 것이 또다른 차이점이다.</p>
<pre><code class="language-python"># posts &gt; serializers.py

from rest_framework import serializers
from .models import Comment, Post, Like

class CommentSerializer(serializers.ModelSerializer):
    author_username = serializers.SerializerMethodField() # 댓글에 대한 유저의 이름을 보여주기 위해 추가

    class Meta:
        model = Comment
        fields = ['id', 'post', 'author', 'author_username', 'text', 'created_at']
        read_only_fields = ['author']

    def get_author_username(self, obj):
        '''
        get_author_username 함수가 serializers.SerializerMethodField()의 반환값이 되어author_username 에 삽입
        Django REST Framework는 해당 필드에 대한 값을 얻기 위해 get_&lt;field_name&gt; 형식의 메서드를 호출
        '''
        return obj.author.username  # 댓글 작성자의 사용자 이름 반환

    def create(self, validated_data):
        validated_data['author'] = self.context['request'].user
        return super().create(validated_data)

class PostSerializer(serializers.ModelSerializer):
    author_username = serializers.SerializerMethodField()
    comments = CommentSerializer(many=True, read_only=True)
    likesCount = serializers.IntegerField(source='likes.count', read_only=True)
    isLiked = serializers.SerializerMethodField()

    class Meta:
        model = Post
        fields = ['id', 'author', 'author_username', 'caption', 'image', 'created_at', 'comments', 'likesCount', 'isLiked']
        read_only_fields = ['author']

    def get_isLiked(self, obj):
        user = self.context['request'].user
        if user.is_authenticated:
            # Like 모델을 사용하여 현재 사용자가 게시물에 좋아요를 눌렀는지 확인
            return Like.objects.filter(post=obj, user=user).exists()
        return False

    def get_author_username(self, obj):
        return obj.author.username  # 댓글 작성자의 사용자 이름 반환

    def create(self, validated_data):
        # 현재 요청을 보낸 사용자를 게시물의 저자로 설정
        validated_data['author'] = self.context['request'].user
        return super().create(validated_data)</code></pre>
<ol>
<li>CommentSerializer</li>
</ol>
<ul>
<li><code>create</code> 메서드를 오버라이딩하여, 새로운 댓글 생성 시 현재 요청을 보낸 사용자를 <code>author</code>로 설정한다.</li>
<li><code>get_author_username</code> 메서드는 <code>author_username</code>이라는 필드의 값을 얻기 위한 메서드이다. 필드의 값은 달라질 수 있다.</li>
<li><code>get_author_username</code>의 필드는 위에 <code>author_username = serializers.SerializerMethodField()</code>로 선언하여 모델에 정의되지 않은 추가 데이터를 serializer에 추가시킨 후 사용한다.</li>
<li><code>SerializerMethodField</code>는 모델에 정의되지 않은 데이터를 <code>Serializer</code>에 포함시킬 때 유용하며, 데이터 가공이나 계산된 값을 제공하는 데에도 사용될 수 있다.</li>
</ul>
<ol start="2">
<li>PostSerializer</li>
</ol>
<ul>
<li><code>create</code> 메서드를 오버라이딩하여, 새로운 게시물 생성 시 현재 요청을 보낸 사용자를 <code>author</code>로 설정한다.</li>
</ul>
<p>이후 마이그레이트를 진행한다. API 명세서를 확인할 수 있다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhp21c/post/964b64fa-3734-4503-8245-e9f6ed90451b/image.png" /></p>
<h2 id="fe-간이-서버로-통신">FE 간이 서버로 통신</h2>
<h3 id="기획">기획</h3>
<pre><code class="language-python">1. index.html: 메인 화면. 게시물 피드를 보여주고, 사용자가 다른 페이지로 이동할 수 있는 네비게이션 바를 포함
2. login.html: 로그인 화면.
3. register.html: 회원가입 화면
4. profile.html: 개별 사용자 프로필 화면. 사용자의 정보, 게시한 게시물 목록 등을 표시.
5. post.html: 게시물 상세 보기 화면. 특정 게시물의 상세 내용과 댓글 등 게시.
6. create-post.html: 새 게시물 생성 화면.
7. edit-profile.html: 프로필 편집 화면.</code></pre>
<h3 id="세팅-1">세팅</h3>
<p><code>pip install django-cors-headers</code> : CORS </p>
<pre><code class="language-python"># settings.py

INSTALLED_APPS = [
    # ... 기존 앱
    'corsheaders',  # 추가
]

MIDDLEWARE = [
    # ... 기존 미들웨어
    'corsheaders.middleware.CorsMiddleware',  # 추가
    'django.middleware.common.CommonMiddleware',  # 이 미들웨어 바로 앞에 추가
]

# 개발 중에는 모든 도메인에서의 CORS를 허용
CORS_ALLOW_ALL_ORIGINS = True</code></pre>
<ul>
<li>관리자 계정 생성(테스트 용도)</li>
<li>admin 페이지에 모델 연결</li>
<li>html 파일 생성</li>
<li>테스트</li>
</ul>