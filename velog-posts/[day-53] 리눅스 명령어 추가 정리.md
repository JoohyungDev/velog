<p>앞에서 설명한 명령어 외에 추가적인 명령어를 알아보자.</p>
<h2 id="리다이렉트">리다이렉트</h2>
<p>출력 결과를 파일에 저장하거나, 파일로부터 입력을 받는 등의 작업을 수행할 수 있다.</p>
<pre><code class="language-shell"># ls의 결과를 file.txt로 저장
ls &gt; file.txt</code></pre>
<p>꺽새가 2개이면 기존 파일의 마지막에 결과를 추가한다.</p>
<pre><code class="language-shell"># file.txt의 내용을 sort 명령에 입력으로 전달
sort &lt; file.txt</code></pre>
<pre><code class="language-shell"># 해당 폴더에 대한 ls 명령의 에러 메세지를 error.txt로 저장
ls /nonexistent_directory 2&gt; error.txt</code></pre>
<h2 id="링크">링크</h2>
<p>윈도우의 바로가기와 비슷하다고 생각하면 된다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhp21c/post/598735cd-97ef-438d-a10c-d7a311c9a2b8/image.png" /></p>
<ul>
<li>하드 링크 : 원본 파일과 동일한 노드를 가르킨다. 원본 파일이 삭제되어도 데이터에 엑세스가 가능하다.</li>
<li>심볼릭 링크 : 윈도우의 바로가기. 원본 파일이 삭제되면 유효하지 않음</li>
</ul>
<pre><code class="language-shell"># hardlink라는 이름의 하드 링크를 생성
ln source_file hardlink</code></pre>
<pre><code class="language-shell"># symlink라는 이름의 심볼릭 링크 생성
ln -s source_file symlink</code></pre>
<h2 id="메모리와-디스크-명령">메모리와 디스크 명령</h2>
<h3 id="메모리-사용량">메모리 사용량</h3>
<p><img alt="" src="https://velog.velcdn.com/images/jhp21c/post/effeb4ff-fd84-496f-8760-bfb6ddad7531/image.png" /></p>
<p><code>-h</code>옵션을 주면 조금더 보기 쉽게 표현해준다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhp21c/post/1d37943b-000a-4b4b-9d82-f481d9d130ed/image.png" /></p>
<p><img alt="" src="https://velog.velcdn.com/images/jhp21c/post/ac07f5a3-c1c2-4400-a47b-83f0e058abc0/image.png" /></p>
<p>실시간으로(5초마다) 메모리 사용 상태를 확인할 수도 있다.</p>
<h3 id="디스크-사용량">디스크 사용량</h3>
<p><code>df</code>는 <code>disk filesystem</code>의 약자로, 파일 시스템의 디스크 사용량을 확인하는 명령어이다.</p>
<pre><code class="language-shell">df -h</code></pre>
<pre><code class="language-shell">Filesystem      Size  Used Avail Use% Mounted on
udev            3.9G     0  3.9G   0% /dev
tmpfs           798M  1.2M  797M   1% /run
/dev/sda1       154G   85G   59G  59% /
tmpfs           3.9G   31M  3.9G   1% /dev/shm
tmpfs           5.0M  4.0K  5.0M   1% /run/lock
tmpfs           3.9G     0  3.9G   0% /sys/fs/cgroup
/dev/loop1      128K  128K     0 100% /snap/someapp/34
/dev/loop2      3.8M  3.8M     0 100% /snap/anotherapp/2</code></pre>
<p>위와 같이 나오게 된다.</p>
<h2 id="사용자-관련-명령">사용자 관련 명령</h2>
<h3 id="비밀번호-변경">비밀번호 변경</h3>
<p><code>passwd</code>는 사용자 암호를 변경하거나 새로운 암호를 설정하는 데 사용한다.</p>
<ul>
<li>본인 암호 변경 : <code>passwd</code></li>
<li>다른 사용자 암호 변경 : <code>passwd [사용자]</code></li>
<li>사용자 암호 잠금 : <code>passwd -l [사용자]</code></li>
<li>사용자 암호 잠금 해제 : <code>passwd -u [사용자]</code></li>
<li>사용자 암호 삭제 : <code>passwd -d [사용자]</code></li>
<li>사용자 암호 즉시 만료 설정 : <code>passwd -e [사용자]</code></li>
</ul>
<h3 id="권한">권한</h3>
<p>파일, 디렉토리의 액세스 권한을 말한다. 읽기(Read, r), 쓰기(Write, w), 실행(Execute, x) 총 3개의 권한이 존재한다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhp21c/post/912dcf28-b1a0-4860-b738-090ccb12c734/image.png" /></p>
<p>위와 같은 형식으로 표현되며, 맨 앞 글자는 어떤 형태의 파일인지 알려주고 나머지는 세글자 단위로 표현된다.</p>
<p>첫 세글자는 소유자, 가운데 세글자는 그룹, 마지막 세글자는 기타 사용자 순서이다.</p>
<p>r, w, x중에 없는 권한이면 <code>-</code>표시로 나타낸다.</p>
<ul>
<li>첫번째 문자</li>
</ul>
<table>
<thead>
<tr>
<th>첫번째 문자</th>
<th>설명</th>
</tr>
</thead>
<tbody><tr>
<td>-</td>
<td>일반 파일</td>
</tr>
<tr>
<td>d</td>
<td>디렉토리</td>
</tr>
<tr>
<td>l</td>
<td>심볼릭 링크</td>
</tr>
<tr>
<td>b</td>
<td>블록 스페셜 파일</td>
</tr>
<tr>
<td>c</td>
<td>문자 스페셜 파일</td>
</tr>
<tr>
<td>p</td>
<td>파이프</td>
</tr>
<tr>
<td>s</td>
<td>소켓</td>
</tr>
</tbody></table>
<ul>
<li>권한 위치(순서)</li>
</ul>
<table>
<thead>
<tr>
<th>권한 위치 (순서)</th>
<th>사용자 범주</th>
</tr>
</thead>
<tbody><tr>
<td>2-4번째 문자</td>
<td>소유자</td>
</tr>
<tr>
<td>5-7번째 문자</td>
<td>그룹</td>
</tr>
<tr>
<td>8-10번째 문자</td>
<td>기타 사용자</td>
</tr>
</tbody></table>
<ul>
<li>권한 문자</li>
</ul>
<table>
<thead>
<tr>
<th>권한 문자</th>
<th>권한</th>
</tr>
</thead>
<tbody><tr>
<td>r</td>
<td>읽기</td>
</tr>
<tr>
<td>w</td>
<td>쓰기</td>
</tr>
<tr>
<td>x</td>
<td>실행</td>
</tr>
<tr>
<td>-</td>
<td>해당 권한 없음</td>
</tr>
</tbody></table>
<h3 id="권한-변경">권한 변경</h3>
<p><code>chmod</code>와 연산자를 사용하여 권한을 변경할 수 있다.</p>
<p>예를 들어, <code>chmod g+w filename</code>은 그룹에 쓰기 권한을 추가하는 명령이며 <code>chmod a-r filename</code>은 모든 사용자의 읽기 권한을 삭제하는 명령이다.</p>
<ul>
<li>연산자</li>
</ul>
<table>
<thead>
<tr>
<th>연산자</th>
<th>설명</th>
</tr>
</thead>
<tbody><tr>
<td>+</td>
<td>권한 추가</td>
</tr>
<tr>
<td>-</td>
<td>권한 삭제</td>
</tr>
<tr>
<td>=</td>
<td>권한 설정</td>
</tr>
</tbody></table>
<ul>
<li>사용자 범주</li>
</ul>
<table>
<thead>
<tr>
<th>사용자 범주</th>
<th>설명</th>
</tr>
</thead>
<tbody><tr>
<td>u</td>
<td>소유자</td>
</tr>
<tr>
<td>g</td>
<td>그룹</td>
</tr>
<tr>
<td>o</td>
<td>기타 사용자</td>
</tr>
<tr>
<td>a</td>
<td>모든 사용자</td>
</tr>
</tbody></table>
<ul>
<li>권한</li>
</ul>
<table>
<thead>
<tr>
<th>권한</th>
<th>설명</th>
</tr>
</thead>
<tbody><tr>
<td>r</td>
<td>읽기</td>
</tr>
<tr>
<td>w</td>
<td>쓰기</td>
</tr>
<tr>
<td>x</td>
<td>실행</td>
</tr>
</tbody></table>
<p>숫자 모드로도 변경할 수 있는데, 읽기는 4, 쓰기는 2, 실행은 1로 표현하며 합산하여 권한을 나타낸다. </p>
<p>예를 들어, <code>chmod 755 filename</code>은 소유자에게 읽기(4), 쓰기(2), 실행(1) 권한(합산하여 7)을, 그룹과 기타 사용자에게는 읽기(4)와 실행(1) 권한(합산하여 5)을 부여하는 명령이다.</p>
<h2 id="쉘스크립트">쉘스크립트</h2>
<p>자동화 작업을 수행하기 위한 스크립트 언어이다. 텍스트 파일로 작성되며, 이 파일에는 실행할 쉘 명령이 포함된다.</p>
<pre><code class="language-shell"># 스크립트가 zsh쉘에서 실행되어야 함을 명시
#!/usr/bin/zsh

