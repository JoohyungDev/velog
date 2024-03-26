<p>저번 시간에 이어 Dockerfile 작성법과 CI/CD에 대해 알아보겠습니다.</p>
<h2 id="dockerfile">Dockerfile</h2>
<h3 id="정의">정의</h3>
<p>도커 이미지를 생성하기 위한 스크립트(설정파일)이다. 도커는 Dockerfile에 작성된 명령문을 순서대로 수행하며 도커 이미지를 생성한다.</p>
<h3 id="사용하는-이유">사용하는 이유</h3>
<ul>
<li>배포의 용이함<ul>
<li>용량이 매우 큰 이미지 파일을 배포하기 보다는 이미지를 만들 수 있는 스크립트를 배포하는 것이 훨씬 효율적일 것이다.</li>
</ul>
</li>
<li>이미지 생성 기록<ul>
<li>이미지의 구성을 이해하고 수정할 수 있다.</li>
</ul>
</li>
</ul>
<h3 id="작성-예시">작성 예시</h3>
<p>파일의 이름은 무조건 <code>Dockerfile</code>로 해야 한다.</p>
<pre><code class="language-shell"># 이미지 설정
FROM python:3.11 
# 작성자
LABEL authors=&quot;이름&quot;
# 작업 디렉토리 설정
WORKDIR /app

#  프로젝트의 의존성 파일을 컨테이너 내부로 복사한다.
COPY requirements.txt /app/

# 의존성 설치
RUN pip install -r requirements.txt

# 나머지 프로젝트 파일들을 컨테이너 내부로 복사한다.
COPY . /app/

# 8000포트로 노출
EXPOSE 8000

