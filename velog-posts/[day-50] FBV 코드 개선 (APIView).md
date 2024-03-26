<p>뷰를 제외한 모든 내용은 이전 글과 동일하다.</p>
<pre><code class="language-python"># blog &gt; views.py

from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import status
from rest_framework.permissions import IsAuthenticated, IsAuthenticatedOrReadOnly
from .models import Post
from .serializers import PostSerializer

class PostList(APIView):
    permission_classes = [IsAuthenticated]

    def get(self, request):
        posts = Post.objects.all()
        serializer = PostSerializer(posts, many=True)
        return Response(serializer.data)

    def post(self, request):
        serializer = PostSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save(author=request.user)
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

post_list = PostList.as_view()


class PostDetail(APIView):
    permission_classes = [IsAuthenticated, IsAuthenticatedOrReadOnly]

    def get_object(self, pk):
        try:
            return Post.objects.get(pk=pk)
        except Post.DoesNotExist:
            raise Http404

    def get(self, request, pk):
        post = self.get_object(pk)
        serializer = PostSerializer(post)
        return Response(serializer.data)

    def put(self, request, pk):
        post = self.get_object(pk)
        if post.author != request.user:
            return Response(status=status.HTTP_403_FORBIDDEN)
        serializer = PostSerializer(post, data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

    def delete(self, request, pk):
        post = self.get_object(pk)
        if post.author != request.user:
            return Response(status=status.HTTP_403_FORBIDDEN)
        post.delete()
        return Response(status=status.HTTP_204_NO_CONTENT)

post_detail = PostDetail.as_view()</code></pre>
<p>CBV를 사용하여 CRUD를 구현하고 있으며, <code>APIView</code>를 상속받아 HTTP 메서드 별로 처리할 로직을 정의하고 있다. 또한, <code>permission_classes</code>를 설정하여 인증된 사용자와 인증되지 않은 사용자의 접근 권한을 제어하고 있다.</p>
<p>이는 FBV의 <code>@permission_classes</code>와 동일한 기능이다. <code>@api_view()</code> 부분은 클래스 내부의 HTTP 메서드의 이름으로 된 함수로 대체되었다. </p>
<pre><code class="language-python">from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import status
from rest_framework.permissions import IsAuthenticatedOrReadOnly
from .models import Notice
from .serializers import NoticeSerializer

class NoticeList(APIView):
    def get(self, request):
        notices = Notice.objects.all()
        serializer = NoticeSerializer(notices, many=True)
        return Response(serializer.data)

    def post(self, request):
        if not request.user.is_authenticated:
            return Response(status=status.HTTP_401_UNAUTHORIZED)
        serializer = NoticeSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save(author=request.user)
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

notice_list = NoticeList.as_view()


class NoticeDetail(APIView):
    permission_classes = [IsAuthenticatedOrReadOnly]

    def get_object(self, pk):
        try:
            return Notice.objects.get(pk=pk)
        except Notice.DoesNotExist:
            raise Http404

    def get(self, request, pk):
        notice = self.get_object(pk)
        serializer = NoticeSerializer(notice)
        return Response(serializer.data)

    def put(self, request, pk):
        notice = self.get_object(pk)
        if not request.user.is_authenticated or notice.author != request.user:
            return Response(status=status.HTTP_403_FORBIDDEN)
        serializer = NoticeSerializer(notice, data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

    def delete(self, request, pk):
        notice = self.get_object(pk)
        if not request.user.is_authenticated or notice.author != request.user:
            return Response(status=status.HTTP_403_FORBIDDEN)
        notice.delete()
        return Response(status=status.HTTP_204_NO_CONTENT)

notice_detail = NoticeDetail.as_view()</code></pre>
<p>blog앱과 유사하게 수정된 모습이다. 중간에 있는 <code>notice_list = NoticeList.as_view()</code> 와 <code>notice_detail = NoticeDetail.as_view()</code>는 FBV와 유사하게 URL을 설정하기 위함이다.</p>