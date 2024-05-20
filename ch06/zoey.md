## 프록시와 웹 게이트 비교   
|  | proxy | gateway |
| --- | --- | --- |
| 위치 | 내부 네트워크에서 클라이언트와 서버 사이에 위치 | 일반적으로 두 개의 다른 네트워크 시스템 사이에 위치 |
| 사용 목적 | 네트워크 내에서 트래픽을 필터링하고 제어, 보안 및 성능 향상을 위해서 사용됨 <br> (캐싱, 로드 밸런싱, 암호화, 웹필터링) | 서로 다른 시스템 간의 통신을 가능하게 하며, 프로토콜 변환, 보안 인증, 라우팅, 모니터링 기능 제공 |
| 프로토콜 변환 | 주로 동일한 프로토콜을 사용하는 클라이언트와 서버 간의 통신을 중개 | 주로 서로 다른 프로토콜을 사용하는 시스템 간의 통신을 중개하고 변환. |
- 프록시는 주로 **네트워크 내에서 트래픽을 관리하고 필터링하는 데 사용**되는 반면, 게이트웨이는 서로 다른 시스템 간의 통신을 중개하고 변환하여 상호 연결성을 제공합니다.

## 포워드 프록시와 리버스 프록시
  - 네트워크 어디에 위치 하는지, 어느 방향으로 데이터를 제공하는 지에 따라서 구분된다.
  - 클라이언트쪽으로 response를 주는게 forward 프록시, 반대편인 서버쪽으로 request를 주는게 reverse 프록시
    1. 포워드 프록시
        - 위치 : 클라이언트 바로 뒤에 놓임
       <img width="411" alt="image" src="https://github.com/heeom/202404-http-perfect-guide/assets/64389364/b38a77b3-17c5-4b11-867d-780e5d17da78">
       https://miro.medium.com/v2/resize:fit:1400/format:webp/1*bsnzGwKKJ5dvUBZlez0mKg.png
        
        - 같은 네트워크 내부의 클라이언트의 요청을 받아서 대신 서버에 접근한 다음 클라이언트에게 응답을 준다.
        
        - forward proxy의 이점
            - 보안 및 암호화
                - 클라이언트 요청은 포워드 프록시 서버를 통과할 때 암호화 된다.
                - 클라이언트의 요청이 외부서버로 직접 전달되는게 아니라 프록시 서버를 통해 전달되므로 클라이언트의 IP가 외부 서버에 노출되지 않는다.
            - 접근 제어 및 필터링
                - 사용자들의 웹 접근을 제어하고 필터링할 수 있음. 특정 웹 사이트에 대한 액세스를 차단하거나 허용할 수 도 있다.
            - 캐싱
                - 프록시 서버는 이전에 요청된 콘텐츠를 캐싱해서 동일한 요청이 들어오면 캐싱된 데이터를 그대로 반환함으로써 서버의 부하를 줄이는 효과가 있다.

    2. 리버스 프록시
        - 위치 : 웹서버/WAS 앞에 놓임
      
            <img width="440" alt="image" src="https://github.com/heeom/202404-http-perfect-guide/assets/64389364/79bb8278-87d8-4750-9a79-0d47dcc1ac52">

           https://miro.medium.com/v2/resize:fit:1400/format:webp/1*iWUm3v18P2219pUvq2uJ7A.jpeg
        
        - 클라이언트는 웹서비스에 접근할 때 웹서버에 요청을 하는 것이 아닌 프록시로 요청을 하게 되고, 프록시가 웹서버에 요청을 해서 리소스에 접근하는 방식이다.
            - 이런 접근 방식을 사용하는 이유?
                - 보안 때문이다. 네트워크 환경에서는 DMZ가 존재하는데 웹서버를 DMZ에 놓고 사용할 수 있지만 보통 웹서버는 DB에 연결되어 있으므로 웹서버에 대한 공격이 DB까지 이어질 수 있다.
                - 그래서 통상적으로 리버스 프록시를 DMZ에 두고 실제 서비스를 하는 서버는 내부망에 위치시킨 후 서비스를 제공한다.
                - DMZ : 내부, 외부 네트워크 사이에 위치하는 구간(내부, 외부 네트워크에 둘다 접근할 수 있다), 이 구간은 보통 메일서버, 웹서버, FTP 서버 등 외부 서비스를 제공하는 서버가 위치하게 된다.
        - WEB(nginx) - WAS(tomcat) 형태에서
            - WEB(nginx)이 reverse proxy가 된다
        - Reverse proxy의 이점
            - 로드 밸런싱
                - 리버스 프록시는 여러대의 서버에 요청을 분산시켜 보내서 서버의 부하를 분산시킨다.
            - 서버 보안
                - 클라이언트는 리버스 프록시를 경유해서 서버에게 요청을 보내기 때문에 실제 서비스하는 서버의 IP주소를 노출시키지 않을 수 있음.
            - 캐싱
                - 리버스 프록시에 캐싱되어 있는 데이터를 제공해서 서버 부하를 줄인다 (포워드 프록시의 캐싱기능과 같다)
            - 암호화
                - 클라이언트와 통신할때 SSL로 암호화, 복호화를 하는데 리버스 프록시에서 서버 대신 들어오는 요청은 모두 복호화하고 나가는 응답은 모두 암호화 해줌 → 서버의 부하를 줄여준다.