# echo : 문자열이나 변수를 출력하는 명령
echo &quot;Hello, World!&quot;</code></pre>
<p>위 파일을 <code>test.sh</code>라는 파일로 저장했을 때, 쉘에서 ./test.sh로 실행하면 <code>Hello, World!</code> 가 출력된다.</p>
<h2 id="멀티-태스크와-프로세스-제어">멀티 태스크와 프로세스 제어</h2>
<ul>
<li><code>태스크</code> : 작업의 가장 기본적인 단위이다. 사용자가 시스템에게 명령을 내릴 때 생성된다.</li>
<li><code>프로세스</code> : 실행중인 프로그램이다. 하나의 프로세스는 하나 이상의 스레드를 가질 수 있으며, 각 스레드는 독립적인 실행 경로를 가진다. 다른 프로세스의 공간에 직접 접근이 불가하다.</li>
<li><code>잡</code> : 일련의 연관된 프로세스의 그룹이다. 쉘에서 명령어를 실행하면, 그 명령어는 잡으로 간주되며, 잡은 하나 이상의 프로세스로 구성될 수 있다. </li>
<li><code>ps</code> : <code>Process Status</code>의 약자로, 현재 시스템에서 실행 중인 프로세스의 정보를 출력해준다.<ul>
<li><code>ps -ef</code> : 모든 프로세스의 정보를 볼 수 있음</li>
<li><code>ps -u [사용자명]</code> : 특정 사용자에 의해 실행된 프로세스를 보여줌</li>
<li><code>ps -l</code> : 프로세스의 자세한 정보 출력</li>
<li><code>ps -p [프로세스 ID]</code> : 특정 프로세스 ID에 대한 정보 출력</li>
</ul>
</li>
</ul>
<p>앞에서 설명했던 <code>grep</code>과 함께 자주 사용된다.</p>
<pre><code class="language-shell"># 현재 실행중인 모든 프로세스 중, 특정 프로세스의 정보만 출력
ps -ef | grep [process name]</code></pre>
<h3 id="jobs">jobs</h3>
<p>현재 쉘에서 실행 중인 작업(job)의 목록을 보여준다. 주로 <code>&amp;</code> 명령어로 백그라운드에서 실행하거나, <code>ctrl+z</code>를 사용하여 프로세스를 일시 중단한 다음 사용한다.</p>
<table>
<thead>
<tr>
<th>옵션</th>
<th>설명</th>
</tr>
</thead>
<tbody><tr>
<td>-l</td>
<td>각 작업의 프로세스 ID를 같이 표시합니다.</td>
</tr>
<tr>
<td>-n</td>
<td>마지막에 보여준 것 이후에 상태가 변경된 작업들만 표시합니다.</td>
</tr>
<tr>
<td>-p</td>
<td>각 작업의 프로세스 ID만 표시합니다.</td>
</tr>
<tr>
<td>-r</td>
<td>실행 중인 작업들만 표시합니다.</td>
</tr>
<tr>
<td>-s</td>
<td>중지된 작업들만 표시합니다.</td>
</tr>
</tbody></table>
<pre><code class="language-shell">$ sleep 100 &amp;
[1] 12345

