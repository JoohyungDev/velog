<h2 id="문제">문제</h2>
<p><img alt="" src="https://velog.velcdn.com/images/jhp21c/post/e3abad59-2170-4809-8ae6-ff4f3f62c291/image.png" /></p>
<p>위 그림과 같이 삭제 버튼을 누르면 나오는 모달의 확인버튼을 누르면 <code>DeleteView</code>의 기본 세팅으로 인해 <code>post_confirm_delete.html</code>이라는 템플릿으로 한번 더 이동하여 삭제를 해야만 했다.</p>
<p>이 과정에서 불편함을 느꼈고, 모달로 한번에 처리하기 위해 관련 자료를 조사한 끝에 시간을 세이브 하였다.</p>
<h2 id="해결">해결</h2>
<pre><code class="language-python">&lt;form class=&quot;post-form d-inline&quot; action=&quot;{% url 'post_delete' post.pk %}&quot;
method=&quot;post&quot;&gt;
    {% csrf_token %}
    &lt;a href=&quot;{% url 'post_delete' post.pk %}&quot;&gt;
    &lt;input type=&quot;submit&quot; value=&quot;확인&quot; class=&quot;btn btn-primary&quot;&gt;
    &lt;/a&gt;
&lt;/form&gt;</code></pre>
<p>확인 버튼을 <code>form</code> 태그로 감싸고 <code>post_delete</code>라는 이름의 삭제 클래스 URL과 연결시켰다. </p>
<p>그 다음에 views.py에서 삭제 클래스안에 get함수를 오버라이딩 하였다.</p>
<pre><code class="language-python">class PostDelete(DeleteView):
    model = Post
    success_url = reverse_lazy(&quot;post_list&quot;)

    def get(self, request, *args, **kwargs):
        return self.post(request, *args, **kwargs)</code></pre>
<p><code>GET</code> 요청이 들어오면 바로 <code>Post</code> 요청으로 처리하게끔 구현된 코드이다.</p>
<h2 id="참고">참고</h2>
<p><a href="https://duckracoon.tistory.com/entry/Django-DeleteView%EB%A5%BC-Template%EC%97%86%EC%9D%B4-%EC%82%AC%EC%9A%A9%ED%95%98%EB%8A%94-%EB%B2%95">관련 블로그</a>
<a href="https://getbootstrap.kr/docs/5.0/components/modal/">모달</a></p>