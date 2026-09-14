# 자바 웹 상태 유지 기술 - 쿠키(Cookie) & 세션(Session) 🍪🔒

<!-- workspace-readme-learning:start -->
## 파일과 연결한 학습 안내

아래 설명은 이 폴더의 실제 소스와 빌드 설정을 기준으로 정리했습니다. 기존 소개의 기능 설명은 연결된 파일과 함께 확인할 수 있습니다.

### 주요 파일과 역할

| 파일 | 역할과 읽을 내용 |
| --- | --- |
| [pom.xml](<pom.xml>) | Maven 의존성·플러그인·패키징 설정 |
| [src/main/java/com/example/cookiesession/step1/CookieServlet.java](<src/main/java/com/example/cookiesession/step1/CookieServlet.java>) | HTTP 요청과 응답을 처리하는 Servlet |
| [src/main/java/com/example/cookiesession/step2/SessionServlet.java](<src/main/java/com/example/cookiesession/step2/SessionServlet.java>) | HTTP 요청과 응답을 처리하는 Servlet |
| [src/main/java/com/example/cookiesession/step3/AuthServlet.java](<src/main/java/com/example/cookiesession/step3/AuthServlet.java>) | HTTP 요청과 응답을 처리하는 Servlet |
| [src/main/java/com/example/cookiesession/step3/FreeServlet.java](<src/main/java/com/example/cookiesession/step3/FreeServlet.java>) | HTTP 요청과 응답을 처리하는 Servlet |
| [src/main/java/com/example/cookiesession/step3/PremiumServlet.java](<src/main/java/com/example/cookiesession/step3/PremiumServlet.java>) | HTTP 요청과 응답을 처리하는 Servlet |
| [src/main/java/com/example/cookiesession/step4/FilterServlet.java](<src/main/java/com/example/cookiesession/step4/FilterServlet.java>) | HTTP 요청과 응답을 처리하는 Servlet |
| [src/main/java/com/example/cookiesession/step2/DTO.java](<src/main/java/com/example/cookiesession/step2/DTO.java>) | 입력·응답 데이터의 구조 — `DTO` |
| [src/main/java/com/example/cookiesession/step4/AuthFilter.java](<src/main/java/com/example/cookiesession/step4/AuthFilter.java>) | 요청 전후 공통 처리를 수행하는 필터 — `init`, `doFilter`, `destroy` |
| [src/main/java/com/example/cookiesession/step4/EncodingFilter.java](<src/main/java/com/example/cookiesession/step4/EncodingFilter.java>) | 요청 전후 공통 처리를 수행하는 필터 — `init`, `doFilter`, `destroy` |
| [src/main/java/com/example/cookiesession/step5/LifeCycleListener.java](<src/main/java/com/example/cookiesession/step5/LifeCycleListener.java>) | Java 타입과 동작 정의 — `contextInitialized`, `contextDestroyed` |
| [src/main/java/com/example/cookiesession/step5/RequestListener.java](<src/main/java/com/example/cookiesession/step5/RequestListener.java>) | 입력·응답 데이터의 구조 — `requestInitialized`, `requestDestroyed` |
| [src/main/java/com/example/cookiesession/step5/SessionListener.java](<src/main/java/com/example/cookiesession/step5/SessionListener.java>) | Java 타입과 동작 정의 — `sessionCreated`, `sessionDestroyed` |
| [src/main/webapp/index.jsp](<src/main/webapp/index.jsp>) | 기본 경로와 연결 화면 — 서버 모델을 표시하는 JSP |
| [src/main/webapp/WEB-INF/views/step01/cookie.jsp](<src/main/webapp/WEB-INF/views/step01/cookie.jsp>) | Cookie 화면 — 서버 모델을 표시하는 JSP |
| [src/main/webapp/WEB-INF/views/step02/session.jsp](<src/main/webapp/WEB-INF/views/step02/session.jsp>) | Session 화면 — 서버 모델을 표시하는 JSP |
| [src/main/webapp/WEB-INF/views/step03/auth.jsp](<src/main/webapp/WEB-INF/views/step03/auth.jsp>) | Auth 화면 — 서버 모델을 표시하는 JSP |
| [src/main/webapp/WEB-INF/web.xml](<src/main/webapp/WEB-INF/web.xml>) | 프로젝트 구성 자료 |

### 실행과 설정 확인

- [pom.xml](<pom.xml>)의 의존성과 패키징을 기준으로 구성합니다. 선언된 Java 설정은 17입니다.
- `.\mvnw.cmd package`로 빌드합니다. WAR 결과물은 프로젝트의 Servlet/JSP 규격과 호환되는 컨테이너에 배포해 확인합니다.

### 관련 PDF와 보충 설명

- [7/3 강의](<../260629_ex/새 폴더/7-3/README.md>): 쿠키·세션·필터의 상태 식별과 요청 제어를 연결합니다.

이 링크는 구현을 이해하기 위한 관련 기초 자료입니다. 해당 강의가 이 저장소의 모든 기능이나 이후 버전의 API를 설명한다는 뜻은 아닙니다.

### 읽는 순서와 복습

- 로그인 상태 생성 → 세션 식별 → 공통 필터 → 접근 허용·거부를 추적합니다. 로그인 성공·실패·만료·로그아웃과 권한 없는 접근을 구분합니다.

<!-- workspace-readme-learning:end -->