$ jobs
[1]+  Running                 sleep 100 &amp;

$ jobs -l
[1]+ 12345 Running                 sleep 100 &amp;</code></pre>
<h3 id="kill">kill</h3>
<p>프로세스를 종료시키거나 강제로 종료시킬 때 사용한다.</p>
<pre><code class="language-shell">kill [options] &lt;pid&gt;</code></pre>
<p>만약, 강제로 종료시킨다면 다음과 같이 명령한다.</p>
<pre><code class="language-shell">kill -9 1234</code></pre>
<p>여기서 <code>-9</code>가 강제 종료 옵션이다.</p>
<h3 id="시그널">시그널</h3>
<p>프로세스간에 통신하는 방법 중 하나이다. 비동기적인 통지 시스템으로, 한 프로세스(또는 운영체제 자체)가 다른 프로세스에게 어떤 사건이 발생했음을 알리는데 사용한다.</p>
<h2 id="시스템-관리">시스템 관리</h2>
<h3 id="사용자-전환하기">사용자 전환하기</h3>
<ul>
<li>su 명령 : <code>switch user</code>의 약자로, 로그인한 사용자의 ID를 다른 사용자의 ID로 변경한다.</li>
</ul>
<pre><code class="language-shell">su -        # root 사용자로 전환합니다.
su - john   # 'john'이라는 사용자로 전환합니다.
su john   # 'john'이라는 사용자로 전환합니다.
su -c whoami john # 'john'이라는 사용자로 전환하여 'whoami' 명령어를 실행하고 다시 원래 사용자로 돌아갑니다.</code></pre>
<ul>
<li>sudo 명령 : 관리자 권한이 필요한 작업을 수행할 때 사용한다.</li>
</ul>
<pre><code class="language-shell">sudo apt-get update</code></pre>
<p><code>sudo</code>를 통해 실행할 수 있는 명령은 <code>/etc/sudoers</code> 파일에서 정의되며, 이 파일은 <code>sudo visudo</code> 명령을 통해 안전하게 편집할 수 있다.</p>
<p>새로 정의한 사용자를 <code>sudoers</code>에 추가하기 위해서는 다음과 같은 과정을 거친다.</p>
<pre><code class="language-shell">sudo visudo
# visudo 파일 안에서 해당 라인 추가
[사용자 이름] ALL=(ALL:ALL) ALL</code></pre>
<p>여기서 첫 번째 <code>ALL</code>은 모든 호스트에서, 두 번째 <code>(ALL:ALL)</code>은 모든 사용자와 그룹으로, 세 번째 <code>ALL</code>은 모든 명령을 실행할 수 있음을 의미한다.</p>
<h3 id="서비스-관련">서비스 관련</h3>
<p><code>systemctl</code>은 서비스를 시작하고, 중지하고, 재시작하거나 그 상태를 확인하는 등의 작업을 수행할 수 있다.</p>
<pre><code class="language-shell"># 서비스 시작
systemctl start [서비스이름]

