# 클라이언트 식별과 쿠키
# 쿠키 VS 세션

## 쿠키

**HTTP의 cookie는 서버가 사용자의 웹 브라우저로 전송하는 작은 데이터** 이다.

브라우저는 이 조각을 저장했다가 이후 요청이 있을때 동일한 서버로 다시 전달할 수 있다.

이를 통해 사용자가 로그인 상태를 유지하고 있는지 확인하는데 사용될 수 있으며, 이는 `Statless`한 HTTP 프로토콜의 상태 정보를 기억한다.

만일 쿠키가 없다면, 사용자는 로그인이 필요한 모든 기능에 대해 **매 요청시 마다 로그인**을 다시해야하는 번거로움을 경험할 수 있다.

이를 해결하기 위해 서버에서는 쿠키를 만들어 제공하며, 이를 클라이언트에게 응답과 함께 전달한다. 클라이언트(ex. 브라우저) 는 해당 쿠키를 통해 브라우저에게 요청을 보냄으로 인해서 로그인이 되어있음을 알릴 수 있다.

### 쿠키의 문제

이러한 편리한 쿠키를 사용하지 않는 이유가 제일 중요하다. 실제 대부분의 개발 단계에서 쿠키를 통해 로그인을 관리하는 코드를 본적은 없을 것이다. 보통의 이유는 아래와 같다.

- 브라우저 마다 관리하는 쿠키가 다르기 때문에 브라우저가 바뀐 경우 로그인을 다시 해야 한다.
- 쿠키가 **탈취**될 경우 문제가 생긴다.

쿠키가 탈취될 경우 해당 쿠키를 달취한 사람은 쿠키의 값을 임의로 바꿀 수 있다. 또한 해당 쿠키를 통해 만료되지 않은 유효한 쿠키라면 로그인 시도까지 가능하며, 이러한 쿠키에 중요한 정보를 보관한다면 큰 문제가 발생할 수 있다. 이를 `Cookie Hijacking` 이라고 한다.