독립적인 HTTP 요청 사이에서 사용자의 로그인 정보나 장바구니 데이터를 식별·저장하는 웹 핵심 상태 보존 기술인 쿠키와 세션의 서블릿 구현법을 다룹니다.

---

## 📂 학습 파일 구성 (Files)

- [pom.xml](<pom.xml>) : 서블릿 및 톰캣 구동용 Maven 디펜던시 설정
- [step1.md](<step1.md>) ~ [step5.md](<step5.md>) : HTTP 비연결성(Stateless)의 한계, 쿠키의 동작 흐름, 안전한 서버 세션 생성과 소멸, 세션 타임아웃 이론 및 코드 실습 요약서
- [src/main/java/](<src/main/java>) : `HttpServletRequest`로부터 세션을 꺼내어 상태를 등록/검증하는 자바 컨트롤러 소스코드

---

## 🛠 배운 핵심 개념 (What We Learned)

- **HTTP Stateless**: 웹의 근본적인 무상태 프로토콜 특징을 이해하고, 왜 보조 저장소(쿠키, 세션)가 탄생했는지 이해합니다.
- **쿠키 vs 세션**: 클라이언트 브라우저에 텍스트로 보관하는 쿠키와, 서버 안전한 메모리에 객체로 보관하는 세션의 보안적/성능적 차이를 배웁니다.

---

## 🚀 실행 및 확인 방법 (How to Run)

1. 톰캣(Tomcat) 서버를 연동하여 프로젝트를 빌드 및 구동합니다.
2. 로그인/로그아웃 페이지를 브라우저로 띄운 뒤, 크롬 개발자 도구의 **Application -> Storage** 메뉴에서 실제 생성되는 쿠키(JSESSIONID) 정보를 추적합니다.

<!-- pdf-til-supplement:start -->
## TIL 부연 설명 — PDF와 연결하기

기존 실습 내용을 이해하기 위한 PDF 기반 부연 설명이다. 아래 예시는 개념을 설명하기 위한 것이며, 이 프로젝트에서 실행해 관찰한 결과와는 구분한다. 페이지 번호는 표지를 포함한 PDF 순서다.

함께 읽을 파일: [src/main/java/com/example/cookiesession/step1/CookieServlet.java](<src/main/java/com/example/cookiesession/step1/CookieServlet.java>) · [src/main/java/com/example/cookiesession/step2/SessionServlet.java](<src/main/java/com/example/cookiesession/step2/SessionServlet.java>) · [src/main/java/com/example/cookiesession/step3/AuthServlet.java](<src/main/java/com/example/cookiesession/step3/AuthServlet.java>)

### 쿠키는 전달 수단, 세션은 서버 상태

쿠키는 브라우저가 조건에 맞는 요청에 실어 보내는 값이고 세션은 서버가 식별자에 연결해 보관하는 상태다. 세션 방식도 브라우저가 세션 ID를 제시하므로 식별자 보호와 만료 처리가 필요하다. 인증으로 신원을 확인한 후 실제 자원에 대한 인가를 별도로 판단한다.

**예시로 이해하기:** 로그인 → 세션 생성 → 이후 요청의 세션 조회 → 로그아웃 시 무효화 순서로 읽는다. HttpOnly는 자바스크립트의 쿠키 읽기를 제한하지만 브라우저의 자동 전송은 막지 않는다. Secure·SameSite·CSRF 정책은 요청을 보내는 방식과 함께 이해한다.

근거: 233-1 쿠키와 세션 — [4쪽](<../260629_ex/새 폴더/7-2/233-1_쿠키와_세션.pdf#page=4>) · [6쪽](<../260629_ex/새 폴더/7-2/233-1_쿠키와_세션.pdf#page=6>) · [10쪽](<../260629_ex/새 폴더/7-2/233-1_쿠키와_세션.pdf#page=10>) · [12쪽](<../260629_ex/새 폴더/7-2/233-1_쿠키와_세션.pdf#page=12>) · [17쪽](<../260629_ex/새 폴더/7-2/233-1_쿠키와_세션.pdf#page=17>)

### 필터와 리스너의 실행 위치

필터는 요청이 서블릿에 도착하기 전후의 공통 처리를 맡고 chain.doFilter로 다음 단계에 넘긴다. 차단 응답을 보낸 뒤에도 체인을 계속 실행하면 보호하려던 코드가 실행될 수 있다. 리스너는 특정 요청 처리보다 애플리케이션·세션 등의 수명주기 사건에 반응한다.

**예시로 이해하기:** 로그인 확인만 필요한 요청에서 getSession(false)를 쓰면 확인 때문에 새 세션이 생성되는 일을 피한다. 로그인 화면까지 인증 필터가 막으면 리다이렉트가 반복될 수 있으므로 공개 경로와 보호 경로를 구분해서 흐름을 그린다.

근거: 233-2 필터와 리스너 — [4쪽](<../260629_ex/새 폴더/7-3/233-2_필터와_리스너.pdf#page=4>) · [7쪽](<../260629_ex/새 폴더/7-3/233-2_필터와_리스너.pdf#page=7>) · [9쪽](<../260629_ex/새 폴더/7-3/233-2_필터와_리스너.pdf#page=9>) · [12쪽](<../260629_ex/새 폴더/7-3/233-2_필터와_리스너.pdf#page=12>)

<!-- pdf-til-supplement:end -->
