<blockquote>
<p>저번 시간에는 실제 면접 당시 들었던 내용을 정리해보았습니다. 이번에는 빠진 내용을 추가적으로 정리해보도록 하겠습니다.</p>
</blockquote>
<h2 id="rest-api-정의">REST API 정의</h2>
<blockquote>
<p><strong>REpresentational State Transfer</strong>의 약자로 웹상에서 사용되는 여러 리소스를 <strong>HTTP URI</strong>로 표현하고, 해당 리소스에 대한 행위를 <strong>HTTP Method</strong>로 정의하는 방식이다.</p>
</blockquote>
<p><strong>RESTful</strong> 규약을 지켜서 개발하는 것이다.</p>
<ul>
<li>대표적으로 self-descriptive, hateos등이 있다.</li>
<li>url에 동사를 사용하지 않고 <strong>명사를 사용</strong>하는 등, 디자인 가이드가 존재한다.</li>
</ul>
<h2 id="http-method">HTTP Method</h2>
<blockquote>
<p>다양한 종류가 있지만 주로 쓰이는 것 위주로 정리하였다. </p>
</blockquote>
<ul>
<li><strong>GET</strong>: 데이터를 조회할 때 사용하며, 데이터가 변경되지 않는다. url에 정보를 담아 서버로 요청을 보내며 ?뒤에 키=값 형태로 구성된다. 여러개인 경우 &amp;를 사용한다.</li>
<li><strong>POST</strong>: 서버의 상태나 데이터를 변경시키 위해 사용하며, 데이터 생성에 주로 사용한다. 요청 본문(<strong>body</strong>)에 데이터를 포함하여 전송한다.</li>
<li><strong>PUT</strong>: 서버에 존재하는 데이터를 새로운 데이터로 전체 교체하는데 사용한다.</li>
<li><strong>DELETE</strong>:  주어진 url의 데이터를 제거하는 데 사용한다. 적절한 권한이 필요하다.</li>
<li><strong>PATCH</strong>: 데이터의 일부만 변경할 때 사용한다.</li>
</ul>
<h2 id="patch의-2가지-방식">Patch의 2가지 방식</h2>
<ul>
<li><strong>JsonPatch</strong></li>
<li><strong>JsonMergePatch</strong>: JsonPatch보다 단순하고 간단하다.</li>
</ul>
<h2 id="http-status-code">HTTP Status Code</h2>
<blockquote>
<p>HTTP 요청에 대한 상태를 나타내는 코드이다.</p>
</blockquote>
<ul>
<li><strong>1xx(정보)</strong>: 요청을 받았으며 프로세스를 계속 진행</li>
<li><strong>2xx(성공)</strong>: 요청을 성공적으로 받았으며 인식했고 수용했음<ul>
<li><strong>200 OK</strong> -&gt; 요청이 성공적으로 실행됨</li>
<li><strong>201 Created</strong> -&gt; 요청이 성공적이며 그 결과 새로우 리소스가 생성됨</li>
</ul>
</li>
<li><strong>3xx(리다이렉션)</strong>: 요청 완료를 위해 추가 작업 조치가 필요함</li>
<li><strong>4xx(클라이언트 오류)</strong>: 요청의 문법이 잘못되었거나 요청을 처리할 수 없음<ul>
<li><strong>400 Bad Request</strong>: 잘못된 문법으로 인해 서버가 요청을 거절함</li>
<li><strong>403 Forbidden</strong>: 클라이언트가 콘텐츠에 접근할 권한이 없음</li>
</ul>
</li>
<li><strong>5xx(서버 오류)</strong>: 서버의 오류 혹은 프록시 서버나 게이트웨이 같은 서버의 보조 구성 요소에 문제가 생겼음</li>
</ul>