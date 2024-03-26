<p>코딩테스트 특강에서 들은 내용을 정리해보도록 하겠습니다.</p>
<h2 id="코딩테스트">코딩테스트</h2>
<h3 id="정의">정의</h3>
<p>자료구조와 알고리즘을 결합한 문제 해결 능력을 보는 테스트이다.</p>
<h3 id="준비-사항">준비 사항</h3>
<ul>
<li>코테를 보는 회사와 회사 유형을 정리하기</li>
<li>사용가능한 라이브러리 확인</li>
<li>언어 선택</li>
<li>코드 스니펫<ul>
<li>감독관이 없는 경우, 스니펫을 활용</li>
</ul>
</li>
<li>유용한 라이브러리 정리</li>
<li>예외 처리</li>
<li>속도 개선<ul>
<li>클래스, 슬라이싱, for문 대신 리스트 컴프리헨션 등</li>
</ul>
</li>
<li>ChatGPT, Copilot 활용(가능하다면)</li>
</ul>
<h3 id="문제-유형과-출제-방식">문제 유형과 출제 방식</h3>
<p>시간과 문제 수는 다양하며 전부 다 맞춰야 통과하지는 않는다.</p>
<ul>
<li>주로 출제되는 알고리즘<ul>
<li>요구사항 구현(33%)</li>
<li>그리디(20%)</li>
<li>너비우선탐색, 깊이우선탐색(20%)</li>
<li>정렬(8%)</li>
<li>다이나믹 프로그래밍, 최단 경로, 이진 탐색 등</li>
</ul>
</li>
</ul>
<h3 id="워밍업-문제">워밍업 문제</h3>
<ul>
<li>카운팅<pre><code class="language-python"># 1부터 10,000까지 8이라는 숫자가 총 몇번 나오는가?
# 8이 포함되어 있는 숫자의 갯수를 카운팅 하는 것이 아니라 8이라는 숫자를 모두 카운팅 해야 한다. (※ 예를들어 8808은 3, 8888은 4로 카운팅 해야 함)
</code></pre>
</li>
</ul>
<p>str(list(range(1, 10001))).count('8')</p>
<pre><code>리스트를 문자로 바꿀 수 있으므로 문자로 바꿔 문자열 메서드인 `count`를 사용한다.
- 최대, 최소 기본
```shell
# 최대
l = [10, 20, 11, 15, 17, 21, 13]
result = -float('inf')
for i in l:
    if i &gt; result: # 최소는 연산자 방향이 반대
        result = i

result</code></pre><ul>
<li>최대, 최소 응용<pre><code class="language-python">1차원의 점들이 주어졌을 때, 그 중 가장 거리가 짧은 것의 쌍을 출력하는 함수를 작성하시오. (단 점들의 배열은 모두 정렬되어있다고 가정한다.)
예를들어 S = [1, 3, 4, 8, 13, 17, 20] 이 주어졌다면, 결과값은 (3, 4)가 될 것이다.
</code></pre>
</li>
</ul>
<p>list(zip(s, s[1:]))
sorted(zip(s, s[1:]), key=lambda x: x[1] - x[0])
sorted(zip(s, s[1:]), key=lambda x: x[1] - x[0])[0]</p>
<pre><code>`max`와 `min`을 사용할 수도 있지만 그러한 함수 없이 구해야할 경우도 있다. 사용하지 않고 구현하였다.

`zip` 함수를 사용한 정렬을 사용한다면 파이썬답게 쉽게 구현가능하다.

가장 거리가 짧은 쌍 및 긴 쌍을 구하는 것도 간단하다. 가장 뒤의 원소를 구하면 된다.


### 짤막 지식
```python
-10 // 3 # -4</code></pre><p><code>//</code> 는 내림이다.</p>
<pre><code class="language-python">-float('inf') &lt; -100000000000000000000000 # True
float('-inf') &lt; -100000000000000000000000 # True
float('inf') &gt; 100000000000000000000000 # True</code></pre>
<p>파이썬 <code>inf</code> 무한대는 수학의 무한대가 아니다.</p>
<h3 id="스택과-큐">스택과 큐</h3>
<p>수강생 중 스택을 <code>프링글스 통</code>과  큐를 <code>자동세차장</code>이라고 한 분이 있었는데 꽤 괜찮은 표현인 것 같다.</p>
<p><code>깊스너큐</code> 라는 말이 있다. 깊이 우선 탐색은 <code>스택</code>으로 풀고, 너비 우선 탐색은 <code>큐</code>로 푼다는 뜻이다.</p>
<p><code>스택</code>은 일반적으로 다음과 같은 리스트를 생각하면 된다.</p>
<pre><code class="language-python"># 스택
s = []
# 값 삽입
s.append(10)
s.append(20)
s.append(30)
# 값 꺼냄
s.pop()</code></pre>
<p>뒤에서 작업이 이루어진다.</p>
<p><code>큐</code>는 일반적으로 값을 뒤에 추가했으면 앞에서 꺼내고, 앞에 추가했으면 뒤에서 꺼내는 특징이 있다.</p>
<pre><code class="language-python"># 보통의 queue
# s = &lt;- [] &lt;-
s = []
# 값 삽입
s.append(10)
s.append(20)
s.append(30)
# 값 꺼냄
s.pop(0)


