# airbnb-clone-project-back
![Copy of Copy of 에어비엔비 클론](https://github.com/lusate/airbnb-clone-project-back/assets/95400441/64635d58-ba86-4b11-9d96-b577b080ffe8)


# 기능별

구글 Gmail을 통해 이메일 인증 코드를 받기 위한 과정.

# 💎 로그인 처리하는 법

## 🎶 유저에게 `ID` / `Password` 를 받아서, 서버의 데이터베이스와 그냥 비교하는 방법 (선호 X)

로그인 유지가 안 될 뿐더러 정보가 유지되지 않으면, 매번 페이지를 이동할 때마다 로그인을 다시 시작하거나 상품을 선택했는데 구매 페이지에서 선택한 상품의 정보가 없을 수도 있다.

로그인 유지하면서 안전한 방법이 필요하다.

## 🎶 세션과 토큰

사용자의 로그인 정보에 대한 것을 어디다가 유지, 저장을 하느냐에 따라 다르다.

### ✅ 세션 방식 (서버 유지)

서버의 메모리, DB와 같은 서버의 자원들을 사용해서 사용자의 정보를 유지시키는 것.

![Untitled](https://github.com/lusate/airbnb-clone-project-back/assets/95400441/d0d83abe-15ed-4139-82c7-f5075146421c)

토큰보다는 보안이 강하지만 서버의 확장성이 떨어지고 서버의 자(세션을 저장, 유지할 공간) 많이 필요.

세션이 서버에 저장이 되고, 트래픽 분산을 위해서 여러 대의 서버를 사용할 때 만약 사용자가 로그인을 했을 때는 만들어진 세션을 참조해야 하기 때문에 처음 로그인한 그 서버에서만 요청을 보내야 한다는 단점이 있다.

---

### ✅ 토큰 방식 (클라이언트 유지)

사용자가 로그인을 하면 서버에서 발행해주는 토큰을 가지고 브라우저의 저장소에 토큰을 유지시키는 방법.

여기서의 토큰이 JWT.

서버에 저장하지 않아서 서버에 확장성이 있고 로그인 했을 때 해당 서버에만 요청을 보내는 것이 아닌 요청이 들어왔을 때 해당 토큰이 유효한 지에만 체크하면 되기 때문에 어떤 서버로 요청을 보내도 상관이 없다.

---

## 🎶 스프링 부트 JWT와 시큐리티 조합.

쿨라이언트가 ID와 비밀번호로 로그인을 요청하면, 서버는 데이터베이스를 조회하여 해당 사용자를 찾습니다. 사용자가 확인되면, 액세스 토큰(Access Token)과 리프레시 토큰(Refresh Token)을 발급합니다. 클라이언트는 이 액세스 토큰을 사용하여 서버가 허용한 API에 접근할 수 있다.

JWT는 일반적으로 클라이언트와 서버 통신 시 권한 인가를 위해 사용하는 토큰이다. 웹 개발을 할 때 보안성에 취약한 쿠키나 서버의 저장 공간을 차지하는 세션 대신 보다 효과적인 인증 방법 구현을 위해 JWT 토큰 인증 방식을 채택.

JWT는 JSON 형식으로 구성되어 있으며, 클레임(claim)이라는 속성을 사용하여 필요한 정보를 포함하고 서명(signature)을 통해 인증과 데이터 무결성을 보장하며, 주로 인증(Authentication)과 정보 교환을 위해 사용

새로 회원 가입 함. → 불필요한 스팸 메일이 계속 날라올 수 있음.

Google 계정에서 2단계도 설정 필요.

![Untitled (1)](https://github.com/lusate/airbnb-clone-project-back/assets/95400441/c2cac697-2091-4d13-9fa9-78d6e1840d00)


구글에서 2차 인증하고 app password 받아와서 저장.

---

## 💧Secret Key

![Untitled (2)](https://github.com/lusate/airbnb-clone-project-back/assets/95400441/638872ce-c391-4807-9281-d6fd5d9b0341)


1) 사용자가 id와 pw를 압력하고 서버로 로그인을 요청한다.

2) 서버는 secret key를 통해서 서명을 하고 공개 키로 암호화 시킨 Access Token을 발급한다.

3) Access Token을 사용자에게 보낸다.

여기까지가 사용자의 로그인이다.

### 토큰 서명

서버는 Secret key를 사용하여 토큰에 서명을 한다. 이 서명은 토큰이 서버에서 발급되었음을 증명하고, 토큰의 내용이 변경되지 않았음을 보장한다.

토큰의 위/변조 방지를 위한 무결성을 보장한다.  Secret key 없으면 토큰의 유효성을 보장할 수 없다.

### JWT 토큰 VS Bearer 토큰

**JWT** : JSON 형식으로 인코딩된 토큰이다. 토큰 내에 사용자 정보와 권한 정보 등을 포함시켜 전달한다.

서버와 클라이언트 간의 인증 및 권한 부여에 사용된다.

**Bearer** : OAuth 2.0 프로토콜에서 사용되는 토큰. 인증된 사용자를 대신하여 API에 접근할 수 있는 권한을 부여한다.

JWT와 달리, 토큰 내에 사용자 정보를 포함시키지 않는다.

JWT는 사용자 정보를 포함하기 때문에 클라이언트가 서버에 요청을 보낼 때마다 사용자 정보를 함께 전달할 필요가 없어서 효율적.

Bearer 토큰은 사용자 정보를 포함시키지 않기 때문에, API에 접근할 때마다 사용자 정보를 함께 전해야 한다. JWT 보다 불편하지만 보안 측면에서는 더욱 안전.

<br>
<br>

## 필터

request를 받으면 request 객체에서 Bearer 토큰 형태로 인증 정보를 받는데 request로부터 Bearer 토큰에 있는 값을 꺼내와야 한다.

꺼내서 Jwt Provider에서 만들었던 validation에 넘겨서 적절한 토큰인지를 검사.

![Untitled (3)](https://github.com/lusate/airbnb-clone-project-back/assets/95400441/04041ca2-a50d-46de-a9d5-ec240b256b03)


필터 메서드에는 init과 doFilter, destroy가 있다.

(필터 사용하려면 Filter  인터페이스 구현 필요.)

**1)** init()

```java
public default void init(FilterConfig filterConfig) throws ServletException {}
```

필터 객체를 초기화하고 서비스에 추가하기 위한 메서드.

웹 컨테이너가 1회 init()을 호출하여 필터 객체를 초기화하면 이후 요청들은 doFilter()를 통해 처리된다.

**2)** doFilter

