#jpql
경로 표현식
 · .(점)을 찍어 객체 그래프를 탐색하는 것
경로 표현식 용어 정리
 · 상태 필드 : 단순히 값을 저장하기 위한 필드(ex : m.username)
 · 연관 필드 : 연관관계를 위한 필드
  - 단일 값 연관 필드 : @ManyToOne, @OneToOne, 대상이 엔티티(ex : m.team)
  - 컬렉션 값 연관 필드 : @OneToMany, @ManyToMany, 대상이 컬렉션(ex : m.orders)
경로 표현식 특징 
 · 상태 필드 : 경로 탐색의 끝, 탐색 X
 · 단일 값 연관 경로 : 묵시적 내부 조인 발생, 탐색 O
 · 컬렉션 값 연관 경로 : 묵시적 내부 조인 발생, 탐색 X
  - FROM 절에서 명시적 조인을 통해 별칭을 얻으면 별칭을 통해 탐색 가능
  ex) select m from Team t join t.members m
명시적 조인, 묵시적 조인
 · 명시적 조인 : join 키워드 직접 사용
 · 묵시적 조인 : 경로 표현식에 의해 묵시적으로 SQL 조인 발생(내부 조인만 가능)
* 실무 조언 : 가급적 묵시적 조인 대신에 명시적 조인 사용

** 페치 조인(fetch join)
 · SQL 조인 종류 X
 · JPQL에서 성능 최적화를 위해 제공하는 기능
 · 연관된 엔티티나 컬렉션을 SQL 한번에 함께 조회하는 기능
 · join fetch 명령어 사용
엔티티 페치 조인
 · 회원을 조회하면서 연관된 팀도 함께 조회(SQL 한번에)
 ex) select m from Member m join fetch m.team
컬렉션 페치 조인
 · 일대다 관계, 컬렉션 페치 조인
 ex) select t from Team t join fetch t.members
페치 조인과 DISTINCT
 · JPQL의 DISTINCT 2가지 기능 제공
  1. SQL에 DISTINCT를 추가
  2. 애플리케이션에서 엔티티 중복 제거
페치 조인과 일반 조인의 차이
 · 일반 조인 실행 시 연관된 엔티티를 함께 조회하지 않음
 · JPQL은 결과를 반환할 때 연관관계 고려 X
 · 단지 SELECT 절에 지정한 엔티티만 조회할 뿐
 · 페치 조인을 사용할 때만 연관됨 엔티티도 함께 조회(즉시 로딩)
 · 페치 조인은 객체 그래프를 SQL 한번에 조회하는 개념
 
페치 조인의 특징과 한계
 · 페치 조인 대상에는 별칭을 줄 수 없다
 · 둘 이상의 컬렉션은 페치 조인 할 수 없다
 · 컬렉션을 페치 조인하면 페이징 API를 사용할 수 없다
 · 연관된 엔티티들을 SQL 한번으로 조회 - 성능 최적화
 · 엔티티에 직접 적용하는 글로벌 로딩 전략보다 우선함
 · 실무에서 글로벌 로딩 전략은 모두 지연 로딩
 · 최적화가 필요한 곳은 페치 조인 적용
페치 조인 - 정리
 · 모든 것을 페치 조인으로 해결할 수는 없음
 · 페치 조인은 객체 그래프를 유지할 때 사용하면 효과적

엔티티 직접 사용 - 기본 키 값
· JPQL에서 엔티티를 직접 사용하면 SQL에서 해당 엔티티의 기본 키 값을 사용
    String query = "select m from Member m where m = :member";
    Member findMember = em.createQuery(query, Member.class)
            .setParameter("member", member1)
            .getSingleResult();
    String query = "select m from Member m where m.id = :memberId";
    Member findMember = em.createQuery(query, Member.class)
            .setParameter("memberId", member1.getId())
            .getSingleResult();   
엔티티 직접 사용 - 외래 키 값
    String query = "select m from Member m where m.team = :team";
    List<Member> members = em.createQuery(query, Member.class)
            .setParameter("team", teamA)
            .getResultList();
            
Named 쿼리 - 정적 쿼리
@NamedQuery(
        name = "Member.findByUserName",
        query = "select m from Member m where m.username =:username"
)
List<Member> resultList = em.createNamedQuery("Member.findByUserName", Member.class)
        .setParameter("username", "회원1")
        .getResultList();
 · 미리 정의해서 이름을 부여해두고 사용하는 JPQL  
 · 정적 쿼리
 · 어노테이션, XML에 저의
 · 애플리케이션 로딩 시점에 초기화 후 재사용
 · 애플리케이션 로딩 시점에 쿼리를 검증
 
벌크 연산
 · JPA 변경 감지 기능을 실행하려면 너무 많은 SQL 실행
벌크 연산 주의
 · 벌크 연산은 영속성 컨텍스트를 무시하고 데이터베이스에 직접 쿼리
  - 벌크 연산을 먼저 실행
  - 벌크 연산 수행 수 영속성 컨텍스트 초기화
    int resultCount = em.createQuery("update Member m set m.age = 20")
            .executeUpdate();  

                   
 


 
