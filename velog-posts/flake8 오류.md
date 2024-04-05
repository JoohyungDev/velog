<p><img alt="" src="https://velog.velcdn.com/images/jhp21c/post/d646f9ac-3d13-4650-ab79-fd4061cf5852/image.png" /></p>
<h2 id="문제">문제</h2>
<p>개인 브랜치에 수정된 파일을 푸시하려고 하니 위와 같은 오류가 발생하였다.</p>
<h2 id="해결">해결</h2>
<p>명령 출력 표시를 눌러 로그 파일을 확인한 후 해당 부분을 수정하여 해결하였다. </p>
<pre><code class="language-shell">&gt; git -c user.useConfigOnly=true commit --quiet --allow-empty-message --file -
black................................................(no files to check)Skipped
prettier.............................................(no files to check)Skipped
flake8...................................................................Failed
- hook id: flake8
- exit code: 1

# 아래 부분에서 오류가 났다는 뜻이다
accounts/models.py:1:1: F401 'django.db.models' imported but unused
accounts/views.py:32:5: E301 expected 1 blank line, found 0

</code></pre>