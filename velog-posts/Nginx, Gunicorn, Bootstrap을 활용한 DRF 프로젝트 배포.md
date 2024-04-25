<p>DRF를 처음 배우고 적용하며 팀 프로젝트를 마무리하며 어떻게 배포를 하였는지 적어보겠습니다. 서버는 AWS LightSail을 사용하였습니다.</p>
<h2 id="백엔드">백엔드</h2>
<ol>
<li><p>ubuntu 서버 생성</p>
</li>
<li><p>ubuntu 초기 세팅 진행</p>
</li>
</ol>
<ul>
<li><p>2-1. 로컬 시간을 변경</p>
<pre><code class="language-shell">sudo ln -sf /usr/share/zoneinfo/Asia/Seoul /etc/localtime</code></pre>
</li>
<li><p>2-2. 파이썬을 설치한다. Lightsail은 설치되어 있었다.</p>
</li>
<li><p>2-3. ubuntu 패키지 업데이트</p>
<pre><code class="language-shell">sudo apt update</code></pre>
</li>
<li><p>2-4. 가상환경 패키지 설치</p>
<pre><code class="language-shell">sudo apt install python3-venv</code></pre>
</li>
<li><p>2-5. 홈 디렉터리(/home/ubuntu) 하위에 프로젝트 디렉터리와 가상환경 디렉터리를 생성한다.</p>
<pre><code class="language-shell">mkdir projects
mkdir venvs</code></pre>
</li>
<li><p>2-6. 가상환경으로 진입한 후 진행한다.</p>
<pre><code class="language-shell">cd ./venvs/mysite/bin/
. activate</code></pre>
</li>
<li><p>2-7. wheel 패키지 관련 오류 방지를 위해 미리 설치한다.</p>
<pre><code class="language-shell">pip install wheel</code></pre>
</li>
</ul>
<ol start="3">
<li><p>projects 폴더에 백엔드 깃허브 레포를 클론한다.</p>
<pre><code class="language-shell">git clone (url) mysite</code></pre>
</li>
<li><p>미리 정의된 모듈을 설치한다. </p>
<pre><code class="language-shell">pip install -r requirements.txt</code></pre>
</li>
<li><p>db 파일이 올라가 있지 않다면 마이그레이션을 진행한다.</p>
<pre><code class="language-shell">python manage.py makemigrations
python manage.py migrate</code></pre>
</li>
<li><p>잘 설치되었는지 테스트 해본다.</p>
<pre><code class="language-shell">python manage.py runserver 0:8000</code></pre>
</li>
</ol>
<p>8000번 포트가 서버에서 열려 있어야 접속이 가능하다. 본인 서버 ip주소:8000번으로 접속이 가능할 것이다!</p>
<ol start="7">
<li>.env 파일을 생성한다.<pre><code class="language-shell">sudo vim .env</code></pre>
manage.py가 있는 폴더에서 실행해야 한다.
<img alt="" src="https://velog.velcdn.com/images/jhp21c/post/82d12328-1a3a-47b9-85f2-9044dafdb8d2/image.png" />
위와 같이 secret_key를 마음대로 설정하고 디버그를 False로 설정해준다.</li>
</ol>
<p>여기까지가 Django 관련 세팅이고 이제 통신을 위한 Gunicorn, Nginx 세팅을 해줘야 한다.</p>
<ol start="8">
<li>Ubuntu에 Gunicorn 서비스 등록<pre><code class="language-shell">sudo vim /etc/systemd/system/site.service</code></pre>
</li>
</ol>
<pre><code class="language-shell">[Unit]
Description=gunicorn daemon
After=network.target

[Service]
User=ubuntu 
Group=ubuntu
WorkingDirectory=/home/ubuntu/projects/mysite 
EnvironmentFile=/home/ubuntu/projects/mysite/.env 
ExecStart=/home/ubuntu/venvs/mysite/bin/gunicorn --workers 2 --bind unix:/tmp/gunicorn.sock config.wsgi:application 

[Install]
WantedBy=multi-user.target</code></pre>
<p>Service 부분만 바꿔주면 된다. </p>
<ul>
<li>User: 유저이름</li>
<li>WorkingDirectory: 프로젝트 루트 디렉토리</li>
<li>EnviromentFile: 환경변수 파일</li>
<li>ExecStart: 가상환경에 설치된 gunicorn 환경 설정<ul>
<li>workers: 보통 CPU 갯수 * 2개</li>
<li>--bing ~~: WSGI 실행 명령</li>
<li>config.wsgi~~: 장고의 settings.py에 정의된 앱들을 실행하겠다는 의미</li>
</ul>
</li>
</ul>
<ol start="9">
<li><p>서비스 실행</p>
<pre><code class="language-shell">sudo systemctl start site</code></pre>
</li>
<li><p>서비스 실행 확인 </p>
<pre><code class="language-shell">sudo systemctl status site</code></pre>
</li>
<li><p>서비스 자동 실행 및 재실행</p>
<pre><code class="language-shell">sudo systemctl enable site # .service 빼고입니다.
# 서버가 재실행 될 때 Gunicorn 서비스가 자동 실행되게 만들어 줍니다.
</code></pre>
</li>
</ol>
<p>sudo systemctl restart site # .service 빼고입니다.</p>
<h1 id="서비스-재실행-명령">서비스 재실행 명령</h1>
<pre><code>이제 Nginx 세팅을 해야 한다.