# Docker 컨테이너가 시작될 때 실행될 명령어를 지정한다. 여기서는 Django 개발 서버를 실행한다.
CMD [&quot;python3&quot;, &quot;manage.py&quot;, &quot;runserver&quot;, &quot;0.0.0.0:8000&quot;]</code></pre>
<p>위 코드를 통해 외부에서 8000번 포트로 접속할 수 있다.</p>
<h2 id="cicd">CI/CD</h2>
<p>실제로 개발을 하는 모습을 상상해 보자. 기능을 개발하고, 깃 푸시, 풀, 테스트, 다시 기능 개발.. 푸시, 풀.. 을 반복하게 된다.</p>
<p>정상적으로만 돌아간다면 상관이 없겠지만 그 과정에서 분명히 에러는 발생할 것이다. 이때, 테스트시 에러가 발생하면 수정하고 깃 푸시.. 풀.. 테스트.. 또 에러발생하면 수정 후 푸시.. 라는 작업을 반복적으로 하게 된다.</p>
<p>굉장히 번거롭다. 이를 해결하기 위해 나온 개념이다.</p>
<h3 id="cicontinuous-integration">CI(Continuous Integration)</h3>
<p>번역하면 지속적인 통합이라는 뜻으로, 애플리케이션의 새로운 코드 변경 사항이 정기적으로 빌드 및 테스트 되어 공유 레포지토리에 통합하는 것을 의미한다.</p>
<p>Git과 같은 형상관리 도구를 사용함을 의미한다.</p>
<h3 id="cdcontinuous-delivery--deployment">CD(Continuous Delivery &amp; Deployment)</h3>
<p><img alt="" src="https://velog.velcdn.com/images/jhp21c/post/7d5d59bc-4195-4206-90fd-b8e668532d40/image.png" /></p>
<p>코드 변경사항이 테스트를 통과하면 자동적으로 생산 환경에 배포되는 과정이다. 우리나라의 경우는 <code>Deployment</code> 라는 뜻으로 주로 쓰인다.</p>
<p>다양한 도구들이 있지만 간단한 프로젝트에서는 GitHub의 Actions를 사용하고, 더 많은 기능이 필요하다면 Jenkins 등의 도구를 사용한다.</p>
<h3 id="github-actions">GitHub Actions</h3>
<ul>
<li>장점<ul>
<li>간편한 접근성 및 관리</li>
<li>마켓플레이스를 통한 확장성
<img alt="" src="https://velog.velcdn.com/images/jhp21c/post/eb89ce46-0da1-4a6e-a05d-1fc73170fa70/image.png" /></li>
</ul>
</li>
<li>단점<ul>
<li>GitHub에 종속</li>
</ul>
</li>
</ul>
<h2 id="cicd-구축을-위한-기술">CI/CD 구축을 위한 기술</h2>
<h3 id="wsgi동기">WSGI(동기)</h3>
<p><code>Web Server Gateway Interface</code>의 약자로, 파이썬 기반의 웹 개발에서 자주 사용되며, 동기 방식의 처리를 기본으로 한다. 
<code>Django</code> 애플리케이션을 웹 서버에 연결하는 방식을 정의한 것이라고 볼 수도 있다.
<img alt="" src="https://velog.velcdn.com/images/jhp21c/post/e13c55ad-6ccb-47ca-be63-6766c9cb2c36/image.png" /></p>
<h3 id="asgi비동기">ASGI(비동기)</h3>
<p><code>Asynchronous Server Gateway Interface</code>의 약자로, 비동기식 웹 서버 및 애플리케이션과의 통신을 위한 인터페이스 규격이다.</p>
<p>채팅 서비스와 같은 실시간 양방향 데이터 통신에 사용한다고 한다.</p>
<h3 id="gunicorn">Gunicorn</h3>
<p><img alt="" src="https://velog.velcdn.com/images/jhp21c/post/e6fdda33-5a17-4854-8535-6dfc65201a95/image.png" /></p>
<p>파이썬 기반의 애플리케이션을 위한 <code>WSGI</code> 웹 서버이다. 메모리 자원 사용이 적고 CPU 사용량이 낮아 성능이 좋다.</p>
<p>웹 브라우저나 다른 클라이언트의 HTTP 요청을 받아, 해당 요청을 파이썬 웹 애플리케이션에 전달하고, 애플리케이션의 응답을 클라이언트에 다시 전송한다.</p>
<p>파이썬 프로그램을 호출하는 서버이다.</p>
<ul>
<li>특징
<img alt="" src="https://velog.velcdn.com/images/jhp21c/post/2611331b-8888-41b8-b510-11d17554c495/image.png" />
그림에서 알 수 있듯이, 여러개의 일꾼(worker)를 사용하여 동시에 여러 HTTP 요청을 처리할 수 있다.</li>
</ul>
<h3 id="nginx">Nginx</h3>
<p><img alt="" src="https://velog.velcdn.com/images/jhp21c/post/61717d9a-89f2-450c-8d66-143c58572a3c/image.png" /></p>
<p><code>Django</code> 애플리케이션 배포 시 <code>Gunicorn</code>과의 조합으로 사용한다. <code>Nginx</code> 는 정적 데이터(이미지, CSS, JS, 기타 미디어 파일 등)를 처리하고 리버스 프록시(Reverse Proxy)역할을 하며 <code>Gunicorn</code>은 WSGI 서버로 동작한다. </p>
<p>리버스 프록시는 클라이언트와 서버 중간에 대리자 역할을 하는 서버를 말한다. 부하를 분산시킨다고 볼 수 있다.</p>
<ul>
<li>통신 방식
<img alt="" src="https://velog.velcdn.com/images/jhp21c/post/6b28be5a-e844-4396-9efd-2ea8d5e86a2c/image.png" /></li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/jhp21c/post/c2bce4c1-bdc7-41b4-b0ae-bfe3436d475e/image.png" /></p>
<p>보통 Nginx와 Gunicorn을 한 프로젝트 내에 위치시켜 Unix 소켓 방식으로 Ip를 사용하지 않고 통신을 한다.</p>
<h3 id="drf-배포">DRF 배포</h3>
<p>Django 프로젝트(DRF)에서 배포하는 예시이다.</p>
<ol>
<li><p>환경 세팅</p>
<pre><code class="language-shell"># settings.py
...
WSGI_APPLICATION = &quot;{프로젝트이름}.wsgi.application&quot;
DEBUG = False # 배포환경에서는 DEBUG 옵션을 해제해야합니다.</code></pre>
</li>
<li><p>데이터베이스 설정(Postgresql)</p>
<pre><code class="language-shell">DATABASES = {
 &quot;default&quot;: {
     &quot;ENGINE&quot;: &quot;django.db.backends.postgresql&quot;,
     &quot;NAME&quot;: DB_NAME, # env파일
     &quot;USER&quot;: DB_USER,  # env파일
     &quot;PASSWORD&quot;: DB_PASSWORD,  # env파일
     &quot;HOST&quot;: DB_HOST,  # env파일
     &quot;PORT&quot;: DB_PORT,  # env파일
 }
}
### postgresql을 사용하려면 psycopg2 설치가 수행되어야합니다. </code></pre>
</li>
<li><p>서버 세팅</p>
<pre><code class="language-shell">git clone {장고 레포지토리 주소}
</code></pre>
</li>
</ol>
<h1 id="가상환경-적용">가상환경 적용</h1>
<p>python3 -m venv venv
source {가상환경이름}/bin/activate</p>
<h1 id="설치">설치</h1>
<p>pip install -r requirements.txt</p>
<pre><code>
4. Gunicorn 세팅
```shell
# 설치
pip install gunicorn 

# 경로
cd /home/{사용자명}/{프로젝트 루트 디렉터리}

gunicorn --bind unix:/tmp/gunicorn.sock {프로젝트명}.wsgi:application

# 일반적인 수행방법(동일하지 않은 시스템)
# gunicorn --bind 0:5000 {프로젝트이름}.wsgi:application</code></pre><p>위 코드는 유닉스(우분투) 소켓을 활용한 방법인데 이렇게 실행하면 반드시 <code>Nginx</code>와 같은 웹서버가 필요하다고 한다.</p>
<ol start="5">
<li>우분투에 Gunicorn 서비스 등록<pre><code class="language-shell"># 편집기 실행
sudo vim /etc/systemd/system/{프로젝트명}.service 
</code></pre>
</li>
</ol>
<h1 id="파일-작성">파일 작성</h1>
<p>[Unit]
Description=gunicorn daemon
After=network.target</p>
<p>[Service]
User=ubuntu # 유저이름 확인해주세요.
Group=ubuntu
WorkingDirectory=/home/ubuntu/{프로젝트 디렉토리} # 프로젝트 루트 디렉터리
EnvironmentFile=/home/ubuntu/{프로젝트 디렉토리}/.env # 환경변수 파일
ExecStart=/home/ubuntu/{프로젝트 디렉토리}/venv/bin/gunicorn \ #가상환경에 설치된 gunicorn
        --workers 2 \ #워커 갯수
        --bind unix:/tmp/gunicorn.sock \ # WSGI실행 명령
        {프로젝트명}.wsgi:application # WSGI(Django) 애플리케이션</p>
<p>[Install]
WantedBy=multi-user.target</p>
<pre><code>
6. 서비스 실행 및 확인
```shell
# 실행
sudo systemctl start {위에서 작성했던 파일명}

