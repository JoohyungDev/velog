<p>JWT를 사용한 로그인 프로세스를 알아보자.</p>
<h2 id="jwt">JWT?</h2>
<h3 id="정의">정의</h3>
<p>JSON 형식의 토큰으로, 사용자의 인증 정보를 안전하게 전송하는 데에 사용된다. 세 부분으로 구성되어 있다.</p>
<ul>
<li>헤더 : 토큰 유형과 해싱 알고리즘 정보를 담고 있음</li>
<li>내용 : 토큰에 담길 실제 데이터로, 일반적으로 사용자 ID, 만료 시간 등의 정보를 포함</li>
<li>서명 : 헤더와 페이로드를 비밀키로 해싱하여 생성된 값, 토큰의 무결성을 보장</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/jhp21c/post/ca33728f-76a4-4647-9e25-c9ef65bd4823/image.png" /></p>
<h3 id="사용하는-이유">사용하는 이유</h3>
<ul>
<li>Stateless : 서버에 세션 정보를 저장할 필요가 없어 확장성이 좋다.</li>
<li>Portable : 토큰 자체에 인증 정보가 포함되어 있어 다양한 도메인/서비스에서 사용할 수 있다.</li>
<li>Self-contained : 토큰 크기가 작고, 필요한 정보를 직접 담고 있어 추가적인 외부 데이터 소스가 필요 없다.</li>
</ul>
<h2 id="로그인-실습">로그인 실습</h2>
<h3 id="사전-준비">사전 준비</h3>
<pre><code class="language-shell"># 폴더 구성 및 설치
mkdir jwt
cd jwt

python -m venv venv
# source venv/Scripts/activate
.\venv\Scripts\activate

pip install -r requirements.txt
django-admin startproject config .
python manage.py startapp accounts</code></pre>
<ul>
<li><code>djangorestframework</code> : RESTful API 개발</li>
<li><code>dj-rest-auth</code> : 인증 및 사용자 관리 구현(로그인, 회원가입 등)</li>
<li><code>django-allauth</code> : 다양한 인증 및 회원가입 옵션을 제공</li>
<li><code>djangorestframework-simplejwt</code> : JWT 인증 구현</li>
</ul>
<h3 id="사용자-정의-모델-생성-및-관리">사용자 정의 모델 생성 및 관리</h3>
<p>프로젝트를 진행하다보면 Django의 기본 유저 모델인 <code>auth.User</code>를 사용할 수도 있지만 추가적으로 커스텀하고자 하는 욕구가 있을 수 있다.</p>
<p>이때 사용하는 것이 managers.py이고, 사용자 정의 매니저를 생성하여 모델의 객체 생성 및 관리 로직을 변경할 수 있다.</p>
<pre><code class="language-python"># accounts &gt; managers.py

from django.contrib.auth.base_user import BaseUserManager
from django.utils.translation import gettext_lazy as _


class CustomUserManager(BaseUserManager):

    def create_user(self, email, password, **extra_fields):
        if not email:
            raise ValueError(_(&quot;The Email must be set&quot;))
        email = self.normalize_email(email)
        user = self.model(email=email, **extra_fields)
        user.set_password(password)
        user.save()
        return user

    def create_superuser(self, email, password, **extra_fields):
        extra_fields.setdefault(&quot;is_staff&quot;, True)
        extra_fields.setdefault(&quot;is_superuser&quot;, True)
        extra_fields.setdefault(&quot;is_active&quot;, True)

        if extra_fields.get(&quot;is_staff&quot;) is not True:
            raise ValueError(_(&quot;Superuser must have is_staff=True.&quot;))
        if extra_fields.get(&quot;is_superuser&quot;) is not True:
            raise ValueError(_(&quot;Superuser must have is_superuser=True.&quot;))
        return self.create_user(email, password, **extra_fields)</code></pre>
<ul>
<li><code>create_user(self, email, password, **extra_fields)</code> : 일반 사용자를 생성하는데에 사용되며 <code>email</code>과 <code>password</code>를 필수 입력 필드로 지정하였다. 
<code>extra_fields</code>에는 추가적인 사용자 필드가 포함되며 메서드 내에서 email을 정규화하고, 사용자 모델 인스턴스를 생성한 후, 비밀번호를 설정하고 저장하는 코드이다.</li>
</ul>
<ul>
<li><code>create_superuser(self, email, password, **extra_fields)</code> : 관리자를 생성하는 데 사용하며 <code>extra_fields</code>를 자동으로 True로 설정한다. 최종적으로 <code>create_user</code> 메서드를 호출하여 슈퍼유저를 생성한다.</li>
</ul>
<h3 id="모델-정의">모델 정의</h3>
<pre><code class="language-python"># accounts &gt; models.py

