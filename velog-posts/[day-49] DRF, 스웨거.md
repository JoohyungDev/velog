<p>전에 잠깐 보았던 DRF 기본 글에 대한 복습 및 스웨거에 대해 정리해 보겠다.</p>
<h2 id="기본-세팅">기본 세팅</h2>
<ul>
<li>프로젝트 폴더 생성</li>
<li>사용할 앱 만들기</li>
<li>settings.py 정의하기</li>
<li>모델 만들기</li>
<li>db연동</li>
<li>admin.py 정의</li>
<li>관리자 계정 생성</li>
</ul>
<p>까지는 여태까지 장고 하면서 봤던 내용이고 이후 과정이 조금 다르다. </p>
<h2 id="drf-관련-pip-설치">DRF 관련 pip 설치</h2>
<pre><code class="language-shell">pip install djangorestframework
pip install django-cors-headers
pip install drf-yasg 
pip install drf_spectacular</code></pre>
<ul>
<li><p><code>django-cors-headers</code> : 응답에 CORS(교차 출처 리소스 공유) 헤더를 추가하는 장고 앱이다. 이를 통해 다른 출처의 클라이언트 측 코드가 API에 액세스할 수 있다.</p>
</li>
<li><p><code>drf-yasg</code> : 장고 REST 프레임워크 API에 대한 OpenAPI(Swagger) 문서를 생성하는 패키지이다.</p>
</li>
<li><p><code>drf_spectacular</code> : <code>drf-yasg</code>와 같은 패키지이고, 원래는 <code>drf-yasg</code>만 설치해도 같이 설치되었는데 지금은 안되어서 따로 설치해야 한다.</p>
</li>
</ul>
<h2 id="settgingspy-수정사항">settgings.py 수정사항</h2>
<pre><code class="language-python">INSTALLED_APPS = [
    ...,
# django lib app
    &quot;rest_framework&quot;,
    &quot;corsheaders&quot;,
    'drf_spectacular',
    ...,
]</code></pre>
<pre><code class="language-python">MIDDLEWARE = [
    &quot;corsheaders.middleware.CorsMiddleware&quot;,  # 최상단 추가
    ...,
]</code></pre>
<p>장고 앱에 CORS 헤더를 활성화하며, 장고 서버가 아닌 다른 출처(도메인)에서 실행되는 클라이언트 측 코드(예: 프론트엔드 애플리케이션)가 애플리케이션에 액세스해야 하는 경우에 필요하다.</p>
<pre><code class="language-python"># 모든 오리진(도메인)의 요청이 장고 애플리케이션에 액세스할 수 있도록 허용
CORS_ORIGIN_ALLOW_ALL = True
# 쿠키 및 기타 자격 증명을 CORS 요청에 포함할 수 있도록 허용
CORS_ALLOW_CREDENTIALS = True</code></pre>
<pre><code class="language-python">REST_FRAMEWORK = {
    # YOUR SETTINGS  drf의 schema 클래스를 drf-specacular의 AutoSchema로 교체해줍니다.
    'DEFAULT_SCHEMA_CLASS': 'drf_spectacular.openapi.AutoSchema',
}</code></pre>
<p>Django REST Framework에서 사용하는 기본 스키마 클래스를 <code>drf_spectacular</code> 패키지에서 제공하는 <code>AutoSchema</code> 클래스로 대체한다.</p>
<h2 id="urlspy-수정사항">urls.py 수정사항</h2>
<pre><code class="language-python"># 프로젝트 앱 &gt; urls.py
from django.contrib import admin
from django.urls import path, include
from drf_spectacular.views import SpectacularAPIView, SpectacularRedocView, SpectacularSwaggerView

urlpatterns = [
    path('admin/', admin.site.urls),
    path('blog/', include('blog.urls')),
    path('api/schema/', SpectacularAPIView.as_view(), name='schema'), # API 스키마 제공(yaml파일)
    path('api/schema/swagger-ui/', SpectacularSwaggerView.as_view(url_name='schema'), name='swagger-ui'), # 테스트할 수 있는 UI
    path('api/schema/redoc/', SpectacularRedocView.as_view(url_name='schema'), name='redoc'), # API 문서화를 위한 UI
]</code></pre>
<ul>
<li><code>http://domain/api/schema/</code> - 이 URL은 클라이언트 애플리케이션이나 기타 도구에서 API의 구조와 기능을 이해하는 데 사용할 수 있는 YAML 형식의 OpenAPI 스키마를 반환한다.</li>
<li><code>http://domain/api/schema/swagger-ui/</code> - 이 URL은 API 엔드포인트를 테스트하고 탐색할 수 있는 대화형 인터페이스를 제공하는 Swagger UI를 표시한다. 테스트해볼 수 있는 URL이다.</li>
<li><code>http://domain/api/schema/redoc/</code> - 이 URL은 OpenAPI 스키마를 기반으로 하는 API의 대체 문서 인터페이스인 ReDoc UI를 표시한다.</li>
</ul>
<p>그 다음 직접 만든 앱(여기선 blog)의 urls.py를 이전과 같이 정의하고, views.py를 정의한다. views.py는 serializer.py를 만들지는 않고 임시로 만들어서 실습해보는 예제이다.</p>
<h2 id="viewspy-수정사항">views.py 수정사항</h2>
<pre><code class="language-python"># blog &gt; views.py

from django.shortcuts import render
from rest_framework.decorators import api_view
from rest_framework.response import Response
from .models import Post


@api_view([&quot;GET&quot;])
def blog_list(request):
    blogs = Post.objects.all()
    serializer = []
    for blog in blogs:
        serializer.append(
            {
                &quot;title&quot;: blog.title,
                &quot;content&quot;: blog.content,
                &quot;created_at&quot;: blog.created_at,
                &quot;updated_at&quot;: blog.updated_at,
            }
        )
    return Response(serializer)</code></pre>