# 확인
sudo systemctl status {위에서 작성했던 파일명} # .service 빼고 작성</code></pre><ol start="7">
<li>서비스 자동 실행 <pre><code class="language-shell"># 서버가 재실행 될 때 Gunicorn 서비스가 자동 실행되게 만들어 줍니다.
sudo systemctl enable {위에서 작성했던 파일명} # .service 빼고입니다.
</code></pre>
</li>
</ol>
<h1 id="서비스-재실행-명령">서비스 재실행 명령</h1>
<p>sudo systemctl restart {위에서 작성했던 파일명} # .service 빼고입니다.</p>
<pre><code>
8. Nginx 세팅
일반적인 Nginx를 포함한 웹 아키텍쳐는 클라이언트 -&gt; Nginx -&gt; WSGI 서버(Gunicorn 등) -&gt; Django로 구성된다. 

 이러한 Nginx를 프록시 서버라고 한다.

  - 프록시 : 한 네트워크 기기가 다른 기기를 대신하여 인터넷 또는 다른 네트워크에 연결되는 것을 의미
  - 프록시 서버 : 프론트와 백엔드 사이에 위치하는 서버로, 중계 서버라고 생각하면 된다.

```shell
# nginx 설정파일 편집 실행
sudo vim /etc/nginx/sites-enabled/default 

server{
    root /var/www/html;
    server_name # 도메인 주소 없으면 IPV4 주소(IP)

    location /api/v1/{ # 클라이언트에서 오는 /api/v1/ 으로 시작되는 모든 요청 핸들링
             include proxy_params;
             proxy_pass http://unix:/tmp/gunicorn.sock;
        }

}</code></pre><ul>
<li><code>proxy_pass</code> : 프론트의 요청을 전달해야 하는 백엔드 서버의 주소를 지정</li>
<li><code>proxy_params</code> : 클라이언트 IP 주소, 요청 스키마, 호스트 이름 등을 전달하기 위한 내용들이 포함된다. 이를 통해 백엔드 서버에서도 클라이언트의 정보를 알 수 있다.</li>
</ul>
<h2 id="github-actions-cicd">Github Actions CI/CD</h2>
<h3 id="환경-변수-추가">환경 변수 추가</h3>
<p><img alt="" src="https://velog.velcdn.com/images/jhp21c/post/56373b52-5f06-4ee7-bf57-fba37583daf8/image.png" /></p>
<h3 id="mainyml트리거-파일-추가">main.yml(트리거 파일) 추가</h3>
<p><img alt="" src="https://velog.velcdn.com/images/jhp21c/post/dfda1b59-5976-451e-b3ef-4f44e30a3457/image.png" />
해당하는 항목을 클릭하여 트리거 파일을 생성한다. 그 안에 다음과 같은 내용을 집어넣는다.</p>
<pre><code class="language-yml">name: Django CI/CD

on:
  push:
    branches: [ &quot;main&quot; ]
  pull_request:
    branches: [ &quot;main&quot; ]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: 체크아웃 레포지토리
        uses: actions/checkout@v3

      - name: 파이썬 설정
        uses: actions/setup-python@v3
        with:
          python-version: '3.11'

      - name: 의존성 설치
        run: |
          pip install --upgrade pip
          pip install -r requirements.txt

      - name: 서버 배포
        uses: appleboy/ssh-action@master
        with:
          host: ${{ secrets.SERVER_HOST }} 
          username: ${{ secrets.SERVER_USER }}
          password: ${{ secrets.SERVER_PASSWORD }}
          script: |
                        set -e
            cd /home/ubuntu/{레포지토리주소}
            git pull origin main

            echo &quot;SECRET_KEY=\&quot;${{ secrets.SECRET_KEY }}\&quot;&quot; &gt; .env
            echo &quot;DEBUG='${{ secrets.DEBUG }}'&quot; &gt;&gt; .env

            source venv/bin/activate
            pip install -r requirements.txt
            sudo systemctl restart {아까등록한서비스이름}.service</code></pre>
<p><code>on</code> 으로 시작하는 부분은 <code>main</code> 브랜치에 코드가 <code>push</code> 혹은 <code>pull_request</code> 가 발생할 때 수행된다는 뜻이다.</p>
<p><code>jobs</code> 는 실행할 작업들을 정의하는 부분이다.</p>
<p><code>runs-on</code> 은 작업이 실행될 가상 환경의 유형을 지정한다.</p>
<p><code>steps</code> 는 워크플로우가 진행되는 각 단계(action)을 의미한다. </p>
<p><code>$</code>로 시작하는 부분은 이전에 정의했던 환경 변수들이다.</p>
<p><code>set -e</code> 스크립트는 어느 부분에서든 명령어가 실패하면 즉시 중단되고, 작업이 실패 상태로 표시됨을 의미한다.</p>
<p>yaml 파일은 아주 예민한 파일이기에 띄어쓰기, 탭 등을 매우매우 중요하게 봐야 한다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhp21c/post/7a74385b-d425-488f-8e3b-abf31b213723/image.png" /></p>
<p>성공시 위와 같은 화면이 나오게 된다.</p>
<h2 id="참고">참고</h2>
<p>아키텍쳐 그림은 위니브 김승주 강사님 자료를 제공받아 작성하였습니다.</p>