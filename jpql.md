#jpql
JPQL 문법
 · 엔티티와 속성은 대소문자 구분 O
 · JPQL 키워드는 대소문자 구분 X
 · 엔티티 이름 사용, 테이블 이름이 아님
 · 별칭은 필수
TypeQuery, Query
 · TypeQuery : 반환 타입이 명확할 때 사용
 · Query : 반환 타입이 명확하지 않을 때 사용
TypedQuery<Member> query1 = em.createQuery("select m from Member m", Member.class);
TypedQuery<String> query2 = em.createQuery("select m.username from Member m", String.class);
Query query3 = em.createQuery("select m.username, m.age from Member m");

결과 조회 API
 · query.getResultList() : 결과가 하나 이상일 때, 리스트 반환
  - 결과가 없으면 빈 리스트 반환
 · query.getSingleResult() : 결과가 정확히 하나, 단일 객체 반환
  - 결과가 없으면 : NoResultException
  - 둘 이상이면 : NonUniqueResultException
Member result = em.createQuery("select m from Member m where m.username = :username", Member.class)
.setParameter("username", "member1")
.getSingleResult();  
파라미터 바인딩 - 이름 기준, 위치 기준 

프로젝션
 · SELECT 절에 조회할 대상을 지정하는 것
 · 프로젝션 대상 : 엔티티, 임베디드 타입, 스칼라 타입
프로젝션 - 여러 값 조회
 · SELECT m.username, m.age FROM Member m
 · 1. Query 타입으로 조회
    List resultList = em.createQuery("select m.username, m.age from Member m")
    .getResultList();
    Object o = resultList.get(0);
    Object[] result = (Object[]) o; 
 · 2. Object[] 타입으로 조회
    List<Object[]> resultList = em.createQuery("select m.username, m.age from Member m")
    .getResultList();
    Object result = resultList.get(0); 
 · 3. new 명령어로 조회
  - 단순 값을 DTO로 바로 조회
  - 패키지 명을 포함한 전체 클래스 명 입력
  - 순서와 타입이 일치하는 생성자 필요
    List<MemberDTO> result = em.createQuery("select new jpql.MemberDTO(m.username, m.age) from Member m", MemberDTO.class)
    .getResultList();
    MemberDTO memberDTO = result.get(0);
 