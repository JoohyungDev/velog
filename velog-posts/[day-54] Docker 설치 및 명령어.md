<p>Docker에 대해 간단하게 알아보겠습니다.</p>
<h2 id="docker">Docker</h2>
<h3 id="정의">정의</h3>
<p>도커는 독립된 환경에서 애플리케이션을 개발하고 실행하도록 도와주는 오픈 플랫폼이다. </p>
<p><code>이미지</code> 라는 개념이 등장하는데 이는 가상 머신이나 컨테이너 또는 프로그램을 실행하는 데 필요한 파일과 라이브러리, 설정등을 가지고 있는 파일이다. </p>
<p><code>이미지</code>를 실행하면 프로세스, 컨테이너가 된다.</p>
<p>컨테이너라는 동일한 형식으로 애플리케이션이 패키징되어 어떤 애플리케이션이든 도커 플랫폼이면 실행이 가능하다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhp21c/post/2f9eef30-31a6-4d77-bd57-34e18fe07630/image.png" /></p>
<h3 id="설치-및-기본-명령어">설치 및 기본 명령어</h3>
<p>서버 실행 후 터미널까지 열렸다는 가정 하에 진행한다.</p>
<h4 id="설치">설치</h4>
<p>Ubuntu 22.04 (LTS) 기준이다.</p>
<ul>
<li><p>충돌 방지</p>
<pre><code class="language-shell">for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done</code></pre>
<p>기존에 설치되어 있거나 설치하다 만 파일들이 있으면 정상적으로 설치되지 않으므로 그러한 파일들을 삭제하는 명령어이다.</p>
</li>
<li><p>설치</p>
<pre><code class="language-shell"># Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
</code></pre>
</li>
</ul>
<h1 id="add-the-repository-to-apt-sources">Add the repository to Apt sources:</h1>
<p>echo <br />  &quot;deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] <a href="https://download.docker.com/linux/ubuntu">https://download.docker.com/linux/ubuntu</a> <br />  $(. /etc/os-release &amp;&amp; echo &quot;$VERSION_CODENAME&quot;) stable&quot; | <br />  sudo tee /etc/apt/sources.list.d/docker.list &gt; /dev/null
sudo apt-get update</p>
<pre><code>명령이 길지만 그대로 복사해서 ctrl+insert 혹은 마우스 우클릭으로 전부 붙여넣기가 가능하다.


- 패키지 설치
```shell
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin</code></pre><ul>
<li>설치 확인<pre><code class="language-shell">sudo docker run hello-world</code></pre>
정상적으로 설치가 진행되었는지 확인을 한다.
<img alt="" src="https://velog.velcdn.com/images/jhp21c/post/88192006-5778-4144-b8e6-c44092f03db7/image.png" /></li>
</ul>
<p>위와 같은 창이 나온다면 성공한 것이다.</p>
<h3 id="명령어">명령어</h3>
<pre><code class="language-shell"># 시스템 재부팅 시에 docker가 동작하도록 설정
sudo systemctl enable docker

# 도커 Cli 명령 실행
sudo docker

# 도커 실행여부
sudo service docker status

# docker 그룹 생성
sudo groupadd docker

# docker 그룹에 현재 유저 추가
# sudo를 붙이지 않고 docker를 사용하기 위함
sudo usermod -aG docker $USER</code></pre>
<pre><code class="language-shell"># hello world 이미지 실행
docker run hello-world

# nginx 실행하기
docker run -p 80:80 nginx

# getting-started 이미지 실행하기
# -d 옵션은 백그라운드에서 실행하는 detached 모드입니다
docker run -d -p 80:80 docker/getting-started

# 실행중인 컨테이너 목록
docker ps

# 모든 컨테이너 목록
docker ps -a

# 로그 보기
docker logs -f &lt;컨테이너 이름&gt;

# 컨테이너 중지하기
docker stop &lt;컨테이너 이름&gt;

# 컨테이너 삭제하기
docker rm &lt;컨테이너 이름&gt;

# 이미지 목록 보기
docker images

# 컨테이너 이미지 삭제하기
docker rmi &lt;이미지이름&gt;</code></pre>
<p><img alt="" src="https://velog.velcdn.com/images/jhp21c/post/2fbf8d5b-d824-44f1-8b5b-324d1217bb07/image.png" /></p>
<p><img alt="" src="https://velog.velcdn.com/images/jhp21c/post/a261724c-b38e-45eb-a94a-443188adc771/image.png" /></p>
<p><img alt="" src="https://velog.velcdn.com/images/jhp21c/post/2d2d9497-574e-4617-adce-6d7b5412fb67/image.png" /></p>
<p><img alt="" src="https://velog.velcdn.com/images/jhp21c/post/3d7dcfe7-8202-4124-8e8c-5412bb316522/image.png" /></p>
<p>프로세스 창의 맨 끝의 <code>NAMES</code>는 컨테이너의 이름으로, 컨테이너 로그조회, 중지, 시작, 삭제 등의 명령을 직접 실행할 때 사용한다.</p>
<p><code>CONTAINER ID</code>의 앞글자 네자리로도 명령이 가능하다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhp21c/post/ebb9fb0e-e979-4c00-8778-fea399992ac7/image.png" /></p>
<p>실행중인 컨테이너가 없을 시의 화면이다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhp21c/post/a162c73b-5aab-43ce-aed7-af6ea79bea96/image.png" /></p>
<p>간단한 구조는 위와 같다.</p>