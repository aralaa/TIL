## SELECT, INSERT, UPDATE, DELETE

### 1) SELECT
```
SELECT 칼럼명 FROM 테이블명

[WHERE 조건]

[ORDER BY 칼럼명 [ASC | DESC]]

[GROUP BY 칼럼명 [HAVING 조건]];
```

### 2) INSERT
```
INSERT INTO 테이블명 (칼럼명1, 칼럼명 2 ...) VALUES (값1, 값2 ...)
```

### 3) UPDATE
```
UPDATE  테이블명 SET 칼럼명1 = 값1, 칼럼명 2 = 값2 WHERE 조건
```
WHERE 조건은 UPDATE문을 사용할 때 필수로 입력해야 한다.

### 4) DELETE
```
DELETE FROM 테이블명 WHERE 조건
```
WHERE 조건을 걸어 테이블에서 삭제될 행을 지정해준다.

## WHERE 절에서 사용 할 수 있는 연산자

연산자 | 설명
--- | --- |
AND | 조건1과 조건2를 둘 다 만족하는 값
OR | 조건1이나 조건2를 만족하는 값
NOT | 조건을 만족하지 않는 값
BETWEEN | 값1과 값2 사이에 존재하는 값
IN | 여러 값을 만족하는 값
LIKE | 해당 문자열 패턴을 만족하는 값
ANY, ALL, SOME, EXIST | 서브쿼리에 대한 연산자

와일드카드 | 설명 | 예시
--- | --- | --- |
% | 여러가지 단어 표시 | 이% => '이'로 시작하는 단어
| | | %이% => '이'가 포함된 단어
_ | 하나의 단어 표시 | 이_수 => 이만수, 이동수
[] | 괄호에 있는 글자를 포함한 단어 | h[oa]t => hat, hot
^ | 괄호에 있지 않은 글자를 포함한 단어 | h[^oa]t => hat, hut이 아닌것
|- | 괄호에 있는 글자를 포함한 단어 | h[a-z]t => hat, hbt ... hzt



## INNER JOIN 과 OUTER JOIN의 차이점

- A와 B를 INNER JOIN 하면 A와 B의 교집합을 얻을 수 있다.
- A와 B를 OUTER JOIN 하면 A와 B의 합집합을 얻을 수 있다.
- 연결한 두 테이블에 중복된 데이터만 출력하고 싶다면 Inner Join 사용
- 연결한 두 테이블의 모든 데이터를 같이 출력 하고싶다면 Outer Join 사용

### A와 B 테이블
A | B
-- | -- |
1 | 3
2 | 4
3 | 5
4 | 6

### INNER JOIN
```
SELECT * FROM a INNER JOIN b ON a.a = b.b;
```
A | B
-- | --
3 | 3
4 | 4

### Left Outer Join

- A 테이블에 있는 모든 행과 B 테이블과 A 테이블에 있는 중복된 값
```
SELECT * FROM a LEFT OUTER JOIN b on a.a = b.b;
```
A | B
-- | --
1 | null
2 | null
3 | 3
4 | 4

### Full Outer Join
- A테이블과 B테이블의 모든 데이터
- A에는 데이터가 있고 B에는 비어있는 경우 B 부분은 null이며, 반대의 경우에는 A 부분이 null이다.
```
SELECT * FROM a FULL OUTER JOIN b on a.a = b.b;
```
A | B
-- | --
1 | null
2 | null
3 | 3
4 | 4
null | 5
null | 6

### 참고  
[심플한 코딩 백과사전](https://webcoding.tistory.com/category/%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4/SQL)  
[INNER JOIN과 OUTER JOIN의 차이](https://jetalog.net/28)  
[INNER JOIN, LEFT JOIN(OUTER JOIN) 비교 이너조인, 아우터조인](https://aljjabaegi.tistory.com/13)  
[JOIN의 종류설명 및 사용법 & 예제](https://coding-factory.tistory.com/87)  