from django.db import models
from django.contrib.auth.models import AbstractUser
from django.utils.translation import gettext_lazy as _

from .managers import CustomUserManager

GENDER_CHOICES = (
    ('male', '남자'),
    ('female', '여자'),
)

class CustomUser(AbstractUser):
    username = None
    email = models.EmailField(_('email address'), unique=True)

    USERNAME_FIELD = 'email'
    REQUIRED_FIELDS = []

    objects = CustomUserManager()

    gender = models.CharField(max_length=6, choices=GENDER_CHOICES, blank=True)
    date_of_birth = models.DateField(blank=True, null=True)


    def __str__(self):
        return self.email</code></pre>
<p>User를 상속받는 방법이 2가지가 있는데 초급자의 경우는 <code>AbstractUser</code>를 상속받는 것이 좋다고 한다. 나머지 1개는 <code>BaseUser</code>인데 처음부터 전부 만들어야 하기 때문이다.</p>
<p><code>objects = CustomUserManager()</code> 는 이전에 정의한 <code>CustomUserManager</code>를 사용자 모델의 객체 관리자로 설정한다는 의미이다.</p>
<p>유저 이름 대신 이메일을 사용하도록 하고 있으며, 성별 선택 옵션, 생년월일을 추가해 주었다.</p>
<h3 id="세팅-수정settingspy">세팅 수정(settings.py)</h3>
<p>accounts 앱을 추가하고, <code>AUTH_USER_MODEL = &quot;accounts.CustomUser&quot;</code> 라는 코드를 추가하여 사용자 정의 User를 사용할 것임을 명시한다.</p>
<p>이후, 마이그레이트를 진행한다.</p>
<h3 id="추가-세팅-수정">추가 세팅 수정</h3>
<ul>
<li>admin 페이지에 모델 등록<pre><code class="language-python"># accounts &gt; admin.py
</code></pre>
</li>
</ul>
<p>from django.contrib import admin
from accounts.models import CustomUser</p>
<p>admin.site.register(CustomUser)</p>
<pre><code>
- 설치된 라이브러리 추가
```python
INSTALLED_APPS = [
...
    # 설치한 라이브러리들
    'rest_framework',
    'rest_framework.authtoken',
    'dj_rest_auth',
    'django.contrib.sites',
    'allauth',
    'allauth.account',
    'allauth.socialaccount',
    'dj_rest_auth.registration',
...
]</code></pre><pre><code class="language-python"># JWT 설정 추가
# settings.py 맨 아래
from datetime import timedelta

# dj-rest-auth
REST_USE_JWT = True # JWT 사용 여부
JWT_AUTH_COOKIE = 'my-app-auth' # 호출할 Cookie Key 값
JWT_AUTH_REFRESH_COOKIE = 'my-refresh-token' # Refresh Token Cookie Key 값

# django-allauth
SITE_ID = 1 # 해당 도메인 id
ACCOUNT_UNIQUE_EMAIL = True # User email unique 사용 여부
ACCOUNT_USER_MODEL_USERNAME_FIELD = None # 사용자 이름 필드 지정
ACCOUNT_USERNAME_REQUIRED = False # User username 필수 여부
ACCOUNT_EMAIL_REQUIRED = True # User email 필수 여부
ACCOUNT_AUTHENTICATION_METHOD = 'email' # 로그인 인증 수단
ACCOUNT_EMAIL_VERIFICATION = 'none' # email 인증 필수 여부

SIMPLE_JWT = {
    'ACCESS_TOKEN_LIFETIME': timedelta(minutes=60),  # AccessToken 유효 기간 설정
    'SLIDING_TOKEN_REFRESH_LIFETIME': timedelta(days=1),  # RefreshToken 유효 기간 설정
}

REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework_simplejwt.authentication.JWTAuthentication',
    ],
}</code></pre>
<p><code>ACCESS</code> 토큰의 지속 시간을 테스트용으로 5분으로 바꾼후 정상 작동하면 60분으로 바꾸는 식으로 한다고 한다.</p>
<p><code>REFRESH</code> 토큰의 경우 재인증시 필요한 토큰인데 시간을 줄이면 더 높은 보안을 유지할 수 있다.</p>
<p>항상 이렇게 전부 정의할 필요는 없고 바꾸고자 하는 부분만 정의하면 된다. </p>
<p>이후 마이그레이트를 진행한다.</p>
<h3 id="url-정의">URL 정의</h3>
<p>config앱과 accounts앱의 url을 정의한다.</p>
<pre><code class="language-python"># config &gt; urls.py

from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path(&quot;admin/&quot;, admin.site.urls),
    path(&quot;accounts/&quot;, include(&quot;accounts.urls&quot;)),
]</code></pre>
<pre><code class="language-python"># accounts &gt; urls.py

from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path(&quot;join/&quot;, include(&quot;dj_rest_auth.registration.urls&quot;)),
    path(&quot;&quot;, include(&quot;dj_rest_auth.urls&quot;)),
]</code></pre>
<p>이 부분은 직접 선언한 것이 아닌 dj_rest_auth에서 제공하는 회원가입, 인증 관련 URL을 포함시킨 것이다.</p>
<h3 id="서버-실행">서버 실행</h3>
<p>만약, 서버 실행 후 <code>No module named 'pkg_resources'</code>에러를 마주친다면 <code>pip install --upgrade setuptools</code>와 <code>pip install --upgrade distribute</code>를 해주자.</p>
<p>이메일로 회원가입, 로그인 시 정상 작동함을 볼 수 있다.</p>
<h3 id="accounts앱-정의">accounts앱 정의</h3>
<p>accounts앱의 url과 view를 정의해야 한다. </p>
<pre><code class="language-python"># accounts &gt; urls.py

from django.contrib import admin
from django.urls import path, include
from .views import example_view

urlpatterns = [
    path(&quot;test/&quot;, example_view),
    path(&quot;join/&quot;, include(&quot;dj_rest_auth.registration.urls&quot;)),
    path(&quot;&quot;, include(&quot;dj_rest_auth.urls&quot;)),
]</code></pre>
<p><code>accounts/test</code> 경로로 접속하면 <code>example_view</code> 함수가 실행된다.</p>
<pre><code class="language-python"># accounts &gt; views.py

from rest_framework.decorators import api_view, permission_classes
from rest_framework.permissions import IsAuthenticated
from rest_framework.response import Response


@api_view([&quot;GET&quot;])
@permission_classes([IsAuthenticated])
def example_view(request):
    # request.user는 인증된 사용자의 정보를 담고 있습니다.
    print(request.data)
    content = {&quot;message&quot;: &quot;Hello, World!&quot;, &quot;user&quot;: str(request.user)}
    return Response(content)</code></pre>
<p><code>GET</code> 요청이고 인증된 사용자라면 응답 데이터를 <code>content</code>라는 딕셔너리로 생성하고 <code>Response</code>클래스를 사용하여 응답을 JSON 형식으로 반환한다.</p>
<h3 id="cros-패키지-설치">CROS 패키지 설치</h3>
<pre><code class="language-shell">pip install django-cors-headers</code></pre>
<p>둘 이상의 도메인 간에 리소스를 공유해야 하는 경우에 사용하는 패키지이다.</p>
<p>Django 프로젝트의 <code>settings.py</code> 파일에 특정 도메인에서만 리소스에 접근할 수 있도록 허용하거나, 특정 HTTP 메서드에 대해서만 CORS를 허용하도록 설정할 수 있다.</p>
<pre><code class="language-python">INSTALLED_APPS = [
...
    'corsheaders',
...
]

MIDDLEWARE = [
...
    'corsheaders.middleware.CorsMiddleware',

]

CORS_ALLOW_ALL_ORIGINS = True</code></pre>
<p><code>CORS_ALLOW_ALL_ORIGINS</code> 설정은 모든 출처(origin)에서 Django 서버로의 CORS 요청을 허용한다는 뜻이다. 실제 서비스에서는 특정 출처만 허용하는 것이 좋다고 한다.</p>
<h3 id="로그인-구현">로그인 구현</h3>
<pre><code class="language-JavaScript"># accounts &gt; login.html

&lt;!DOCTYPE html&gt;
&lt;html lang=&quot;ko&quot;&gt;
&lt;head&gt;
    &lt;meta charset=&quot;UTF-8&quot;&gt;
    &lt;meta http-equiv=&quot;X-UA-Compatible&quot; content=&quot;IE=edge&quot;&gt;
    &lt;meta name=&quot;viewport&quot; content=&quot;width=device-width, initial-scale=1.0&quot;&gt;
    &lt;title&gt;login&lt;/title&gt;
&lt;/head&gt;
&lt;body&gt;
    &lt;form action=&quot;&quot; method=&quot;&quot;&gt;
        이메일 : &lt;input type=&quot;text&quot; name=&quot;email&quot;&gt;&lt;br&gt;
        패스워드 : &lt;input type=&quot;password&quot; name=&quot;password&quot;&gt;&lt;br&gt;
        &lt;input id=&quot;login&quot; type=&quot;button&quot; value=&quot;로그인&quot;&gt;
    &lt;/form&gt;
    &lt;script&gt;
        const login = document.querySelector('#login');
        login.addEventListener('click', (e) =&gt; {
            e.preventDefault(); // submit의 기본동작을 막는다.
            const email = document.querySelector('input[name=&quot;email&quot;]').value;
            const password = document.querySelector('input[name=&quot;password&quot;]').value;
            const data = {
                email: email,
                password: password
            }
            console.log(data)


            // fetch를 이용해서 서버에 POST 요청을 보낸다.
            fetch('http://127.0.0.1:8000/accounts/login/', {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json',
                },
                body: JSON.stringify(data)
            })
            .then(response =&gt; response.json())
            .then(data =&gt; {
                console.log(data)
            })

            // 로그인이 되는 로직 100줄

            // form을 없애는 코드
            // document.querySelector('form').remove();
            // document.write('이호준님 환영합니다!')

            // 또는 /home으로 리다이렉트 되는 코드
            // 리다이렉트 될 때 주의할 점: 토큰 값은 어딘가에 유지가 되고 있어야 로그인을 확인할 수 있습니다.
            // window.location.href = 'http://....
        })
    &lt;/script&gt;
&lt;/body&gt;
&lt;/html&gt;</code></pre>
<p>이메일과 비밀번호로 로그인을 할 수 있는 간단한 폼이 구현되어 있다.</p>
<p><code>JavaScript</code> 부분에서는 로그인 버튼을 누르면 실행되는 여러가지를 정의하고 있다.</p>
<p><code>e.preventDefault()</code>로 폼의 submit 동작을 막고 있다. </p>
<p><code>JavaScript</code>에서 직접 비동기 통신을 하기 위함인데 이를 통해 페이지 새로고침 없이 데이터를 전송하고 서버 응답을 받아 처리할 수 있다.</p>
<p>폼에서 이메일과 비밀번호를 data로 받고 <code>fetch</code>로 Django에 POST 요청을 보낸다. </p>
<p><code>JSON.stringify(data)</code>로 서버와 통신을 위해 data를 JSNO 형식으로 바꿔준다. </p>
<p><code>fetch</code> 함수를 사용하여 서버로 로그인 요청을 보내고 응답을 받으면 <code>then</code> 메서드로 응답(Promise)을 JSON 형식으로 파싱한다. 이후, 콘솔창에 출력하는 코드이다. </p>
<h3 id="회원가입-구현">회원가입 구현</h3>
<pre><code class="language-JavaScript"># register.html

&lt;!DOCTYPE html&gt;
&lt;html lang=&quot;ko&quot;&gt;
&lt;head&gt;
    &lt;meta charset=&quot;UTF-8&quot;&gt;
    &lt;meta http-equiv=&quot;X-UA-Compatible&quot; content=&quot;IE=edge&quot;&gt;
    &lt;meta name=&quot;viewport&quot; content=&quot;width=device-width, initial-scale=1.0&quot;&gt;
    &lt;title&gt;register&lt;/title&gt;
&lt;/head&gt;
&lt;body&gt;
    &lt;form action=&quot;http://127.0.0.1:8000/accounts/join/&quot; method=&quot;post&quot;&gt;
        이메일 : &lt;input type=&quot;text&quot; name=&quot;email&quot;&gt;&lt;br&gt;
        패스워드1 : &lt;input type=&quot;password&quot; name=&quot;password1&quot;&gt;&lt;br&gt;
        패스워드2 : &lt;input type=&quot;password&quot; name=&quot;password2&quot;&gt;&lt;br&gt;
        &lt;input type=&quot;button&quot; value=&quot;회원가입&quot;&gt;
    &lt;/form&gt;
    &lt;script&gt;
        const register = document.querySelector('input[type=&quot;button&quot;]');
        register.addEventListener('click', (e) =&gt; {
            e.preventDefault(); // submit의 기본동작을 막는다.
            const email = document.querySelector('input[name=&quot;email&quot;]').value;
            const password1 = document.querySelector('input[name=&quot;password1&quot;]').value;
            const password2 = document.querySelector('input[name=&quot;password2&quot;]').value;
            const data = {
                email: email,
                password1: password1,
                password2: password2
            }
            console.log(data)
            fetch('http://127.0.0.1:8000/accounts/join/', {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json',
                },
                body: JSON.stringify(data)
            }).then(response =&gt; response.json())
            .then(data =&gt; {
                console.log(data)
            })
        })
    &lt;/script&gt;
&lt;/body&gt;
&lt;/html&gt;</code></pre>
<p>일반적으로 폼 데이터를 서버로 전송하려면 두 가지 방법이 있다.</p>
<ol>
<li>HTML 폼 태그를 사용하여 데이터를 제출  </li>
</ol>
<ul>
<li><code>action</code> 속성에 서버의 URL을 , <code>method</code> 속성에 HTTP 메서드를 지정하면, 폼을 제출할 때 해당 URL로 데이터가 전송됨</li>
</ul>
<ol start="2">
<li><code>JavaScript</code>의 <code>fetch</code> 또는 <code>XMLHttpRequest</code>를 사용하여 비동기적으로 데이터를 전송하는 방법</li>
</ol>
<ul>
<li>사용자 경험이 향상되며 동적인 웹 구현이 가능</li>
</ul>
<p>위 코드에서는 2가지 방식을 모두 보여주고 있다. 
2번 방식의 script는 로그인 방식과 유사하다.</p>
<h3 id="게시글-작성-구현">게시글 작성 구현</h3>
<ul>
<li>blog 앱 생성</li>
<li>settings.py에 blog 추가</li>
<li>모델 생성<pre><code class="language-python"># blog &gt; models.py
</code></pre>
</li>
</ul>
<p>from django.db import models</p>
<p>class Post(models.Model):
    title = models.CharField(max_length=100)
    content = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)</p>
<pre><code>def __str__(self):
    return self.title</code></pre><pre><code>
- url 추가(config, blog)
- view 구현
```python
# blog &gt; views.py
# DRF로 FBV 작성
# post_list: GET(비회원), POST(회원)

