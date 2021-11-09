JWT, JSESSION

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/edd65822-fce7-4b94-a956-830451e180ad/Untitled.png)

⇒ Spring Security의 구조 사진 중 가장 유명한 사진

## Spring Security란?

- spring을 공부하다 보면 회원가입, 로그인을 구현하게 되는데 그 때마다 꼭 등장하는 spring의 주요 기능 중 하나
- 보안에 중점을 둬서, 인증 인가 등 다양한 옵션을 제공하는 프레임워크
- filter 기반으로 동작해서, MVC와는 따로 동작
- 설정파일은 java로도 xml로도 가능

### 인증과 인가

(이해를 위해서 필요한 보안 개념들)

- 인증: 본인이 맞는가
- 인가: 인증된 사용자가 접근할 수 있는가

## 과정

1. 사용자가 form을 통해 로그인 정보가 담긴 Request를 보냄.
2. `AuthenticationFilter`가 `HttpServletRequest`에서 아이디와 패스워드를 인터셉트한다. `HttpServletRequest`에서 꺼내온 사용자 아이디와 패스워드를 진짜 인증을 담당할 `AuthenticationManager` 인터페이스(구현체 - ProviderManager)에게 인증용 객체(UsernamePasswordAuthenticationToken)로 만들어줘서 위임한다.
3. `AuthenticationManager`는 실제 인증을 할 AuthenticationProvider에게 Authentication객체(UsernamePasswordAuthenticationToken)을 다시 전달한다.
4. 인증 절차가 시작되면 AuthenticationProvider 인터페이스가 실행 -> DB에 있는 이용자의 정보와 화면에서 입력한 로그인 정보를 비교
5. AuthenticationProvider 인터페이스에서는 authenticate() 메소드를 오버라이딩 하게 되는데 이 메소드의 파라미터인 Authentication 으로 화면에서 입력한 로그인 정보를 가져올 수 있다.
6. AuthenticationProvider 인터페이스에서 DB에 있는 이용자의 정보를 가져오려면, UserDetailsService 인터페이스를 사용
7. UserDetailsService 인터페이스는 화면에서 입력한 이용자의 이름(username)을 가지고 loadUserByUsername() 메소드를 호출하여 DB에 있는 이용자의 정보를 UserDetails 형으로 가져온다. 만약 이용자가 존재하지 않으면 예외를 던진다. 이렇게 DB에서 가져온 이용자의 정보와 화면에서 입력한 로그인 정보를 비교하게 되고, 일치하면 Authentication 참조를 리턴하고, 일치 하지 않으면 예외를 던진다.
8. 인증이 완료되면 사용자 정보를 가진 Authentication 객체를 SecurityContextHolder에 담은 이후 AuthenticationSuccessHandle를 실행한다.(실패시 AuthenticationFailureHandler를 실행한다.)

## AuthenticationFilter

- AuthenticationManager를 통한 인증 실행
- 인증 성공 시, 얻은 Authentication 객체를 SecurityContext에 저장 후 AuthenticationSuccessHandler 실행
- 인증 실패 시, AuthenticationFailureHandler 실행

## AuthenticationProvider

- DB 정보와 화면에서 입력한 로그인정보 비교
- Spring Security의 AuthenticationProvider을 구현한 클래스로 security-context에 provider로 등록 후 인증절차를 구현

## UserDetailsService

- DB에서 유저 정보를 가져오는 역할

## UserDetails

- 사용자의 정보를 담는 인터페이스, 직접 상속받아 사용하면된다.
- 다른 데이터들이 들어있어도 오버라이드 메소드만 spring security가 알아서 사용해준다

# 프론트단의 session과 cookie

⇒ 위의 동작 과정 이후에 세션관점에서 보면

- 유저에게 session ID와 함께 응답을 내려줌
- 이후 요청에서는 요청쿠키에서 JSESSIONID를 까서 검증 후 유효하면 Authentication를 쥐어준다.
- 

## 접근 권한

- hasRole(Role) : 해당 Role 을 갖고있는 사용자 허용
- hasAnyRole(Role1, Role2, ...) : 해당 Role 중에 1개이상 갖고있는 사용자 허용
- anonymous : 익명 사용자 허용
- authenticated : 인증된 사용자 허용
- permitAll : 모든 사용자 허용
- denyAll : 모든 사용자 허용

# PasswordEncoder

