<p>AWS 서비스 개요 및 리눅스 명령어를 알아보자.</p>
<h2 id="aws">AWS</h2>
<p>아마존에서 제공하는 클라우드 플랫폼이며, 사용한 만큼 비용을 지불한다. 비슷한 서비스로는 Azure, GCP, Ncloud 등이 있다.</p>
<h3 id="기본-개념-및-이해">기본 개념 및 이해</h3>
<ul>
<li><code>리전(Region)</code> : 독립된 데이터 센터 그룹으로 구성된다.</li>
<li><code>가용 영역(Availability Zone)</code> : 가상 컴퓨팅 환경(서버)을 의미하며, EC2(Amazon Elastic Compute Cloud)를 통해 제공한다. </li>
<li><code>S3(Simple Storage Service)</code> : 객체 저장소 서비스로, 파일이나 데이터를 안전하게 저장하고 검색할 수 있다. 변하지 않는 자료 등을 저장하는 데 사용된다.</li>
<li><code>RDS(Relational Database Service)</code> : 관계형 데이터베이스 서비스로, 다양한 SQL 엔진을 제공한다. </li>
<li><code>Lambda</code> : 서버리스 컴퓨팅 서비스로, 코드를 실행할 수 있는 컴퓨팅 리소스를 제공한다. 사용자는 코드를 업로드하고 이벤트에 대한 응답으로 실행될 수 있도록 구성할 수 있다.</li>
<li><code>VPC(Virtual Private Cloud)</code> : 가상 사설 네트워크로, 사용자의 AWS 리소스를 구성하고 격리된 네트워크 환경을 생성할 수 있다.</li>
</ul>
<h3 id="주요-서비스">주요 서비스</h3>
<ul>
<li><p><code>Amazon EC2</code> </p>
<ul>
<li>가상 서버 제공 서비스</li>
</ul>
</li>
<li><p><code>AWS Lambda</code></p>
<ul>
<li>서버리스 컴퓨팅</li>
<li>트리거 기반 작동(이벤트)</li>
<li>코드 업로드 후 자동으로 실행</li>
</ul>
</li>
<li><p><code>S3</code></p>
</li>
<li><p><code>Amazon RDS</code></p>
</li>
<li><p><code>Amazon DynamoDB</code></p>
<ul>
<li>서버리스 NoSQL 서비스</li>
<li>필드의 스키마(정해진 형식)이 없고 자유로움</li>
<li>대규모 데이터 처리(사진 등)</li>
</ul>
</li>
<li><p><code>VPC</code>
등등 다양한 서비스들이 많이 있다.</p>
</li>
</ul>
<p>처음 회원가입을 하면 프리티어라고 정해진 기간동안 무료이니 사용해보면 좋을 것 같다. </p>
<h3 id="공인-ip와-사설-ip">공인 IP와 사설 IP</h3>
<p>Lightsail을 사용하면서 인스턴스 생성 시 IP가 필요한데 이때 사용하는 것이 공인 IP이다.</p>
<ul>
<li>Public IP : 전 세계적으로 유일하게 할당되며, 이는 인터넷 상에서 고유성을 부여하여 특정 기기와 통신을 가능하게 함</li>
<li>Private IP : 로컬 네트워크 내에서만 사용될 수 있는 주소이다. 유일하지 않으며, 로컬 네트워크 내에서만 유일하면 된다.</li>
</ul>
<p>공인 IP는 웹 서버나 이메일 서버 같은 인터넷에 직접 연결되어야 하는 서버, 원격에서 접근이 필요한 기기등에 쓰인다. </p>
<p>대부분의 가정이나 기업에서는 ISP로부터 공인 IP 주소를 할당받아 사용하며, 내부적으로는 사설 IP로 여러 기기를 연결한다.</p>
<h2 id="리눅스">리눅스</h2>
<p>SSH(Secure Shell)로 서버에 접속하기 위해 Tabby라는 프로그램을 설치한다. 
<img alt="" src="https://velog.velcdn.com/images/jhp21c/post/7155f769-3891-4db9-9a9f-04d8ce3eed73/image.png" /></p>
<p>설치후 실행하면 위와 같은 설정 화면을 볼 수 있다. </p>
<p>프로필 및 연결 &gt; 새프로필을 누르고 다음과 같이 설정한다. 
<img alt="" src="https://velog.velcdn.com/images/jhp21c/post/b6654689-3910-40a7-96a8-5e7f7587f121/image.png" /></p>
<p>비밀 키는 Lightsail 인스턴스 페이지에서 확인할 수 있다.
호스트에는 아까 말했던 공인 IP주소가 들어간다.</p>
<p>실행시키면 정상적으로 쉘이 나옴을 확인할 수 있다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhp21c/post/8bde34aa-5106-4b51-ad89-c8b55cf4a67f/image.png" /></p>
<h3 id="리눅스-1">리눅스?</h3>
<p>다양한 운영체제 중에 하나로, 유닉스 시스템을 기반으로 한 오픈 소스 소프트웨어이다. 사용 목적에 따라 다양한 배포판을 사용한다. (Ubuntu, Fedora, Debian, CentOS 등)</p>
<h3 id="커널과-쉘">커널과 쉘</h3>
<ul>
<li>커널 : OS 그 자체를 말하며, 하드웨어와 직접적으로 상호 작용한다. 사용자와 하드웨어 사이의 중재자 역할</li>
<li>쉘 : 사용자는 쉘을 통해 커널에 명령할 수 있다. 명령하는 방식은 GUI, CLI 2종류가 있으며 Bash, Zsh, Fish 등의 다양한 쉘을 사용할 수 있다.</li>
</ul>
<h3 id="디렉토리">디렉토리</h3>
<p>리눅스의 디렉토리 구조는 트리구조로 되어 있으며, 이를 파일시스템 트리라고도 부른다.</p>
<ul>
<li><code>/</code> : 루트 디렉토리이다.</li>
<li><code>bin</code> : 기본적인 실행 가능 프로그램(binary)들이 저장된 디렉토리</li>
<li><code>etc</code> : 시스템 설정 파일 디렉토리</li>
<li><code>home</code> : 각 사용자의 홈 디렉토리를 포함. 사용자의 이름이 p라면 <code>/home/p</code>가 해당 사용자의 홈 디렉토리이다.</li>
<li><code>var</code> : 시스템 운영 중에 생성되는 가변 데이터 디렉토리</li>
<li><code>usr</code> : 사용자 관련 앱 및 파일 저장 디렉토리</li>
<li><code>lib</code> : 시스템 라이브러리 파일 저장 디렉토리</li>
<li><code>dev</code> : 디바이스 파일 디렉토리. 리눅스에서는 하드웨어를 파일로 간주하여 모든 하드웨어 장치 파일이 포함된다.</li>
<li><code>tmp</code> : 일시적인 파일 저장 디렉토리</li>
</ul>
<h3 id="경로와-확장자">경로와 확장자</h3>
<ul>
<li>절대 경로 : 루트 디렉토리에서 시작하여 특정 파일이나 디렉토리에 도달하는 완전한 경로</li>
<li>상대 경로 : 현재 디렉토리에 기반한 특정 파일이나 디렉토리의 위치</li>
<li>확장자 : 파일의 종류나 형식을 나타낸다. <code>.txt</code> 등. 리눅스에서는 파일의 유형을 결정하는 데 메타데이터와 <code>magic numbers</code>를 더 많이 사용한다. 따라서 선택사항이며, 생략되기도 한다.</li>
</ul>
<h3 id="기본적인-제어">기본적인 제어</h3>
<ul>
<li><p>cd : <code>change directory</code>의 약자로, 디렉토리(폴더)를 이동할 때 사용한다.</p>
<ul>
<li>cd만 입력하면 사용자의 홈 디렉토리로 이동한다.</li>
<li>cd [디렉토리 경로]</li>
<li>cd ~ : <code>~</code> 는 사용자의 홈 디렉토리를 의미한다.</li>
<li>cd - : 직전 디렉토리로 돌아간다.<pre><code class="language-shell">cd ..</code></pre>
</li>
</ul>
</li>
<li><p>pwd : <code>print working directory</code>의 약자로, 현재 작업 중인 디렉토리의 절대 경로를 출력해준다.</p>
</li>
<li><p>ls : <code>list</code>의 약자로, 디렉토리의 내용물을 나열한다.</p>
<ul>
<li><code>-l</code> 옵션으로 세부적인 정보를 같이 확인 가능</li>
<li><code>-a</code> : <code>.</code>으로 시작하는 숨김 파일을 포함해서 보여줌</li>
<li>이외에도 다양한 명령어가 존재한다.<pre><code class="language-shell">ls -al</code></pre>
</li>
</ul>
</li>
<li><p>mv : <code>move</code>의 약자로 파일이나 디렉토리를 이동하거나 이름을 변경하는 데 사용한다.</p>
<pre><code class="language-shell"># 윈도우의 잘라내기 후 붙여넣기와 같음
# 파일 이동하기
mv file.txt (이동할 경로)</code></pre>
<pre><code class="language-shell"># 파일 이름 변경
mv file.txt (변경할 이름)</code></pre>
<p>이러한 <code>mv</code> 명령어는 대상 파일을 덮어쓸 수 있으므로 주의가 필요하다. </p>
<ul>
<li><code>-i</code> 옵션 :  덮어쓰기 전에 확인 요청을 보냄<pre><code class="language-shell">mv -i file1.txt file2.txt</code></pre>
</li>
</ul>
</li>
<li><p>cp : 파일(디렉토리)을 복사하는 데 사용</p>
<pre><code class="language-shell">cp myfile.txt myfile_copy.txt</code></pre>
<ul>
<li><code>mv</code>와 똑같이 <code>-i</code> 옵션이 존재한다.</li>
</ul>
</li>
<li><p>mkdir : <code>make directory</code>의 약자로, 새로운 디렉토리를 생성한다.</p>
<pre><code class="language-shell"># dir이라는 디렉토리 생성
mkdir dir1</code></pre>
<pre><code class="language-shell"># 부모 디렉토리/자식디렉토리 구조의 디렉토리를 생성하며 
# 부모 디렉토리가 존재하지 않으면 먼저 생성함

