<p>리눅스에서 사용하는 텍스트 에디터의 한 종류이다. 
초보자의 경우는 <code>Nano</code>라는 에디터를 추천하지만 결국에는 부족한 부분이 있기에 처음부터 <code>vim</code>을 사용하는 것도 추천한다.</p>
<h2 id="vim-기본-사용법">vim 기본 사용법</h2>
<h3 id="실행법">실행법</h3>
<p>터미널에서 <code>vim [파일이름]</code>을 실행하면 해당 파일을 생성하여 편집할 수 있다.</p>
<h3 id="모드-변경">모드 변경</h3>
<p><img alt="" src="https://velog.velcdn.com/images/jhp21c/post/7829be6f-be50-4db3-8abc-f35e85cb585c/image.png" /></p>
<ul>
<li>명령 모드 : <code>vim</code>을 실행하면 가장 먼저 설정된 모드로서 <code>i</code>를 눌러 입력 모드로 편집할 수도 있고 <code>:</code>을 눌러 저장 및 종료를 할 수도 있다.</li>
<li>입력 모드 : 명령 모드에서 <code>i</code>를 눌러 입장하는 모드로, 직접적인 편집이 가능하다. <code>ESC</code>를 눌러 명령 모드로 복귀할 수 있다.</li>
<li>실행 모드 : 명령 모드에서 <code>:</code>을 눌러 입장하는 모드로, <code>:wq</code> 저장 및 종료 등 종료에 관한 실행을 할 수 있다.</li>
</ul>
<h3 id="기본-조작">기본 조작</h3>
<ul>
<li>편집<ul>
<li><code>i</code></li>
<li><code>I</code> : 현재 줄의 처음으로 이동</li>
<li>기타 여러가지가 존재한다.</li>
</ul>
</li>
<li>이동<ul>
<li>화살표키</li>
<li><code>gg</code> : 문서의 첫 줄 이동</li>
<li><code>[숫자]G</code> : 지정한 줄 번호로 이동</li>
<li><code>G</code> : 문서의 마지막 줄 이동</li>
</ul>
</li>
<li>행 삭제<ul>
<li><code>dd</code> : 현재 줄 삭제</li>
<li><code>[숫자]dd</code> : 지정한 수 만큼 삭제</li>
</ul>
</li>
<li>복사<ul>
<li><code>yy</code> or <code>Y</code> : 현재 줄 복사</li>
<li><code>[숫자]yy</code> : 지정한 수만큼 현재 줄 포함 아래로 줄 복사</li>
</ul>
</li>
<li>붙여넣기<ul>
<li><code>P</code> : 현재 커서 위치의 다음 줄에 삭제한 텍스트 붙여넣기</li>
<li><code>p</code> : 현재 커서 위치의 이전 줄에 붙여넣기</li>
</ul>
</li>
<li>찾기<ul>
<li><code>/[찾을 문자열]</code> : 문서에서 앞으로 찾을 문자열 검색</li>
<li><code>?[찾을 문자열]</code> : 문서에서 뒤로 찾을 문자열 검색</li>
<li><code>n</code> : 이전에 검색한 방향으로 다음 검색 결과로 이동</li>
<li><code>N</code> : 이전에 검색한 반대 방향으로 다음 검색 결과로 이동</li>
</ul>
</li>
<li>저장 및 종료<ul>
<li><code>:w</code> : 저장</li>
<li><code>:q</code> : 종료</li>
<li><code>:wq</code> : 저장 및 종료<h3 id="실수로-강제-종료되었다면">실수로 강제 종료되었다면?</h3>
<pre><code class="language-shell">vbnetCopy code
E325: ATTENTION
Found a swap file by the name &quot;.filename.swp&quot;
  while opening file &quot;filename&quot;
     dated: ...
  [not edited]
Do you want to recover this file? [Y]es/[N]o/[A]bort/[D]elete/[Q]uit/[R]ecover</code></pre>
위와 같은 메세지가 나타나면, R을 눌러 복구할 수 있다.</li>
</ul>
</li>
</ul>
<p><code>Vim</code>의 설정을 변경하여 <code>:set autowrite</code>를 사용하면, 모든 변경사항을 자동으로 저장하게끔 할 수도 있다.</p>