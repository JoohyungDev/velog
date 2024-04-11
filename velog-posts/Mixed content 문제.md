<h2 id="문제">문제</h2>
<p>프로젝트 도중, GitHub Pages로 생성된 부트스트랩 웹과 장고 간의 통신을 하는 도중 다음과 같은 경고가 발생했다. </p>
<pre><code class="language-console">Mixed Content: The page at '&lt;URL&gt;' was loaded over HTTPS, 
but requested an insecure element '&lt;URL&gt;'. This request was not upgraded to HTTPS because its URL's host is an IP address.</code></pre>
<h2 id="해결">해결</h2>
<p>HTML 파일 헤더에 다음과 같은 문장을 설정하여 해결하였다.</p>
<pre><code>&lt;meta http-equiv=&quot;Content-Security-Policy&quot; content=&quot;upgrade-insecure-requests&quot;&gt;</code></pre>