12. 유저 이름을 변경한다.
```shell
sudo vim /etc/nginx/nginx.conf</code></pre><pre><code class="language-python">user ubuntu;
worker_processes auto;
pid /run/nginx.pid;
include /etc/nginx/modules-enabled/*.conf;

events {
        worker_connections 768;
        # multi_accept on;
}

http { ...</code></pre>
<ol start="13">
<li>서버 블록을 설정한다.<pre><code class="language-shell">sudo vim /etc/nginx/sites-available/mysite</code></pre>
</li>
</ol>
<pre><code class="language-shell">server {
    listen 80;
    server_name 백엔드 서버 ip;

    location / {
        include proxy_params;
        proxy_pass http://unix:/tmp/gunicorn.sock;

    }

    location /static/ {
            alias /home/ubuntu/projects/mysite/config/staticfiles/;
    }
}</code></pre>
<ol start="14">
<li>서버 블록을 설정한 파일을 심볼릭 링크(바로가기)하여 활성화<pre><code class="language-shell">sudo ln -s /etc/nginx/sites-available/설정 파일 이름 /etc/nginx/sites-enabled/
</code></pre>
</li>
</ol>
<pre><code>
여기까지가 백엔드에서 설정할 부분이다.

## 프론트엔드
여기서의 프론트는 바닐라 자바스크립트를 활용한 부트스트랩 파일을 배포하는 역할이다.

1. Ubuntu 로컬 시간을 수정하고 업데이트를 진행한다.
```shell
sudo ln -sf /usr/share/zoneinfo/Asia/Seoul /etc/localtime

sudo apt update</code></pre><ol start="2">
<li>유저 이름을 변경한다. <pre><code class="language-shell">sudo vim /etc/nginx/nginx.conf</code></pre>
<pre><code>user ubuntu;
worker_processes auto;
pid /run/nginx.pid;
include /etc/nginx/modules-enabled/*.conf;
</code></pre></li>
</ol>
<p>events {
        worker_connections 768;
        # multi_accept on;
}</p>
<p>http {...</p>
<pre><code>
3. 프론트 레포를 클론한다.
```shell
cd /var/www</code></pre><pre><code class="language-shell">git clone 저장소url myfrontsite</code></pre>
<p>myfrontsite라는 이름은 바꿔도 상관없다.</p>
<ol start="4">
<li><p>서버 블록을 설정한다.</p>
<pre><code class="language-shell">sudo vim /etc/nginx/sites-available/myfrontsite</code></pre>
<pre><code class="language-shell">server {
 listen 80;
 server_name 프론트서버 ip;

 # 프론트엔드 asset 제공
 location / {
     root /var/www/myfrontsite; # 프론트엔드 파일 경로 지정
     try_files $uri $uri/ =404;
 }

 # 백엔드 서버로 API 요청 프록시
 location /api/ {
     proxy_pass http://백엔드 서버 ip;
     include proxy_params;
 }
}</code></pre>
</li>
<li><p>서버 블록을 설정한 파일을 심볼릭 링크(바로가기)하여 활성화</p>
<pre><code class="language-shell">sudo ln -s /etc/nginx/sites-available/myfrontsite /etc/nginx/sites-enabled/</code></pre>
</li>
<li><p>Nginx 재시작</p>
<pre><code class="language-shell">sudo systemctl restart nginx</code></pre>
</li>
</ol>
<p>먼 여정을 함께하시느라 고생하셨습니다. 여기까지 진행한다면 문제없이 서버 배포가 가능할 것입니다.</p>
<p>만약 프론트 서버 ip로 접속했을 때, 기본 nginx 화면 등이 나온다면 default 관련한 파일을 제거하면 됩니다.</p>
<h2 id="번외">번외</h2>
<ol>
<li>Gunicorn 재시작<pre><code class="language-shell">sudo systemctl restart site</code></pre>
</li>
<li>nginx 파일에 문제가 없는지 확인<pre><code class="language-shell">sudo nginx -t</code></pre>
</li>
<li>Nginx 재시작<pre><code class="language-shell">sudo systemctl restart nginx</code></pre>
</li>
</ol>