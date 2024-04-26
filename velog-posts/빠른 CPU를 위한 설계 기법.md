<p><img alt="" src="https://velog.velcdn.com/images/jhp21c/post/4017ede5-393b-427b-94b2-8bc33b1bda20/image.png" /></p>
<blockquote>
<p>클럭 신호가 빠르게 반복되면 그만큼 빨라질까요?</p>
</blockquote>
<p>일반적으론 맞습니다만, 필요 이상으로 클럭을 높이면 발열이 심해집니다.</p>
<blockquote>
<p>클럭 속도를 늘리는 방법 말고는 뭐가 있을까요?</p>
</blockquote>
<ul>
<li>코어 수 늘리기(듀얼 코어, 멀티 코어...)</li>
<li>스레드 수 늘리기(멀티 스레드)</li>
</ul>
<h2 id="코어">코어</h2>
<ul>
<li>현대적인 관점에서 CPU라는 용어를 재해석 필요</li>
<li>오늘날 CPU에는 명령어를 실행하는 부품이 여러개 존재</li>
<li>이를 코어라고 부름</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/jhp21c/post/f377aabd-6abe-495d-8903-f42962f938ca/image.png" /></p>
<p><img alt="" src="https://velog.velcdn.com/images/jhp21c/post/43b87c9b-9b09-426b-8bf9-0d0c55a425c4/image.png" /></p>
<blockquote>
<p>코어 수에 비례해서 빨라지나요?</p>
</blockquote>
<p>무조건은 아닙니다. 적절한 역할 분배가 중요합니다.</p>
<h2 id="스레드와-멀티-스레드">스레드와 멀티 스레드</h2>
<p><img alt="" src="https://velog.velcdn.com/images/jhp21c/post/adf4bbdd-90bd-4267-b4d3-87e0eb22e9d4/image.png" /></p>
<h3 id="하드웨어-스레드">하드웨어 스레드</h3>
<ul>
<li>하나의 코어가 동시에 처리하는 명령어 단위
<img alt="" src="https://velog.velcdn.com/images/jhp21c/post/ef86a2d5-f7d2-485d-af6c-4fb7f0646ee6/image.png" /></li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/jhp21c/post/8293b3dc-4870-4162-b8e7-dcfe54a268c8/image.png" /></p>
<ul>
<li>2개의 코어가 있고 각각 2개의 명령어를 실행한다면 2코어 4스레드!!</li>
<li>위 사진을 멀티 스레드 프로세서, 멀티 스레드 CPU 라고 함</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/jhp21c/post/449a8d9a-2a70-4336-9779-f9af805e319d/image.png" /></p>
<ul>
<li><p>하나의 명령어를 실행하기 위해서 꼭 필요한 레지스터들을 하나의 코어가 여러개 갖고 있다면 멀티 스레드 프로세서를 설계할 수 있다.</p>
</li>
<li><p>실제 프로세서의 개수는 아니지만 메모리에 저장된(실행되고 있는) 프로그램이 느끼기에 몇 개의 CPU가 있는지?</p>
<ul>
<li>하드웨어 스레드 개수 만큼 있다 -&gt; 논리 프로세서 라고도 부름</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/jhp21c/post/613a78d1-0771-4e3f-9d48-41b1d5a60876/image.png" /></p>
</li>
<li><p>하드웨어 스레드가 8개라는 의미</p>
</li>
</ul>
<h3 id="소프트웨어-스레드">소프트웨어 스레드</h3>
<ul>
<li>하나의 프로그램에서 독립적으로 실행되는 단위
<img alt="" src="https://velog.velcdn.com/images/jhp21c/post/ca7867a2-12fa-494e-bcab-584fe01ae02e/image.png" /></li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/jhp21c/post/44ff80ac-6190-4aa7-8af1-7fbbd8234d93/image.png" /></p>
<h2 id="참고">참고</h2>
<p><a href="https://www.youtube.com/watch?v=kFWP6sFKyp0&amp;list=LL&amp;index=7&amp;t=11050s">강민철님 유튜브</a></p>