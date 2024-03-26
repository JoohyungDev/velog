<h2 id="문제">문제</h2>
<p>포스트 상세 페이지에 댓글 기능을 추가하던 중, 위와 같은 오류가 발생했다. 기본적으로 <code>WSGIRequest</code> 객체에는 <code>author</code> 속성이 없어서 발생한다고 한다. 따라서 <code>author</code> 대신 <code>user</code> 를 사용하여 현재 유저 정보를 접근하면 된다.</p>
<p>추가적으로, <code>WSGIRequest</code>가 궁금하여 찾아보았다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhp21c/post/41998d37-90e2-4b74-b8f2-88cc9f485dfb/image.png" /></p>
<h2 id="wsgi">WSGI</h2>
<p>웹서버와 프레임워크(Django), 앱을 연결해주는 Python의 표준 API라고 한다. 웹서버와 웹앱의 소통을 도와주는 번역기 같은 역할을 한다고 보면 된다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhp21c/post/cc005461-2f77-450f-b24d-2d960b621a0a/image.png" /></p>
<p>Django의 내장 서버는 WSGI 기능을 자체적으로 포함하고 있다. 나중에 다시 배울 내용이기에 이정도만 보도록 하자.</p>
<h2 id="해결">해결</h2>
<pre><code class="language-python"># views.py
def new_comment(request, pk):
    if request.user.is_authenticated:
        post = get_object_or_404(Post, pk=pk)

        if request.method == &quot;POST&quot;:
            comment_form = CommentForm(request.POST)
            if comment_form.is_valid():
                comment = comment_form.save(commit=False)
                comment.post = post
                # comment.author = request.author
                comment.author = request.user
                comment.save()
                return redirect(comment.get_absolute_url())
        else:
            return redirect(post.get_absolute_url())
    else:
        raise PermissionDenied</code></pre>
<p>comment.author = request.author로 되있던 부분을 request.user로 수정하였다.</p>
<h2 id="참고">참고</h2>
<p><img alt="" src="https://velog.velcdn.com/images/jhp21c/post/07f43536-f87a-445b-a84c-e29595e77795/image.png" /></p>
<p><a href="https://tibetsandfox.tistory.com/22">관련 블로그</a></p>