```java
  public void doFilter(ServletRequest request, ServletResponse response,            FilterChain chain) throws IOException, ServletException;
```

url-pattern에 맞는 모든 HTTP 요청이 Dispatcher Servlet으로 전달되기 전에 웹 컨테이너에 의해 실행되는 메서드다.

FilterChain의 doFilter로 다음 대상으로 요청을 전할 수 있게 된다.

전, 후에 우리가 필요한 처리 과정을 넣어줌으로써 원하는 처리를 진행할 수 있다.

**3)** destroy

```java
public default void destroy() {}
```

필터 객체를 제거하고 사용하는 자원을 반환하기 위한 메서드.

## 💧 Security

✔️ **SecurityContext**

`SecurityContextHolder.*createEmptyContext*()` 를 통해 새로운 SecurityContext 인스턴스를 생성.

Authentication을 보관하는 역할을 하며, SecurityContext를 통해 Authentication 객체를 꺼내올 수 있다.

`**SecurityContextHolder**`는 Spring Security에서 현재 실행 중인 스레드의 보안 컨텍스트를 관리하는 유틸리티 클래스입니다. `createEmptyContext()` 메서드는 빈 보안 컨텍스트를 생성하여 `securityContext` 변수에 할당합니다. 보안 컨텍스트는 현재 사용자의 보안 정보와 권한을 포함하고 있으며, 인증된 사용자 및 해당 사용자의 권한을 추적하는 데 사용

✔️ **Authentication**

현재 접근하는 주체의 정보와 권한을 담는 인터페이스.

Security Context에 저장되며, SecurityContextHolder를 통해 SecurityContext에 접근하고, SecurityContext를 통해 Authentication에 접근할 수 있다.

✔️ **AbstractAuthenticationToken**
`AbstractAuthenticationToken authenticationToken = new UsernamePasswordAuthenticationToken(userId, null);`

