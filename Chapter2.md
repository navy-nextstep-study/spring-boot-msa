## 질문
### 근우
- 지연 초기화를 JPA를 제외하고 사용해본적이 있는지? 없다면 이와 비슷한 캐싱을 구현해서 사용해본적이 있는지?

### 이슬
- 지연 초기화 기능을 사용하는 경우가 있을까? - 97p

### 창현
- 스프링 프레임워크와 스프링 부트를 사용했을 때의 경험 또는 본인의 생각
- AutoConfiguration과 Properties의 관계


## 실습문제
여행 플랫폼은 MSA 아키텍처로 설계되어 있으며, 숙소, 항공, 관광지 등 다양한 도메인으로 구성되어 있다. 전시 도메인은 사용자에게 여러 도메인의 데이터를 통합하여 보여주는 역할을 하며, 각 도메인 서버는 HTTP API를 통해 통신한다.

숙소 도메인 서버에 문제가 생겼다고 가정해보자. 이때 전시 도메인은 다음과 같은 문제가 발생할 수 있다.
- 숙소 도메인 서버가 전시 도메인에게 에러 응답을 반환한다. 이 경우, 전시 도메인을 호출하는 클라이언트는 항상 에러 응답을 받게 된다.
- 숙소 도메인 서버가 전시 도메인에게 응답을 주지 않아 타임아웃이 발생한다. 이 경우, 전시 도메인의 모든 쓰레드가 숙소 도메인의 응답을 기다리느라 대기 상태가 되어, 더 이상 새로운 클라이언트의 요청을 처리할 수 없게 된다.

핵심 문제는 숙소 도메인의 장애가 전시 도메인까지 영향을 미친다는 점이다. 숙소 도메인 서버에 문제가 생겼을 때, 전시 도메인의 서비스가 영향을 받지 않도록 설계를 해보자.