# 서비스 중지
systemctl stop [서비스이름]

# 서비스 재시작
systemctl restart [서비스이름]

# 서비스 상태 확인
systemctl status [서비스이름]

# 시스템 부팅 시 서비스 자동 시작 설정
systemctl enable nginx.service

# 시스템 부팅 시 서비스 자동 시작 설정 해제
systemctl disable nginx.service</code></pre>
<h3 id="네트워크-명령">네트워크 명령</h3>
<ul>
<li><p><code>ifconfig</code> : 네트워크 구성을 보거나 저장하는 데 사용한다.
<code>sudo apt install net-tools</code>를 설치하여 사용해야 하며
최근에는 <code>ip</code> 명령으로 대체되고 있다고 한다.</p>
</li>
<li><p><code>dig</code> : DNS(Domain Name System) 서버에 쿼리를 전송하여 정보를 얻는 데에 사용한다.</p>
</li>
</ul>
<pre><code class="language-shell">$ dig google.com

; &lt;&lt;&gt;&gt; DiG 9.10.3-P4-Ubuntu &lt;&lt;&gt;&gt; google.com
;; global options: +cmd
;; Got answer:
;; -&gt;&gt;HEADER&lt;&lt;- opcode: QUERY, status: NOERROR, id: 54830
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;google.com.                    IN      A

;; ANSWER SECTION:
google.com.             299     IN      A       216.58.210.14

