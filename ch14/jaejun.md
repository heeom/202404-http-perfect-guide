SSL 인증서 체인

SSL 인증서는 **서버 인증서 > 중간 인증서 > 루트 인증서** 순으로 이어지는 연결 고리를 가지고 있는데 이를 인증서 체인이라고 한다. SSL 통신 과정에서 인증서 체인이 정상적으로 확인되어야 최종적으로 문제 없이 암호화 통신을 할 수 있다.

- naver ssl 인증

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/9bf36ad3-0719-431a-b1e2-3f429aeb690d/54e8e4d3-24c2-4859-a275-68c3c975e11e/Untitled.png)

네이버의 경우엔 *.www.naver.com > DigiCert TLS RSA SHA256 2020 CA1 > DigiCert Global Root CA 순으로 인증서 체인이 연결

- .www.naver.com : 서버 인증서
- DigiCert TLS RSA SHA256 2020 CA1 : 중간 인증서(체인 인증서)
- DigiCert Global Root CA : 루트 인증서

### **1. 서버 인증서**

서버 인증서는 특정 도메인에 대한 인증서다. 이 인증서는 서버에 설치되어야 한다.

### **2. 중간 인증서**

중간 인증서는 체인 인증서라고도 부르는데 서버 인증서와 루트 인증서 사이를 이어주는 인증서 역할이다. 서버에 설치되어야 한다.

중간 인증서는 한 개가 될 수도 있고 두 개가 될 수도 있고 여러 개가 될 수 도 있긴한데 일단 이 개념은 큰 틀에서 중요한 건 아니기 때문에 넘어간다.

### **3. 루트 인증서**

루트 인증서는 인증서 체인에서 최상위에 있는 인증서다. 클라이언트에 설치되어야 한다. 그래서 OS나 브라우저에 여러 루트 인증서가 내장되어 있다.

CA에 의해 서명된 공인 인증서를 쓰면 딱히 루트 인증서를 신경쓸 일이 없지만 자체 서명한 사설 인증서를 쓰면 루트 인증서를 클라이언트에 설치해줘야 한다.

---

## **SSL 인증서 인증 방식**

1. **서버 인증서 제공**: 클라이언트가 서버에 연결하면, 서버는 일반적으로 서버 인증서를 클라이언트에게 제공. 서버 인증서에는 서버의 정보와 공개 키가 포함되어 있다.
2. **중간 인증서 제공:** 서버 인증서와 함께, 서버는 중간 인증서도 함께 제공할 수 있다. 중간 인증서는 루트 CA에 의해 서명된 서버 인증서를 발급한 중간 CA의 인증서다.
3. **루트 인증 기관 (Root CA)** : 클라이언트는 서버 인증서를 받은 후, 루트 CA의 공개 키를 포함한 루트 인증서를 클라이언트의 신뢰 목록에서 검색한다. 이는 일반적으로 운영 체제나 브라우저에 내장되어 있는 미리 정의된 신뢰할 수 있는 루트 CA 목록에서 이루어진다.
4. **중간 인증서 검증**: 클라이언트는 중간 인증서도 검증한다 중간 인증서는 루트 CA의 인증서에 의해 서명되어야 하며, 클라이언트는 중간 인증서를 사용하여 서버 인증서의 유효성을 검증한다.
5. **서버 인증서 검증**: 클라이언트는 서버 인증서를 검증한다. 이를 위해 클라이언트는 중간 인증서와 함께 받은 서버 인증서를 사용하여 서버의 공개 키를 확인하고, 이 공개 키가 서버로부터 받은 데이터의 암호화 및 디지털 서명을 확인한다.