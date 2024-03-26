<p>어제에 이어 리눅스 명령어를 알아보도록 하자.</p>
<ul>
<li><code>cat</code> : <code>concatenate</code>의 약자로, 파일의 내용을 표준 출력으로 보내거나 파일을 연결하거나 생성하는 데 사용한다.<pre><code class="language-shell"># 파일의 내용 출력
cat filename.txt</code></pre>
</li>
</ul>
<pre><code class="language-shell"># file1과 file2의 내용을 결합하고, 결과를 mergedfile에 저장
cat file1.txt file2.txt &gt; mergedfile.txt</code></pre>
<p>만약, 같은 이름의 파일이 존재한다면 완전히 덮어씌운다.</p>
<p>꺽새가 2개라면(&gt;&gt;) 같은 이름의 파일이 존재하면 그 파일의 하단에 내용을 추가한다. 원본을 보존한다는 말이다.</p>
<pre><code class="language-shell"># vim으로 편집하는게 아닌 shell에서 바로 내용 추가 가능
cat &gt; newfile.txt</code></pre>
<pre><code class="language-shell"># file1의 내용을 file2에 복사
cat file1.txt &gt; file2.txt</code></pre>
<ul>
<li><p><code>sort</code> : 말 그대로 특정 파일의 내용을 정렬하는 명령어이다.</p>
</li>
<li><p><code>grep</code> : <code>Global Regular Expression Print</code>의 약자로, 텍스트의 패턴을 검색하는 데 사용한다.</p>
</li>
</ul>
<pre><code class="language-shell">grep [옵션] [찾고자 하는 패턴] [파일]
grep &quot;test&quot; test.txt</code></pre>
<p>위 코드는 test.txt 파일에서 test라는 단어를 찾아서 반환한다.</p>
<pre><code class="language-shell">grep -r &quot;test&quot; .</code></pre>
<p><code>recursive(재귀)</code>의 약자로, 현재 폴더(.) 부터 하위 폴더까지 재귀적으로 test라는 단어를 찾는 코드이다.</p>
<p><code>grep</code> 명령어는 파이프(<code>|</code>)와 함께 사용되어 다른 명령의 출력에서 특정 패턴을 검색하는 데 사용된다.</p>
<p>파이프는 한 명령의 출력을 다른 명령의 입력으로 전달한다는 특징이 있다.</p>
<pre><code class="language-shell">ps aux | grep 'python'</code></pre>
<p>실행중인 프로세스 중에서 python이라는 문자열이 포함된 프로세스를 찾는 코드이다. </p>
<ul>
<li><code>find</code> : 파일을 찾기 위한 명령어이다.<pre><code class="language-shell">find [경로] [표현식]</code></pre>
</li>
</ul>
<pre><code class="language-shell"># example.txt라는 이름의 파일 찾기
find . -name example.txt</code></pre>
<pre><code class="language-shell"># home 경로에서 readme라는 이름을 대소문자 구분하지 않고 찾기
find /home -iname readme</code></pre>
<ul>
<li><p><code>which</code> : 특정한 실행 가능 파일의 경로를 찾아준다.</p>
<pre><code class="language-shell"># 파이썬이 어디에 설치되어 있는지 확인
which python</code></pre>
</li>
<li><p><code>whereis</code> : 특정 바이너리, 소스, 메뉴얼 페이지 파일의 위치를 찾을 때 사용한다.</p>
</li>
</ul>
<pre><code class="language-shell">whereis ls</code></pre>
<ul>
<li>와일드카드 : 명령어는 아니지만 명령어 사용시에 사용하기에 적어본다.</li>
</ul>
<p>파일들을 일치시키는 데 사용되는 심볼이나 문자이다.</p>
<p><code>*</code>, <code>?</code>, <code>[]</code> 가 가장 흔하게 사용된다.</p>
<ul>
<li><code>*</code> : <code>*.txt</code> 라고 하면 모든 텍스트 파일을 찾는다.</li>
<li><code>?</code> : <code>?.txt</code> 라고 하면 한 문자로 된 모든 텍스트 파일을 찾는다.</li>
<li><code>[]</code> : <code>[abc].txt</code> 라고 하면 a.txt, b.txt, c.txt와 일치한다.</li>
</ul>
<p>파이썬의 정규표현식과 비슷한 느낌이라고 보면 될 것 같다.</p>