mkdir -p parentdir/childdir</code></pre>
</li>
<li><p>rmdir : <code>remove directory</code>의 약자로, 빈 디렉토리를 삭제하는 명령어이다. 보통 이것 대신 rm에 옵션을 주어 사용한다고 한다.</p>
<pre><code class="language-shell">  rmdir testDir</code></pre>
</li>
<li><p>rm : <code>remove</code>의 약자로 파일이나 디렉토리를 삭제하는 명령어</p>
<pre><code class="language-shell">rm [옵션] [파일 또는 디렉터리]</code></pre>
<ul>
<li>다양한 옵션<ul>
<li><code>-i</code> : 삭제하기 전에 확인 요청</li>
<li><code>-f</code> : 강제로 삭제</li>
<li><code>-r</code> : 디렉토리와 그 내부의 모든 파일과 서브 디렉토리를 재귀적으로 삭제</li>
<li><code>-v</code> : 삭제된 파일 정보 출력</li>
</ul>
</li>
</ul>
<pre><code class="language-shell"># 일반적인 파일 삭제
rm test.txt</code></pre>
<pre><code class="language-shell"># 특히 주의 필요
# 현재 폴더의 모든 내용을 삭제하는 명령어
# 복구가 어려우니 조심해야 한다.
# 특정 파일이나 디렉토리를 지정하는 것을 권장한다.
rm -rf ./*</code></pre>
</li>
<li><p>touch : 파일을 생성하거나 타임스탬프를 변경하는 데 사용</p>
<pre><code class="language-shell"># example이라는 이름의 텍스트 파일 생성
touch example.txt</code></pre>
<pre><code class="language-shell"># 특정 시간으로 파일 타임스탬프 변경
touch -t 202306231200 example.txt</code></pre>
</li>
</ul>
<p>이러한 작업 중 권한 오류가 생긴다면 <code>sudo</code> 수식어를 붙임으로서 대부분 해결이 가능하다.</p>
<p>보통 현재 유저의 디렉토리가 아닌 <code>home</code> 디렉토리 혹은 그 이상의 디렉토리에서 작업할 때 발생하는 오류이다. </p>
<p>이는 다른 유저의 파일이나 폴더를 마음대로 다루지 않게끔 하기 위함이다.</p>