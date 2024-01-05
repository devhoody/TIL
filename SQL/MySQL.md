- [SELECT](#select)


# SELECT

- 조회기능

> SELECT * from user
> 
- user 내 모든 행 출력.

> desc user;
> 
- user 테이블 설정 조회

> select * from user where user_id = 29
select * from user where user_id <> 29
> 
- <> : 29가 아닌 행 조회

> select * from user where alias = ‘노씨’
select created_date date from user where created_date = '2023-10-31 19:58:19’
> 
- DATE, 문자열은 ‘’으로 감싼다.

> select alias, grade_id, access_location from user where (grade_id = 1 or access_location = 3) and (grade_id = 4 or access_location = 4);
> 
- and, or 연산자 순서 : AND > OR

> select * from user order by created_date desc;
select * from user order by created_date;
> 
- order by의 기본형은 오름차순, desc를 적어야 내림차순 조회

> select user_id, alias from user order by user_id limit 3;
select user_id, alias from user order by user_id limit 3 offset 3;
> 
- limit은 출력되는 행의 개수를 제한.limit 3 ⇒ 3개의 행까지 출력
- offset은 행의 일정 수 이후부터 출력. offset 3 ⇒ 3개 행 이후부터 출력
- 실행순서 : offset 이후 limit

- 포함 검색 : LIKE
    - %노%
    - %노
    - 노%
    - %검색 의 경우, 이프케이프 문자(/)을 %앞에 붙인다.: /%