- NoOpPasswordEncoder : deprecate 되었다. (추천X)
- BCryptPasswordEncoder : bcrypt 해쉬 알고리즘을 이용 (추천)
- StandardPasswordEncoder : sha 해쉬 알고리즘을 이용

# Failure, Success Handler

- 직접 구현해도 ok
- org.springframework.security.web.authentication ⇒ 여기에 simple*구현체들이 이미 존재,
- 상속받아서 사용 가능하다

# Security설정(모르는 것만 $표시)

- auto-config : true 로 할경우 filter는 dafault 값으로 동작한다. false 면 anonymous, x509, http-basic, session-management, expression-handler, custom-filter, port-mappings, request-cache, remember-me 를 정의해줘야한다. $
- use-expressions : 스프링 표현식(spEL)의 사용여부다 $
- csrf : csrf 보안 설정여부
- intercept-url : pattern에 정의된 경로에 대해 access 권한을 지정 (Filter가 감시)
  - 개인적으로 xml설정으로 url 감시하는 것은 swagger, static 리소스 같이 안보이는 것들만 해주고 컨트롤러단에서는 어노테이션을 이용하는 것이 가독성이 더 좋아보인다.
- form-login
  - login-page : login form 페이지 URL
  - username-parameter : form id의 name 속성값
  - password-parameter : form pw의 name 속성값
  - login-processing-url : form action 값 (security를 이용해 인증처리)
  - default-target-url : 로그인 성공 시 이동 URL $
  - authentication-failure-url : 로그인 실패 시 이동 URL $
  - always-use-default-target : true 로 하면 무조건 default-target-url 로 이동한다. false 로 하면 authentication-success-handler 대로 redirect 된다.
  - authentication-success-handler-ref : 로그인 성공 시 프로세스 정의, bean id 입력
    - 만약 최종 로그인일시를 DB에 기록해야한다면 handler로 처리하는게 좋겠다.
    - res.sendRedirect 등으로 처리
  - authentication-failure-handler-ref : 로그인 실패 시 프로세스 정의, bean id 입력
- logout
  - logout-url : 로그아웃 처리할 URL (security가 알아서 만들기 때문에, 이 경로로 요청만 하면된다)
  - logout-success-url : 로그아웃 성공 시 이동 URL
  - success-handler-ref : 로그아웃 성공 시 프로세스 정의, bean id 입력
  - invalidate-session : 로그아웃 시 세션 삭제여부 $
- session-management
  - invalid-session-url : invalid session(세션 타임아웃 등) 일 때 이동 URL $
  - max-sessions : 최대 허용 가능한 세션 수 $
  - expired-url : 중복 로그인 발생시 이동 URL (처음 접속한 세션이 invalidate가 되고 다음 요청시 invalid-session-url로 이동) $
  - error-if-maximum-exceeded : max-sessions을 넘었을때 접속한 세션을 실패처리할지 여부 (expired-url와 얘중에 하나만 쓰면 될듯) $

# JWT란?

JWT(Json Web Token)은 JSON 객체를 통해 안전하게 정보를 전송할 수 있는 웹표준(RFC7519) 입니다. JWT는 '.'을 구분자로 세 부분으로 구분되어 있는 문자열로 이루어져 있습니다. 각각 헤더는 토큰 타입과 해싱 알고리즘을 저장하고, 내용은 실제로 전달할 정보, 서명에는 위변조를 방지하기위한 값이 들어가게 됩니다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c2c15354-857d-4f4d-8905-c2ea58f2dab4/Untitled.png)

- JWT는 JSON 객체를 암호화 하여 만든 문자열 값으로 위, 변조가 어려운 정보이다
- 다른 토큰들과 달리 토큰 자체에 데이터를 가지고 있다는 특징이 있다

## JWT 설정과 동작

- jwt를 쓰게 되면 전통적 세션, 쿠키 방식과는 다르다
- 새로운 토큰을 사용하는 것이기 때문에 토큰을 생성, 인증 정보 조회, 회원 정보 추출 등의 검증하는 코드를 짜야한다
- 이후로는 비슷하다. 토큰이 유효한지 검사하고 userdetails를 통해 데이터를 가져오고

참고: https://velog.io/@hellas4/Security-기본-원리-파악하기-이론편

[빨간색코딩]https://sjh836.tistory.com/165

https://mangkyu.tistory.com/76

https://webfirewood.tistory.com/115