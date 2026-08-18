데이터를 깔끔하고 읽기 쉽게 처리할 수 있도록 도와주는 도구들의 모음

### Stream 이란
- Java 8에서 도입된 데이터 처리 파이프라인
- 컬렉션, 배열 등의 요소를 순차/병렬 처리하는 파이프 라인
- 원본 데이터를 변경하지 않음 (불변)
	- names 리스트에 stream 을 써도 names 는 변하지 않음
- 일회용 - 한 번 소비하면 재사용 불가
	- filter 를 거친 데이터에는 나머지 데이터가 남고 다시 원본 데이터를 읽지 못함
```java
List<String> names = List.of("Kim", "Lee", "Park", "Choi");  
  
List<String> result = names.stream()  
        .filter(n -> n.length() > 2)  
        .map(String::toUpperCase)  
        .collect(Collectors.toList());
```

### Stream vs 반복문
for 반복문 (명령형)
```java
List<String> result = new ArrayList<>();  
for (String name : names) {  
    if (name.length() > 2) {  
        result.add(name.toUpperCase());  
    }  
}
```

Stream (선언형)
```java
List<String> result = names.stream()  
        .filter(n -> n.length() > 2)  
        .map(String::toUpperCase)  
        .collect(Collectors.toList());
```

| 구분   | 반복문         | Stream           |
| ------ | -------------- | ---------------- |
| 스타일 | 명령형 (How)   | 선언형 (What)    |
| 가독성 | 로직 파악 필요 | 의도가 명확      |
| 병령화 | 직접 구현      | parallelStream() |

### Stream 파이프라인 구조
```
Source -> filter -> map -> sorted -> collect
```
- Source: 컬렉션, 배열, 파일 등
- 중간 연산: filter, map, sorted 등 (여러개 연결 가능)
- 최종 연산: collect, reduce, forEach 등 (1개, 파이프라인 실행 트리거)
> 중간 연산은 최종 연산이 호출될 때까지 실행되지 않는다 (지연 평가)

### Stream 생성 방법
```java
// 1. 컬렉션에서 생성  
List<Integer> list = List.of(1, 2, 3);  
Stream<Integer> s1 = list.stream();  
  
// 2. 배열에서 생성  
int[] arr = {1, 2, 3};  
IntStream s2 = Arrays.stream(arr);  
  
// 3. Stream.of()  
Stream<String> s3 = Stream.of("a", "b", "c");  
  
// 4. 범위 생성  
IntStream s4 = IntStream.range(1, 10); // 1~9  
IntStream s5 = IntStream.rangeClosed(1, 10); //1~10  
  
// 5. 무한 스트림  
Stream<Integer> s6 = Stream.iterate(0, n -> n + 2);  
Stream<Double> s7 = Stream.generate(Math::random);
```

### 중간 연산 - filter, map
filter: 조건에 맞는 요소만 통과
```java
List<Integer> evens = numbers.stream()  
        .filter(n -> n % 2 == 0)  
        .toList();
```

map: 각 요소를 변환
```java
List<Integer> lengths = names.stream()  
        .map(String::length)  
        .toList();
```

체이닝
```java
List<String> results = employees.stream()  
        .filter(e -> e.getSalary() > 5000)  
        .map(Employee::getName)  
        .toList();
```

### 중간 연산 - flatMap, distinct, sorted
flatMap: 중첩 구조 평탄화
```java
List<List<String>> nested = List.of(  
        List.of("a", "b"), List.of("c", "d")  
);  
List<String> flat = nested.stream()  
        .flatMap(Collection::stream)  
        .toList();  
// [a,b,c,d]
```

distinct: 중복 제거
```java
List<Integer> unique = List.of(1, 2, 2, 3, 3)  
        .stream()  
        .distinct()  
        .toList();  
// [1,2,3]
```

sorted: 정렬
```java
names.stream()  
        .sorted()                                      // 자연 정렬  
        .sorted(Comparator.reverseOrder())             // 역순  
        .sorted(Comparator.comparing(String::length)); // 길이순
```

### 최종 연산 - collect, reduce
collect: 결과를 컬렉션으로 수집
```java
// List로 수집  
List<String> list = stream.collect(Collectors.toList());  
  
// Set으로 수집  
Set<String> set = stream.collect(Collectors.toSet());  
  
// Map으로 수집  
Map<String, Integer> map = employees.stream()  
        .collect(Collectors.toMap(  
                Employee::getName, Employee::getSalary  
        ));  
  
// 그룹핑  
Map<String, List<Employee>> byDept = employees.stream()  
        .collect(Collectors.groupingBy(Employee::getDept));
```

reduce: 요소를 하나로 합침
```java
int sum = numbers.stream()  
        .reduce(0, Integer::sum);  
Optional<Integer> max = numbers.stream()  
        .reduce(Integer::max);
```

### 최종 연산 - forEach, count, anyMatch
forEach: 각 요소에 동작 수행
```java
names.stream().forEach(System.out::println);
```

count: 요소 개수
```java
long count = names.stream()  
        .filter(n -> n.startsWith("K"))  
        .count();
```

