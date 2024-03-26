<p>SSH로 Lightsail 인스턴스 접속을 하던 중 오류가 발생했고 해결 과정을 적어보겠습니다.</p>
<h2 id="문제">문제</h2>
<p><img alt="" src="https://velog.velcdn.com/images/jhp21c/post/6dd3b8c1-9fee-4116-995c-f6787f49a995/image.png" /></p>
<p>tabby라는 터미널 프로그램을 사용하여 SSH 방식으로 Lightsail 인스턴스에 접속하는 도중 오류가 발생하였다. </p>
<pre><code class="language-shell">ssh -i /path/to/my_key.pem username@remote_host</code></pre>
<p>위와 같이 쳣을때 사진과 같은 오류가 발생한다면 다음 스텝을 밟아보자.</p>
<h2 id="해결">해결</h2>
<ol>
<li><p><code>.pem</code> 파일 우클릭</p>
</li>
<li><p>속성 &gt; 보안 &gt; 고급
<img alt="" src="https://velog.velcdn.com/images/jhp21c/post/d66492b2-8d9e-4eda-b1f7-2abcb47b50d0/image.png" /></p>
</li>
<li><p>맨위 변경(C) 클릭
<img alt="" src="https://velog.velcdn.com/images/jhp21c/post/5445ca85-ff1f-4042-939f-aada7c2c4228/image.png" /></p>
</li>
</ol>
<ol start="4">
<li><p>선택할 개체 이름 입력 부분에 본인 컴퓨터 이름 입력
<img alt="" src="https://velog.velcdn.com/images/jhp21c/post/aa35f5a7-248a-486b-a8f5-c426b6dc8857/image.png" /></p>
</li>
<li><p>이름 확인 클릭
<img alt="" src="https://velog.velcdn.com/images/jhp21c/post/cea8c2ee-7803-4e11-b3e4-b313270298c5/image.png" /></p>
</li>
<li><p>모든 권한 체크 후 확인
5번 과정에서 확인을 누르면 다음과 같은 창이 뜨는데 모든 권한을 클릭하고 확인을 누른다.</p>
</li>
</ol>
<p>상속됨도 없애야 한다.
<img alt="" src="https://velog.velcdn.com/images/jhp21c/post/de51fefb-d10d-4a96-91f0-7c689dbbf271/image.png" /></p>
<h2 id="참고">참고</h2>
<p><a href="https://kamang-it.tistory.com/entry/%EC%9C%88%EB%8F%84%EC%9A%B0%EC%A6%88%EC%97%90%EC%84%9C-chmod%ED%8C%8C%EC%9D%BC-%EA%B6%8C%ED%95%9C-%EB%B3%80%EA%B2%BD-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0">참고 블로그</a></p>