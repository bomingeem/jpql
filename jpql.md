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

페이징 API
 · JPA는 페이징을 다음 두 API로 초상화
 · setFirstResult(int startPosition) : 조회 시작 위치(0부터 시작)
 · setMaxResults(int maxResult) : 조회할 데이터 수
    List<Member> result = em.createQuery("select m from Member m order by m.age desc", Member.class)
            .setFirstResult(1)
            .setMaxResults(10)
            .getResultList(); 
            
조인
 · 내부 조인
    String query = "select m from Member m inner join m.team t";
    List<Member> result = em.createQuery(query, Member.class)
            .getResultList(); 
 · 외부 조인
    String query = "select m from Member m left outer join m.team t"; 
 · 세타 조인         
    String query = "select m from Member m, Team t where m.username = t.name";
조인 - ON 절
 · ON절을 활용한 조인(JPA 2.1부터 지원) 
  1. 조인 대상 필터링
    String query = "select m from Member m left join m.team t on t.name = 'teamA'";  
  예) 회원과 팀을 조인하면서, 팀 이름이 A인 팀만 조인
  2. 연관관계 없는 엔티티 외부 조인
    String query = "select m from Member m left join Team t on m.username = t.name";
    
JPA 서브 쿼리 한계
 · JPA는 WHERE, HAVING 절에서만 서브 쿼리 사용 가능
 · SELECT 절도 가능(하이버네이트에서 지원)
 · FROM 절의 서브 쿼리는 현재 JPQL에서 불가능
  - 조인으로 풀 수 있으면 풀어서 해결

JPQL 타입 표현
 · 문자 : 'HELLO'
 · 숫자 : 10L
 · Boolean : TRUE, FALSE
 · ENUM : jpabook.MemberType.Admin (패키지명 포함)
    String query = "select m.username, 'HELLO', TRUE from Member m where m.type = :userType";
    List<Object[]> result = em.createQuery(query)
            .setParameter("userType", MemberType.ADMIN)
            .getResultList();
 · 엔티티 타입 : TYPE(m) = Member (상속 관계에서 사용) 
 
조건식
String query = "select " +
               "case when m.age <= 10 then '학생요금' " +
               "     when m.age >= 60 then '경로요금' " +
               "     else '일반요금' " +
               "end " +
               "from Member m" ;
 · COALESCE : 하나씩 조회해서 null이 아니면 반환              
    String query = "select coalesce(m.username, '이름 없는 회원') from Member m";    
 · NULLIF : 두 값이 같으면 null 반환, 다르면 첫번째 값 반환   
    String query = "select nullif(m.username, '관리자') from Member m";    
                
    

 