<ul>
<li><code>@api_view([&quot;GET&quot;])</code> : GET 요청만 허용하는 DRF API 뷰로 표시하는 데코레이터
최종적으로 API 요청에 대한 응답으로 직렬화된 데이터를 반환한다.</li>
</ul>
<h2 id="fe-서버">FE 서버</h2>
<p>Nginx등을 깊게 배우진 않아 임시로 장고 blog앱 내부에 FE라는 폴더를 만들어 간이로 장고와 FE 2개의 서버를 돌리는 느낌을 내보았다.</p>
<pre><code class="language-html"># FE 서버 &gt; index.html

&lt;!DOCTYPE html&gt;
&lt;html lang=&quot;en&quot;&gt;
&lt;head&gt;
    &lt;title&gt;Document&lt;/title&gt;
&lt;/head&gt;
&lt;body&gt;
    &lt;main id=&quot;main-container&quot;&gt;&lt;/main&gt;
&lt;script&gt;
fetch('http://127.0.0.1:8000/blog/')
.then(response =&gt; response.json())
.then(data =&gt; {
    const mainContainer = document.getElementById('main-container')
    data.forEach(post =&gt; {
        const postElement = document.createElement('div')
        postElement.innerHTML = `
            &lt;h1&gt;${post.title}&lt;/h1&gt;
            &lt;p&gt;${post.content}&lt;/p&gt;
        `
        mainContainer.appendChild(postElement)
    })
})
&lt;/script&gt;
&lt;/body&gt;
&lt;/html&gt;</code></pre>
<ul>
<li><code>fetch('http://127.0.0.1:8000/blog/')</code> : DRF API(장고 서버)에 GET 요청(데이터를 요청)을 보낸다.</li>
<li><code>response.json()</code> : API로부터 받은 응답을 파싱. 해당 코드에서 응답은 직렬화된 블로그 포스트 데이터(JSON)이다.
변환된 JSON 데이터를 순회하며 각 제목과 내용을 사용하여 새로운 div요소를 생성하고 이를 main-container에 추가하는 코드이다.</li>
</ul>
<p>서버로부터 받은 데이터를 활용해 웹 페이지에 동적으로 정보를 표시하며, 이를 통해 사용자가 페이지를 새로고침하지 않아도 최신 데이터를 볼 수 있다.</p>
<h2 id="get-post-테스트">GET, POST 테스트</h2>
<p>썬더 클라이언트라는 vscode 익스텐션으로 테스트 하는 작업이다.</p>
<pre><code class="language-python"># blog &gt; views.py
from django.shortcuts import render
from rest_framework.decorators import api_view
from rest_framework.response import Response
from .models import Post
from rest_framework import serializers
from drf_spectacular.utils import extend_schema

# views.py 수정을 통해 api/schema/swagger-ui 문서를 변경할 수 있도록 수
# 이 코드는 원래 .serializers.py로 작성이 되었어야 하는 파일입니다.
class PostSerializer(serializers.Serializer):
    title = serializers.CharField()
    content = serializers.CharField()


@extend_schema(
    methods=[&quot;GET&quot;, &quot;POST&quot;],
    request=PostSerializer,
    responses={200: PostSerializer(many=True)},
)
@api_view([&quot;GET&quot;, &quot;POST&quot;])
def blog_list(request):
    serializer = PostSerializer(Post.objects.all(), many=True)
    return Response(serializer.data)
</code></pre>
<ul>
<li><code>extend_schema</code> : 스키마 생성을 위한 추가 메타데이터를 제공하는데 사용</li>
<li><code>class PostSerializer</code> : serializers.py에 있어야 하는 내용으로 Post 모델 인스턴스의 직렬화 및 역직렬화에 포함되어야 하는 필드를 정의</li>
<li><code>@extend_schema</code> 함수 :  GET 및 POST 요청을 모두 처리하고, 요청 본문은 POST 요청의 경우 <code>PostSerializer</code>를 준수해야 하며, 성공적인 응답(상태 코드 200)은 <code>PostSerializer</code>를 사용하여 직렬화된 <code>Post</code> 객체 목록을 반환</li>
<li><code>@api_view</code> : request를 매개변수로 받아, Post모델의 몯느 인스턴스를 직렬화하여 응답으로 반환</li>
<li><code>blog_list</code> 함수 <ul>
<li>Post 모델의 모든 인스턴스를 조회하고, PostSerializer를 사용하여 직렬화</li>
<li><code>many=True</code> : 여러 인스턴스를 직렬화할 수 있음</li>
<li>최종적으로 직렬화된 데이터를 Response객체에 담아 반환</li>
</ul>
</li>
</ul>
<p>최종 API 명세서의 모습이다. <code>http://127.0.0.1:8000/api/schema/swagger-ui/#/</code>
<img alt="" src="https://velog.velcdn.com/images/jhp21c/post/fa7b970f-a7bf-4721-8f3d-b2ae7f25a40c/image.png" /></p>
<p><img alt="" src="https://velog.velcdn.com/images/jhp21c/post/3fda212a-987c-4018-bdcc-45d6875abb39/image.png" /></p>
<p><img alt="" src="https://velog.velcdn.com/images/jhp21c/post/b714e7e2-0886-4e28-bd77-ee19ce2532a0/image.png" />
<img alt="" src="https://velog.velcdn.com/images/jhp21c/post/7ee63f2a-b581-4dd5-8726-e4b1c194de2a/image.png" />
<img alt="" src="https://velog.velcdn.com/images/jhp21c/post/a0497fd4-2f9a-4c6b-a7a3-9515398c66c2/image.png" /></p>