from rest_framework.decorators import api_view, permission_classes
from rest_framework.permissions import IsAuthenticated
from rest_framework.response import Response
from .models import Post

@api_view([&quot;GET&quot;, &quot;POST&quot;])
@permission_classes([IsAuthenticated])
def post_list(request):
    if request.method == &quot;GET&quot;:
        posts = Post.objects.all()
        content = {&quot;posts&quot;: [{&quot;title&quot;: post.title, &quot;content&quot;: post.content} for post in posts]}
        return Response(content)
    elif request.method == &quot;POST&quot;:
        print(request.data)
        title = request.data[&quot;title&quot;]
        content = request.data[&quot;content&quot;]
        post = Post.objects.create(title=title, content=content)
        post.save()
        return Response({&quot;message&quot;: &quot;글 작성 완료!&quot;})</code></pre><p><code>GET</code> 요청이면 모든 게시글 객체를 가져와 제목과 내용을 딕셔너리로 변환한 후, <code>Response</code> 객체로 응답한다.</p>
<p><code>Post</code> 요청이면 요청 데이터에서 제목과 내용을 추출하여 새로운 <code>Post</code>객체를 생성한 후 저장한다. 성공 메세지를 <code>Response</code> 객체로 응답하고 있다.</p>
<ul>
<li>admin 페이지에 추가</li>
<li>관리자 계정 생성</li>
<li>마이그레이트</li>
<li>게시글 생성 테스트<pre><code class="language-JavaScript"># writer.html
</code></pre>
</li>
</ul>



    
    
    
    write


    <!-- 해당 코드는 셈플 코드 입니다. -->
    <form action="https://api.velog.io/rss/@jhp21c" method="">
        email : <input name="email" type="text" /><br />
        패스워드 : <input name="password" type="password" /><br />
        <input id="login" type="button" value="로그인" />
    </form>
    <form action="https://api.velog.io/rss/@jhp21c" method="">
        title: <input name="title" type="text" /><br />
        content: <input name="content" type="text" /><br />
        <input id="write" type="button" value="게시물작성" />
    </form>
    <pre><code>&gt;&quot;&quot;&quot;&quot;&gt;&gt;&gt;&quot;&quot;&quot;&quot;&gt;&gt;&lt;&gt;</code></pre><ol><li><code></code><code></code><code></code></li></ol><ul><li><code></code></li><li><pre><code class="language-JavaScript"></code></pre></li></ul><p></p><p></p><p></p><p></p><ol start="2"><li><code></code><code></code><code></code></li></ol><h2 id="참고"></h2><p><a href="https://velog.io/@vamos_eon/JWT%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80-%EA%B7%B8%EB%A6%AC%EA%B3%A0-%EC%96%B4%EB%96%BB%EA%B2%8C-%EC%82%AC%EC%9A%A9%ED%95%98%EB%8A%94%EA%B0%80-1"></a></p>