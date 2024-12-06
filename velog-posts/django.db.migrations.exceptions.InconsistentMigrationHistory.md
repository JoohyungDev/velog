<h2 id="문제">문제</h2>
<blockquote>
</blockquote>
<p>django.db.migrations.exceptions.InconsistentMigrationHistory: Migration socialaccount.0001_initial is applied before its dependency sites.0001_initial 
on database 'default'.</p>
<p><code>allauth</code>를 세팅하던 중에 <code>settings.py</code>를 수정하며 문제가 발생하였다. 다른 앱들은 정상적으로 추가가 되는데 <code>django.contrib.sites</code> 이놈이 문제였다. </p>
<p><code>allauth</code>를 활용하여 소셜 로그인을 하려면 <code>sites</code>를 <code>admin</code>페이지에서 수정해야 하는데 이게 선언이 왠지 모르겠는데 안되있었다.. </p>
<p>따라서 추가하려고 하니까 너무 늦게 선언을 해서 에러가 발생한다.</p>
<p>구글 리서치 결과, 여러 해결방안이 나왔지만 해당되지는 않았고.. 따라서 <code>migrations</code> 폴더 전체와 <code>db.sqlite3</code>를 날려버리고 다시 재선언하였다.</p>
<p>이 방법은 로컬로 사이드프로젝트 느낌으로 하기에 가능한 것이지 실제로는 이렇게 하면 안될 것 같다. 함부로 따라하지 마세요.</p>
<p>최후의 수단으로 사용하면 좋을 듯 하다.</p>
<h2 id="해결">해결</h2>
<ol>
<li><p>해당 user model을 설정한 app의 migrations 폴더 제거</p>
</li>
<li><p>db.sqlite3 파일 삭제</p>
</li>
<li><p>python manage.py makemigrations &lt;app 이름&gt;</p>
</li>
<li><p>python manage.py migrate &lt;app 이름&gt;</p>
</li>
<li><p>python manage.py createsuperuser</p>
</li>
</ol>
<p>의 순서로 진행하여 해결하였다. 다음부터는 <code>settings.py</code>를 꼼꼼히 살펴봐야 겠다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhp21c/post/49875451-2c07-44f1-9936-56477cebf384/image.png" /></p>
