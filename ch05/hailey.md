# 웹서버
## 1. 아파치 서버 VS NGINX 서버
### 아파치 서버

- 세계에서 가장 많이 사용 하는 웹 서버 중 하나
- 리눅스, 윈도우 등 거의 모든 운영체제에서 사용 가능
- 구축이 쉽고 다양한 기능을 제공
- 쓰레드 프로세스 기반 구조
  - 클라이언트 요청당 하나의 쓰레드가 처리
  - 사용자가 많으면 메모리, CPU 낭비가 심해짐
- Prefork MPM(multi-process-module) 방식
  - MPM : 클라이언트에서 받아들인 요청을 자식 프로세스들에게 분배하는 방식
  - Prefork : 자식 프로세스를 먼저 시작해 두고 클라이언트 요청대해서 각각의 자식 프로세스가 통신을 담당
  - Single CPU, Dual CPU 에서 성능이 좋음
    ![image](https://github.com/hwyi21/202404-http-perfect-guide/assets/58624211/6c2bab8b-03f1-47b5-ae2c-cf11754a5ad5)
### NGINX 서버

- 경량화된 소프트웨어 웹 서버
- 높은 성능과 적은 메모리
  - 비동기 I/O 처리 방식을 사용하여 높은 성능을 제공
  - 대규모 웹사이트에서도 빠른 응답 시간을 보장
  - 적은 메모리 사용량으로도 높은 성능을 제공
  - 서버 운용 비용 절감
- 이벤트 기반(Event-Drvien) 방식으로 동작
  - 1개 또는 고정된 프로세스만 생성 
  - 비동기 방식으로 요청들을 Concurrency(동시성)하게 처리
    ![image](https://github.com/hwyi21/202404-http-perfect-guide/assets/58624211/a7626173-28bb-462a-b552-8ab76cbb8166)
- Master Process와 다수의 Worker Process로 구성되어 실행
  - Master Process : 설정 파일을 읽고 유효성을 검사하며 Worker Process를 관리
  - Worker Process : 모든 요청이 처리 되는 곳
    ![image](https://github.com/hwyi21/202404-http-perfect-guide/assets/58624211/b795a070-a265-4871-8b83-a6dbebe9593c)


** 프로세스
- 단순히 실행 중인 프로그램

** 스레드

- 프로세스 내에서 실제로 작업을 수행하는 작은 작업 단위 
- 모든 프로세스에는 한 개 이상의 스레드가 존재함
- 두 개 이상의 스레드를 가지는 프로세스를 멀티스레드 프로세스라고 함
- 장점 
  - 성능 향상
  - 프로세스의 자원을 공유하고 별도의 메모리 공간 할달이 필요 없기 때문에 자원 공유 효율성 향상
  - 동시성 : 작업을 병렬로 처리 가능
  - 간결성 : 작업을 분리 할 수 있어서 코드가 간결해짐
- 단점
  - 스레드 간의 상호간섭
  - 성능 저하 : 스레드를 많이 생성하면 스레드 스케줄러에서 각각의 스레드들이 병렬로 실행되기 위해 컨텍스트 스위칭(CPU에 실행할 프로세스를 교체하는 기술)이 빈번하게 발생해야 하기 때문에 성능 저하가 발생 
  - 동기화 이슈 : 스레드 간의 실행 순서나 타이밍에 따라 예측 불가능한 문제 발생
  - 자원 소비 : 개별적인 실행 흐름을 가지기 때문에 스레드마다 메모리 자원 소비 