;; Query time: 32 msec
;; SERVER: 8.8.8.8#53(8.8.8.8)
;; WHEN: Mon Jul 17 10:46:22 UTC 2017
;; MSG SIZE  rcvd: 55</code></pre>
<p>ip 주소 등을 확인 가능하다.</p>
<h3 id="사용자-생성과-삭제">사용자 생성과 삭제</h3>
<ul>
<li><code>adduser</code> : 사용자 생성 시 사용<pre><code class="language-shell">adduser [옵션] 사용자명</code></pre>
</li>
</ul>
<p>위 코드를 실행하면, 해당 사용자의 홈 디렉토리를 만들고, 비밀번호를 설정하고, 시스템에 필요한 다른 설정을 수행한다.</p>
<ul>
<li><code>deluser</code> : 사용자 제거<pre><code class="language-shell"># 사용자 제거
sudo deluser username
</code></pre>
</li>
</ul>
<h1 id="사용자-계정-홈-디렉토리-삭제">사용자 계정, 홈 디렉토리 삭제</h1>
<p>sudo deluser --remove-home username</p>
<pre><code>
### 그룹 관리
사용자들이 자원을 공유하는 데 사용하는 도구이다. 각 사용자는 기본 그룹에 속하며, 추가적으로 여러 그룹의 멤버가 될 수 있다. 관련 정보는 `/etc/group` 파일에 저장된다.

- `chown` : 파일 또는 디렉토리의 소유자를 변경
```shell
chown new_owner filename

# 한 번에 소유자와 그룹을 모두 변경
chown new_owner:new_group filename</code></pre><ul>
<li><p><code>groupadd</code> : 그룹 생성</p>
<pre><code class="language-shell">sudo groupadd developers</code></pre>
</li>
<li><p><code>usermod -a -G</code> : 그룹에 사용자 추가</p>
<pre><code class="language-shell">sudo usermod -a -G developers john</code></pre>
<p>여기서 <code>-a</code>는 사용자를 그룹에 추가하고, <code>-G</code>는 추가할 그룹을 지정하는 옵션이다.</p>
</li>
<li><p><code>groupdel</code> : 그룹 삭제</p>
<pre><code class="language-shell">sudo groupdel developers</code></pre>
</li>
<li><p><code>groups</code> : 그룹 확인</p>
<pre><code class="language-shell">groups john</code></pre>
</li>
</ul>
<h3 id="종료와-재시작">종료와 재시작</h3>
<ul>
<li>종료<pre><code class="language-shell"># 즉시 종료
sudo shutdown -h now
sudo poweroff
</code></pre>
</li>
</ul>
<h1 id="m분-후-종료">m분 후 종료</h1>
<p>sudo shutdown -h +m</p>
<pre><code>
- 재시작
```shell
# 즉시 재시작
sudo shutdown -r now 
sudo reboot

# m분 후 재시작
sudo shutdown -r +m</code></pre><h3 id="사용자-환경-설정vim">사용자 환경 설정(Vim)</h3>
<ul>
<li><code>~/.vimrc</code> <ul>
<li>Vim 사용자 개인의 환경 설정 파일</li>
<li>Vim의 다양한 옵션, 키 매핑, 플러그인 설정 등을 구성</li>
<li>새로운 Vim 세션이 시작될 때마다 자동으로 실행
다른 파일들도 있지만 보통 <code>~/.vimrc</code> 파일을 수정하여 설정을 변경한다.</li>
</ul>
</li>
</ul>
<h3 id="애플리케이션-설치-및-삭제">애플리케이션 설치 및 삭제</h3>
<ul>
<li><p>설치</p>
<pre><code class="language-shell">sudo apt update
sudo apt install [package-name]</code></pre>
</li>
<li><p>삭제</p>
<pre><code class="language-shell"># 패키지의 구성 파일은 제거하지 않는 삭제
sudo apt remove [package-name]
</code></pre>
</li>
</ul>
<h1 id="완전-삭제">완전 삭제</h1>
<p>sudo apt purge [package-name]</p>
<h1 id="삭제한-후-필요-없는-종속성-제거">삭제한 후 필요 없는 종속성 제거</h1>
<p>sudo apt autoremove</p>
<pre><code></code></pre>