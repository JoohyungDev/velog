<p>서버 관리와 파일 전송에 사용되는 SSH(Secure Shell)을 알아보겠습니다.</p>
<h2 id="명령어">명령어</h2>
<h3 id="정의">정의</h3>
<p>사용자가 원격으로 다른 컴퓨터에 안전하게 접속하도록 도와주는 프로토콜이다.</p>
<p>기본 형태는 다음과 같다.</p>
<pre><code class="language-shell">ssh [options] [user@]hostname</code></pre>
<ul>
<li><code>user@</code> : 로그인 할 사용자 이름</li>
<li><code>hostname</code> : 원격 서버의 도메인 이름 혹은 IP 주소</li>
</ul>
<h3 id="다른-포트를-사용하여-원격-서버-접속">다른 포트를 사용하여 원격 서버 접속</h3>
<pre><code class="language-shell">ssh -p port username@remote_host</code></pre>
<p>기본적으로 22포트를 사용하며, <code>port</code> 자리에 다른 포트 번호를 명시할 수 있다.</p>
<h3 id="키파일을-사용하여-원격-서버-접속">키파일을 사용하여 원격 서버 접속</h3>
<pre><code class="language-shell">ssh -i /path/to/my_key.pem username@remote_host</code></pre>
<p>키가 있는 경로에서 실행 혹은 경로를 명시하여 private key로 접속이 가능하다.</p>
<h3 id="원격-서버에로컬-파일-복사">원격 서버에로컬 파일 복사</h3>
<pre><code class="language-shell">scp local_file username@remote_host:/remote/directory/</code></pre>
<p><code>local_file</code>이라는 이름의 로컬 파일을 원격 호스트의 <code>/remote/directory/</code>로 복사하는 명령이다.</p>
<h2 id="비밀번호-접속">비밀번호 접속</h2>
<p>키로 인스턴스에 접속하는 것이 안전하긴 하지만 항상 키를 갖고 다닐 순 없다. 따라서 비밀번호로 접속을 하는 방법을 알아보자.</p>
<pre><code class="language-shell"># ssh 설정 파일 수정
sudo vim /etc/ssh/sshd.config</code></pre>
<pre><code class="language-shell"># /etc/ssh/sshd.config
...


PasswordAuthentication no  -&gt; yes 로 변경

...</code></pre>
<p>파일 초중반 부에 있는 <code>Password...</code> 인증 부분을 허용한다.</p>
<pre><code class="language-shell"># 서비스 재시작
sudo systemctl restart ssh </code></pre>
<p>이후 키로 접속한 것과 마찬가지로 </p>
<pre><code class="language-shell">
ssh {사용자이름}@{ip주소}</code></pre>
<p>와 같이 접속 가능하다.</p>
<h2 id="django-서버-생성">Django 서버 생성</h2>
<p>Lightsail을 활용하여 간단하게 서버를 만들어 보자.</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhp21c/post/8733ec23-9882-46ba-84e1-75703e024cc2/image.png" /></p>
<p><img alt="" src="https://velog.velcdn.com/images/jhp21c/post/9c7f9e2b-a646-41d9-aef3-c8359ca08302/image.png" />
보통은 위 사진처럼 Django를 직접 선택하지 않고 OS만 선택하지만 간단한 진행을 위해 선택하였다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhp21c/post/e76980c8-f14b-4cbe-a76a-fbd914dca78b/image.png" /></p>
<p><img alt="" src="https://velog.velcdn.com/images/jhp21c/post/a36377c8-a258-44ad-bbd4-b19419b1d697/image.png" /></p>
<p>인스턴스 아이디에는 규칙이 존재한다.</p>
<ul>
<li>규칙 : 유저아이디-프로젝트 이름</li>
</ul>
<p>이후 인스턴스를 생성하고 활성화까지 대기한다.</p>
<p>활성화가 되었으면 다음과 같은 버튼을 클릭하여 터미널에 들어간다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhp21c/post/a5b20dbb-5f87-4ca0-aaa1-e1e59d858dab/image.png" /></p>
<p><img alt="" src="https://velog.velcdn.com/images/jhp21c/post/c8a5d6d3-9c53-4d36-ad29-be84ff6454f3/image.png" /></p>
<p>이후 깃허브에서 django todolist 소스코드를 클론한다.</p>
<pre><code class="language-shell">git clone https://github.com/beomjae/django-todolist.git</code></pre>
<p><img alt="" src="https://velog.velcdn.com/images/jhp21c/post/9ed9f72e-4601-422b-ac96-c8cdea163223/image.png" /></p>
<p>다운받은 소스코드 디렉토리로 이동한다.</p>
<pre><code class="language-shell">cd django-todolist</code></pre>
<p>정의되어 있는 라이브러리를 설치한다. </p>
<pre><code class="language-shell">pip install -r requirements.txt</code></pre>
<p>데이터베이스 변경내역을 데이터베이스에 적용하기 위해 마이그레이트를 진행한다.</p>
<pre><code class="language-shell"># migrate
python manage.py migrate</code></pre>
<p>8000번 포트로 Django 서버를 실행한다.</p>
<pre><code class="language-shell">python manage.py runserver 0.0.0.0:8000</code></pre>
<p>만약, 8000번 포트가 막혀 있다면, Lightsail 인스턴스의 네트워킹탭에서 포트를 추가한다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhp21c/post/acfd086b-d560-485e-9fb2-ddac53c504d2/image.png" /></p>
<p><img alt="" src="https://velog.velcdn.com/images/jhp21c/post/fa587355-7f78-44d4-9d87-760a1017d02b/image.png" />
서버 생성이 완료된 모습이다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhp21c/post/6d96b1d2-fa7b-4b83-810f-583a29bdceb0/image.png" />
간단한 구조 그림이다.</p>