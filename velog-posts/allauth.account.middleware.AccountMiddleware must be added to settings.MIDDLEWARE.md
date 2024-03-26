<h2 id="문제">문제</h2>
<p><code>django-allauth</code>를 활용한 소셜미디어 로그인 기능을 사용하려고 하는데 제목과 같은 문제가 발생하였다. </p>
<h2 id="해결">해결</h2>
<p>한글로 해석해보면 <code>settings.py</code> 안에 <code>MIDDLEWARE</code> 부분에 저 부분을 추가해주기만 하면 해결 되는 단순한 문제였다.</p>
<pre><code class="language-python">MIDDLEWARE = [
    &quot;django.middleware.security.SecurityMiddleware&quot;,
    &quot;django.contrib.sessions.middleware.SessionMiddleware&quot;,
    &quot;django.middleware.common.CommonMiddleware&quot;,
    &quot;django.middleware.csrf.CsrfViewMiddleware&quot;,
    &quot;django.contrib.auth.middleware.AuthenticationMiddleware&quot;,
    &quot;django.contrib.messages.middleware.MessageMiddleware&quot;,
    &quot;django.middleware.clickjacking.XFrameOptionsMiddleware&quot;,
    &quot;allauth.account.middleware.AccountMiddleware&quot;,
]</code></pre>
<h2 id="참고">참고</h2>
<p><a href="https://stackoverflow.com/questions/77012106/django-allauth-modulenotfounderror-no-module-named-allauth-account-middlewar">스택오버플로우</a></p>