사용자 ID와 비밀번호(여기서는 `null`)를 기반으로 `AbstractAuthenticationToken` 인스턴스를 생성

사용자 인증 정보를 나타냄.

✔️ **UsernamePasswordAuthenticationToken**

UserId가 Principal 역할, Password는 Credential의 역할을 함.

`WebAuthenticationDetailsSource`는 사용자의 웹 인증 세부 정보를 생성하는 클래스이며, `buildDetails(request)` 메서드는 HTTP 요청(request)로부터 사용자의 웹 인증 세부 정보를 생성

`SecurityContextHolder`는 시스템 전반에서 사용자의 보안 컨텍스트를 제어하고 관리하는 역할을 수행하며, `setContext` 메서드를 통해 보안 컨텍스트를 설정함으로써 해당 기능이 수행된다.

`SecurityContextHolder.*setContext*(securityContext);`

인증 과정이 완료된 후, 인증 결과(=`Authentication` 토큰)를 `SecurityContext`에 저장하고, 이를 `SecurityContextHolder`에 설정하는 과정.

**MimeMessage 객체 생성**

`Session` 객체를 인자로 받아 생성된다. 이 `Session` 객체는 메일 서버와의 연결 설정 정보를 담고 있다.

메일의 발신자, 수신자, 제목, 본문 등을 설정할 수 있습니다. 메일의 내용은 텍스트 뿐만 아니라 HTML, 이미지 등 다양한 MIME 타입을 지원.

MimeMessage를 활용하면 다양한 형태의 이메일을 쉽게 생성하고 전송할 수 있다. 

JavaMail API를 사용하는 개발자라면 MimeMessage 클래스를 통해 이메일 관련 기능을 효율적으로 구현 가능.

**MimeMessageHelper**

`MimeMessageHelper` 클래스는 Spring Framework의 일부로, `MimeMessage` 객체를 더 쉽게 다룰 수 있게 해주는 유틸리티 클래스

이메일 메시지에 다양한 부분(예: 텍스트, 이미지, 첨부 파일 등)을 쉽게 추가

`multipart` 인자를 `true`로 설정하면 됩니다. 이를 통해 메일에 텍스트와 함께 이미지나 첨부 파일 등을 추가 가능.

**@NotNull** : Null만 허용하지 않는다.   “”, “ “은 허용

**@NotEmpty** : Null이랑 “” 허용하지 않는다.

**@NotBlank :** Null과 “”, “ “ 모두 허용 X ****

**Basic, Bearer**

Basic에서는 **`Basic`** 인증에서는 **`username:password`의** base64 인코딩 값이 된다

Bearer 에서는 ~~

rm -rf .next/cache
넥스트에서 캐시를 지우는 것

이거는 git 붙이고 --cache는
깃에서 ignore 한테 캐시를 지우라고 명령을 내리는 것
재정의

[https://velog.io/@gkdud583/HttpSecurity-WebSecurity의-차이](https://velog.io/@gkdud583/HttpSecurity-WebSecurity%EC%9D%98-%EC%B0%A8%EC%9D%B4)

## **Security 신규 버전 ex) antMatchers →** `requestMatchers`

https://samori.tistory.com/64

https://hansel.tistory.com/156 → 몇 번 이상 틀렸을 시

<br>

---

<br>

https://mangkyu.tistory.com/76

---

git ignore application.yml 문제

- 상황 :
    
    인텔리제이에서 application.yml으로 mySQL 연결 하는 설정 파일인데
    
    gitIgnore에 *.yml 등록후 다른 브랜치로 넘어갔다 오면 계속 사라졌다.
    
- 해결 :
    
    캐시가 문제인 것으로 캐시 파일을 지우면 해결이 된다.
    
    ```java
    rf -r --cache .
    ```
    
- 결론 :
    
git ignore에 등록하기 전의 상황을 알고있고 ignore에 동록하나 트레킹을 하지 못하여 git에 이전에 저장한 스냅샷에 맞게 지워버리는 현상이 일어났다. 그래서 캐시 삭제를 통해 지금 git ignore에 등록한 파일도 스냅샷에 찍어서 만들어 내어 변경이 안생기도록 했다.
    
