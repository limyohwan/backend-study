# Spring-Security
## OncePerRequestFilter
- spring security 필터 체인에서 요청당 한번만 실행되는 필터를 구현하는데 사용

## 인증처리 순서
1. 해당 api endpoint에 username과 password를 작성하여 요청함
2. 요청받은 후 서비스 레이어에서 AuthenticationManagerBuilder를 통해 AuthenticationManager 객체를 가져옴
3. username과 password를 이용하여 UsernamePasswordAuthenticationToken을 만들어 AuthenticationManager의 authenticate 메서드에 인자로 넣음
4. 내부적인 동작을 통해 AuthenticationProvider에서 UserDetailsService의 loadByUsername 메서드를 호출하게 되고 loadByUsername에서 실제 DB에 접속하여 해당 username에 해당하는 엔티티를 불러오게됨
5. 해당 엔티티를 불러와 UserDetails의 구현체를 생성하여 리턴하게됨
6. 내부적으로 UserDetails와 UsernamePasswordAuthenticationToken을 비교하여(username, password 검증) 인증에 성고하면 SecurityContextHolder에 Authentication 객체를 set하여 완료하게 되고 아닐시 에러를 던지게 됨
7. 성공적으로 인증이 완료되면 액세스 토큰(json body)과 리프레시 토큰(cookie)을 생성하여 리턴하게됨

## JWT 헤더 검증
- spring security filter chain에 OncePerRequestFilter를 구현한 JwtAuthorizationFilter를 구현하게 됨
- 해당 필터에서 Bearer형식의 JWT가 해당 Authorization Header 값에 들어있는지 유효성 검증을 하게 됨
- 검증 완료시 SecurityContextHolder에 Authentication 객체를 만들어 완료하게되고 검증 실패시 에러를 던지게 됨