# 내맘대로 queue
# s = -&gt; [] -&gt;
s = []
# 값 삽입
s.insert(0, 10)
s.insert(0, 20)
s.insert(0, 30)
# 값 꺼냄
s.pop()</code></pre>
<h3 id="스택-큐-문제">스택 큐 문제</h3>
<p><img alt="" src="https://velog.velcdn.com/images/jhp21c/post/b573b6f5-af45-4422-b801-2a5cb0876667/image.png" /></p>
<p>[1, 2, 3, 4, 1] 으로 원소가 배치되어 있을때 카운트가 하나 증가하는 문제이다. </p>
<pre><code class="language-python">data = [1, 1, 1, 2, 3, 4, 1, 2, 3, 4, 1]
stack = []
count = 0
for i in data:
    stack.append(i)
    if stack[-5:] == [1, 2, 3, 4 ,1]:
        for _ in range(5):
            stack.pop()
        count+= 1
print(count)

stack == [1]
stack == [1, 1]
stack == [1, 1, 1]
stack == [1, 1, 1, 2]
stack == [1, 1, 1, 2, 3]
stack == [1, 1, 1, 2, 3, 4]
stack == [1, 1, 1, 2, 3, 4, 1] # 여기서 마지막 5개가 [1, 2, 3, 4, 1]이 매칭이 됩니다!
    # =&gt; [1, 1]
stack == [1, 1, 2]
stack == [1, 1, 2, 3]
stack == [1, 1, 2, 3, 4]
stack == [1, 1, 2, 3, 4, 1] # 여기서 마지막 5개가 [1, 2, 3, 4, 1]이 매칭이 됩니다!
    # =&gt; [1]</code></pre>
<ul>
<li>풀이 순서</li>
</ul>
<ol>
<li>데이터, 스택(리스트), 카운트 변수를 정의한다.</li>
<li>반복문을 돌면서 스택에 원소를 추가한다.</li>
<li>스택의 마지막 5개의 원소가 [1, 2, 3, 4, 1]과 같으면 카운트를 증가시키고 그 5개의 원소를 지운다.</li>
<li>반복문이 종료될 때까지 반복하며 카운트를 반환한다.</li>
</ol>
<h3 id="연결리스트">연결리스트</h3>
<p>메모리 효율을 위해 사용된다. 리스트의 원소들이 순서대로 쫙 있는 것이 아닌, 떨어져 있으며 <code>next</code>로 이어져 있다.
<img alt="" src="https://velog.velcdn.com/images/jhp21c/post/e6cd3a2c-5a70-4f77-8ed6-f03ffb6c1398/image.png" /></p>
<ul>
<li>간단한 연결리스트 구성<pre><code class="language-python">연결리스트 = {
  'head' : {
      'value': 22,
      'next': {
          'value' : 2,
          'next' : {
              'value' : 90,
              'next' : {
                  'value' : 77,
                  'next' : None
              }
          }
      }
  }
}</code></pre>
</li>
</ul>
<p><code>head</code>는 처음 리스트 조각(부분?)을 말하고 <code>value</code>는 그 부분에 저장된 값, <code>next</code>는 연결될 다음 리스트를 의미한다.</p>
<pre><code class="language-shell">연결리스트['head']['next']['next'] # {'value': 90, 'next': {'value': 77, 'next': None}}</code></pre>
<ul>
<li><p>문제</p>
<ul>
<li>head -&gt; [90, next] -&gt; [2, next] -&gt; [77, next] -&gt; [35, next] -&gt; [21, next] -&gt; None</li>
<li>35를 출력해주세요.</li>
</ul>
<pre><code class="language-python">연결리스트 = {
          'head': {
              'value': 90,
              'next': {
                  'value': 2,
                  'next': {
                      'value': 77,
                      'next': {
                          'value': 35,
                          'next': {
                              'value': 21,
                              'next': None
                          }
                      }
                  }
              }
          }
      }

# 출력문
연결리스트['head']['next']['next']['next']['value'] # 35</code></pre>
</li>
<li><p>예시 1</p>
<pre><code class="language-python">node1 = {
  'value': 12,
  'next': None
}
</code></pre>
</li>
</ul>
<p>node2 = {
    'value': 99,
    'next': None
}</p>
<p>node3 = {
    'value': 37,
    'next': None
}</p>
<p>node1['next'] = node2
node2['next'] = node3</p>
<p>node1['next']['next']['value']</p>
<pre><code>
보통 `next`로 계속 이어지는 것이 아닌 위처럼 나눠서 선언한 후 연결하는 방식으로 사용한다.

- 예시 2
```python
class Node:
    def __init__(self, data):
        self.value = data
        self.next = None

node1 = Node(90)
node2 = Node(2)
node3 = Node(77)
node4 = Node(35)

node1.next = node2
node2.next = node3
node3.next = node4

node1.next.next.value</code></pre><p>조금 더 효율적인 연산을 위해 클래스를 사용해서 구현하였다. 특정 원소 1개만 출력하는 예시이다.</p>
<ul>
<li>예시 3<pre><code class="language-python">class Node:
  def __init__(self, data):
      self.value = data
      self.next = None
</code></pre>
</li>
</ul>
<p>node1 = Node(90)
node2 = Node(2)
node3 = Node(77)
node4 = Node(35)</p>
<p>node1.next = node2
node2.next = node3
node3.next = node4</p>
<p>currentnode = node1
while currentnode:
    print(currentnode.value)
    currentnode = currentnode.next</p>
<pre><code>
연결리스트의 모든 원소를 `while`문으로 전부 출력하는 예시이다. 연결리스트 문제는 위와 같은 구성을 베이스로 잡고 진행한다.



















## 참고
[연결리스트](https://en.wikipedia.org/wiki/Linked_list)
[문제](https://pyalgo.co.kr/?page=6#)</code></pre>