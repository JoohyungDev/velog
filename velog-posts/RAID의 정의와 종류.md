<p><img alt="" src="https://velog.velcdn.com/images/jhp21c/post/2b804547-41fa-447e-8499-5ac3afce7c1e/image.png" /></p>
<h2 id="raid-redundant-array-of-independent-disks">RAID (Redundant Array of Independent Disks)</h2>
<ul>
<li>하드 디스크와 SSD로 사용하는 기술</li>
<li>데이터의 안전성 혹은 높은 성능을 위해 여러 물리적 보조기억장치를 마치 하나의 논리적 보조기억장치처럼 사용하는 기술</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/jhp21c/post/c0363512-93ac-4c0b-b3cb-ff2c49d1aa29/image.png" /></p>
<h3 id="raid-레벨">RAID 레벨</h3>
<ul>
<li>RAID를 구성하는 기술</li>
<li>RAID 0, RAID 1, RADE 2, RAID 3, RAID 4, RAID 5, RAID 6</li>
<li>그로부터 파생된 RAID 10, RAID 50...</li>
</ul>
<h3 id="raid-0">RAID 0</h3>
<ul>
<li>데이터를 단순히 나누어 저장하는 구성 방식
<img alt="" src="https://velog.velcdn.com/images/jhp21c/post/d46fd110-3fcc-40a7-9bd1-2e8dcf46e173/image.png" /></li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/jhp21c/post/3d9f8934-7744-48de-81a5-37cdf10824a0/image.png" /></p>
<ul>
<li><p>스트라입: 마치 줄무늬처럼 분산되어 저장된 데이터</p>
</li>
<li><p>장점: 입출력 속도 향상
<img alt="" src="https://velog.velcdn.com/images/jhp21c/post/2ee9e5dc-3aa6-4090-bc6e-1da4db2687f8/image.png" /></p>
</li>
<li><p>단점: 저장된 정보가 안전하지 않음</p>
</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/jhp21c/post/52fde738-d641-47dc-870a-408b64a9c53a/image.png" /></p>
<h3 id="raid-1">RAID 1</h3>
<ul>
<li>미러링: 복사본을 만드는 방식</li>
<li>데이터를 쓸 때 원본과 복사본 두 군데에 씀 (느린 쓰기 속도)
<img alt="" src="https://velog.velcdn.com/images/jhp21c/post/38b6e59e-3e0b-4f8c-bce3-c7938ab954c9/image.png" /></li>
</ul>
<h3 id="raid-4">RAID 4</h3>
<ul>
<li>완전한 복사본을 만드는 대신 오류를 검출하고 복구하기 위한 정보 저장</li>
<li>패리티를 저장한 장치를 이용해 오류를 검출하고 복구</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/jhp21c/post/a7d53c87-3059-430c-b324-e12896f99b7f/image.png" /></p>
<ul>
<li>단점: 패리티 디스크의 병목</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/jhp21c/post/a36db1fe-2ac1-4174-adc6-eb68d4d9ffd1/image.png" /></p>
<h3 id="raid-5">RAID 5</h3>
<ul>
<li>패리티 정보를 분산하여 저장하는 방식
<img alt="" src="https://velog.velcdn.com/images/jhp21c/post/a394d1f3-130f-428f-acb8-48e264e9fb21/image.png" /></li>
</ul>
<h3 id="raid-6">RAID 6</h3>
<ul>
<li>두 종류의 패리티 사용</li>
<li>RAID 5보다 안전, 쓰기는 5보다 느림
<img alt="" src="https://velog.velcdn.com/images/jhp21c/post/d04912b2-a6cd-486f-aae2-4f92247dc186/image.png" /></li>
</ul>
<h2 id="출처">출처</h2>
<p><a href="https://www.youtube.com/watch?v=kFWP6sFKyp0&amp;t=20672s">강민철님 유튜브</a></p>