![image](https://github.com/Zero-ToHero/202404-http-perfect-guide/assets/71249347/6318f6c7-6fb3-4c13-8333-f83c463665f8)

# 세션

위와 같은 쿠키의 문제로 이를 대신하기 위해 쿠키에 정보를 보관하는 것이 아닌 서버 측에서 이를 저장하고 관리하는 방식의 세션이 탄생한다.

이러한 방식은 실제 클라이언트에 로그인 요청에 대한 응답을 작성할 때, 인증 정보 자체는 서버에 저장하고 사용자의 식별자인  `JSESSIONID(session_id)` 를 쿠키에 담아 전달한다.

이를 통해 클라이언트는 이후 요청마다 해당 `JSESSIONID` 가 담긴 쿠키를 함께 보내고 서버는 해당 ID를 통해 저장해 두었던 세션 정보를 통해 로그인 여부를 파악할 수 있다.

이를 통해 쿠키의 탈취로 인한 문제를 해결할 수 있으며, 또한 사용자의 로그인 상태, 닉네임 등을 세션에 담아두면 사용자가 요청할 때마다 DB를 조회할 필요성이 없어져 효율적이다.

### 세션의 문제점

그럼에도 불구하고 세션에도 몇가지 문제점은 존재하며 아래와 같다.

- 유의미한 데이터가 아님에 쿠키보단 안전하지만, session Id 탈취시 로그인이 가능하다.
- 서버에서 세션 저장소를 사용해야 하기 때문에 요청이 많아질 경우 서버의 부하가 심하다.
- 서버의 부하가 심하다? → **인스턴스 추가 확장(`스케일 아웃`)을 통해 해결 가능하지 않나?**

### 스케일 아웃에 따른 세션의 문제점

대용량 트래픽을 처리하기 위해 여러 인스턴스를 추가하게 되며(스케일 아웃) 요청의 분배를 위해

`Load Balancing` 을 하게 된다.

이러한 Load Balancing이 세션에 미치는 영향이 무엇일지 고민해볼 필요가 있다. 아래와 같은 상황이라면 어떨까

![image](https://github.com/Zero-ToHero/202404-http-perfect-guide/assets/71249347/7c0013e1-f338-46d7-83ea-a077428a1561)

위의 요청 사항에 이어 사용자는 다음 회원정보 조회 요청을 보내게 된다. 이때 발생할 수 있는 부분이 바로 해당 요청이 정확하게 세션정보가 기록된 Server1로 갈지에 대한 의문이다.

다만 Load Balancer는 매 요청마다 자신의 알고리즘을 통해 요청을 분배하기 때문에 Server1로 간다는 확신은 할 수 없다.

![image](https://github.com/Zero-ToHero/202404-http-perfect-guide/assets/71249347/1ddce308-40be-4ffc-b770-2dc871407f09)

위와 같은 상황이 발생하게 된다면, 사용자는 로그인을 처음부터 다시 해야 하는 상황이 발생하며, 로그인을 다시 하더라도 다음 요청이 2번 서버로 전달 된다면 다시 반복해야 하는 상황이 발생한다.

### 해결방법

### Sticky Session

위와 같은 문제를 해결하기 위한 방법으로 Sticky Session이 존재한다.

이는 첫 request에 대한 응답을 준 서버에 대해서 해당 서버로만 요청을 보내는 방법으로, 즉 세션의 요청을 처음 처리한 서버로만 보내는 것을 말한다.

![image](https://github.com/Zero-ToHero/202404-http-perfect-guide/assets/71249347/58ccfe92-3b80-4f35-bba6-06980340a3fa)

![image](https://github.com/Zero-ToHero/202404-http-perfect-guide/assets/71249347/9fe63694-8188-435c-8510-53bec3e6b8b9)

일반 적으로 위와 같은 `Sticky Session` 의 경우 Cookie를 사용하거나 클라이언트의 IP를 활용한다.

- **단점**
  **특정 서버에만 요청의 과부하**가 올 수 있다.
  특정 서버가 만일 장애로 인한 종료시 해당 서버에 기록된 **모든 세션들이 소실**될 수 있다.

### Session Clustering

이번엔 세션을 공유하는 방식으로 `Session Clustering` 이라고 한다. 이는 하나의 서버가 장애로 인해 역할을 수행하지 못하는 상황이 생기더라도 동일한 세션으로 관리하기 때문에 세션에 대한 정보를 유실없이 유지할 수 있다는 특징이 있다.(모든 서버에 세션 저장)

![image](https://github.com/Zero-ToHero/202404-http-perfect-guide/assets/71249347/243d2b69-2468-41f9-ac63-1c5e30305895)

- 단점
  모든 서버에 세션정보를 다 저장해야 하기 때문에 서버 메모리의 비효율이 생긴다.
  세션 정보를 추가할 때 마다 모든 서버에 세션 정보를 추가해야 한다.(많은 네트워크가 발생)
  서버가 하나 추가될 경우기존 WAS에 새로운 서버의 정보를 입력해 클러스터링 해야한다

### Session Server

마지막으로 별도의 세션을 두어 관리하는 방식이다. 보통 간단하게 Redis를 통해 Session Storage로 사용할 수 있으며, 이는 각 서버 혹은 단일 서버에 세션을 저장하는 것이 아니라 외부 서버를 통해(캐시 서버) 세션정보를 관리하는 것을 말한다.

![image](https://github.com/Zero-ToHero/202404-http-perfect-guide/assets/71249347/69390270-b58d-47d5-abe0-91f2b0dfc218)

즉 모든 WAS들이 요청이 들어올 경우 Redis 혹은 별도의 Session Server를 통해 세션정보를 읽어오는 방식이다.

이를 통해 각 서버마다 통신해야 하는 네트워크 통신 부하를 줄일 수도 있으며, 메모리의 사용량에 대한 걱정을 없앨 순 있다. 다만 이러한 경우 Session Server의 장애로 인한 문제또한 고려해 봐야할 대상이다.
