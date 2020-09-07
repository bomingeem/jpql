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
 