매칭 연산
```java
boolean any = numbers.stream()  
        .anyMatch(n -> n > 10); // 하나라도 만족하면 true  
boolean all = numbers.stream()  
        .allMatch(n -> n > 0); // 모두 만족하면 true  
boolean none = numbers.stream()  
        .noneMatch(n -> n < 0); // 모두 불만족하면 true
```

findFirst: 첫 번째 요소 반환
```java
Optional<String> first = names.stream()  
        .filter(n -> n.length() > 3)  
        .findFirst();  
```

findAny: 아무 요소 하나 반환 (병렬에서 유용)
```java
Optional<String> any = names.parallelStream()  
        .filter(n -> n.length() > 3)  
        .findAny();
```

| 메서드    | 순서 보장 | 병렬 성능 |
| --------- | --------- | --------- |
| findFirst | O         | 느림      |
| findAny   | X        | 빠름      |

### 지연 평가
Lazy Evaluation
```
Stream 생성 -> filter -> map -> sorted -> collect -(트리거)> 모든 연산 실행
			 중간 연산1 중간 연산2 중간 연산3  최종 연산
				|		 |		 |
			 (지연평가) (지연평가) (지연평가)
			    |        |       |
			     ----------------
			           실행 안됨
```
- 최종 연산이 호출되어야 중간 연산 실행, 즉 실행 계획만 세워둠
- 필요한 만큼만 처리 (Short-circuit)
- 성능 최적화에 유리

```java
List<String> result = names.stream()  
        .filter(n -> {  
            System.out.println("filter: " + n);  
            return n.length() > 2;  
        })  
        .map(n -> {  
            System.out.println("map: " + n);  
            return n.toUpperCase();  
        })  
        .findFirst()  
        .orElse("");  
// filter: Kim -> map: Kim -> 결과 Kim  
// "Lee", "Park" 은 처리하지 않음
```
- findFirst() 가 결과를 찾으면 나머지 요소는 처리하지 않음
- 대용량 데이터에서 불필요한 연산을 줄여 성능 향상

### Optional 과 Stream
Optional - null 안전한 값 래퍼
```java
Optional<String> opt = names.stream()  
        .filter(n -> n.startsWith("Z"))  
        .findFirst();  
// 값 추출  
String name = opt.orElse("None");  
String name2 = opt.orElseGet(() -> getDefault());  
opt.ifPresent(System.out::println);
```
- orElse: 기본값 직접 지정
- orElseGet: 기본값을 람다로 지연 생성
- ifPresent: 값이 있을 때만 동작 수행

Optional 을 Stream 으로
```java
opt.stream(); // 값이 있으면 1개짜리 Stream, 없으면 빈 Stream
// Optional<String> phone;
// public Optional<String> getPhone() {  
//    return phone;  
// }
  
// flatMap으로 Optional 처리
List<String> phones = employees.stream()  
        .map(Employee::getPhone) // Stream<Optional<String>>  
        .flatMap(Optional::stream) // Stream<String> (빈 값 제거)  
        .toList();
```
- Optional.stream() 으로 빈 값을 자연스럽게 필터링
- null 체크 없이 안전한 데이터 처리 가능
employees 가 3명이고 phone 이 각각 "123", 없음, "456" 이라면:

| 단계 | 값 |
| --- | --- |
| `.map(Employee::getPhone)` | `Stream<Optional<String>>` → `[Optional["123"], Optional.empty, Optional["456"]]` |
| 각 원소에 `Optional::stream` 적용 | `["123"]`, `[]`, `["456"]` ← 스트림 3개 |
| `flatMap` 이 평탄화 | `Stream<String>` → `["123", "456"]` |


### 병렬 Stream
```java
// 병렬 스트림 생성  
List<Integer> result = numbers.parallelStream()  
        .filter(n -> n > 0)  
        .map(n -> n * 2)  
        .toList();  
  
// 기존 스트림을 병렬로 전환  
numbers.stream().parallel()  
        .forEach(System.out::println);
```

주의사항
| 항목          | 설명                             |
| ------------- | -------------------------------- |
| 적합한 경우   | 대용량 데이터, CPU 집약 연산     |
| 부적합한 경우 | 소량 데이터, I/O 작업, 순서 중요 |
| 공유 상태     | 사용 금지 (동시성 문제)          |
| ForJoinPool   | 공용 풀 사용 - 블로킹 주의       |
> 반드시 성능 측정 후 사용 여부 결정

### 예제
DTO 변환 - DB 에서 가져온 데이터를 유저 DTO 로 변환할 때 등
```java
List<UserDto> dtos = users.stream()
	.map(u -> new UserDto(u.getName(), u.getEmail()))
	.toList()
```

조건별 그룹핑 + 통계 - 카테고리별 주문값, 합계, 통계 값 등을 한번에 뽑을 수 있음
```java
Map<String, DoubleSummaryStatistics> stats = 
	orders.stream().collect(
		Collectors.groupingBy(
			Order::getCategory,
			Collectors.summarizingDouble(Order::getAmount)
		));
```

CSV 한줄로 합치기
```java
String csv = names.stream()
	.collect(Collectors.joining("," ));
```

null 안전 필터링
```java
List<String> safe = list.stream()
	.filter(Objects::nonNull)
	.collect(Collectors.toList());
```