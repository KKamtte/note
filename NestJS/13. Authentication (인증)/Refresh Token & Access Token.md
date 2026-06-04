1- 두 토큰 모두 JWT 기반이다.
- Access Token 은 API 요청을 할 때 검증용 토큰으로 사용된다.
- 인증이 필요한 API 를 사용할 때는 꼭 Access Token을 Header 에 넣어서 보내야 한다.
	- 유저 정보 수정, 회사 채용공고 지원 인원 확인 등
- Refresh Token 은 Access Token 을 추가로 발급할 때 사용된다.
- Aceess Token 을 새로고침하는 기능이 있기 때문에 Refresh Token 이라고 부른다.
- Access Token 은 유효기간이 짧고 Refresh Token은 유효기간이 길다.
- 자주 노출되는 Access Token 은 유효기간을 짧게해서 Token 이 탈취되어도 해커가 오래 사용하지 못하도록 방지할 수 있다.
- 상대적으로 노출이 적은 Refresh Token 의 경우 Access Token 을 새로 발급받을 때만 사용되기 때문에 탈취 가능성이 적다.

![[스크린샷 2023-11-14 오후 10.42.01.png]]

![[스크린샷 2023-11-14 오후 10.44.06.png]]

![[스크린샷 2023-11-14 오후 10.44.55.png]]

![[스크린샷 2023-11-14 오후 10.45.36.png]]
