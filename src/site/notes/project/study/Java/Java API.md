---
dg-publish: true
---


## 1. Java API란?

- JDK에서 기본 제공하는 클래스 라이브러리
- `java.lang`, `java.util`, `java.io`, `java.time` 등의 패키지로 구성
- 이미 검증되고 최적화된 구현체 제공


### 주요 API 카테고리

| **패키지**          | **주요 클래스**                     | **용도**      |
| ---------------- | ------------------------------ | ----------- |
| java.lang        | String, Math, System           | 기본 언어 기능    |
| java.util        | Collections, Arrays, List, Map | 자료구조 및 유틸리티 |
| java.util.stream | Stream, Collectors             | 함수형 프로그래밍   |
| java.time        | LocalDate, LocalDateTime       | 날짜/시간 처리    |
| java.io          | File, BufferedReader           | 입출력         |

---

## 2. 자주 사용하는 String API

### 2.1 문자열 분리 및 결합
```Java
// split() - 문자열 분리
String text = "apple,banana,orange";
String[] fruits = text.split(","); // ["apple", "banana", "orange"]
String[] parts = text.split(",", 2); // ["apple", "banana,orange"] - limit 사용

// join() - 문자열 결합 (Java 8+)
String joined = String.join("-", "2024", "01", "15"); // "2024-01-15"
String fromList = String.join(", ", List.of("A", "B", "C")); // "A, B, C"
```

### 2.2 공백 처리
```Java
String input = "  hello  ";

input.trim();       // "hello" - 양쪽 공백 제거
input.strip();      // "hello" - Unicode 공백도 제거 (Java 11+)
input.stripLeading();  // "hello  " - 앞쪽만
input.stripTrailing(); // "  hello" - 뒤쪽만
```

### 2.3 검사 및 변환
```Java
String str = "Hello123";

// 검사
str.isEmpty();           // false
str.isBlank();          // false (공백만 있어도 true, Java 11+)
str.contains("llo");    // true
str.startsWith("He");   // true
str.endsWith("23");     // true

// 변환
str.toUpperCase();      // "HELLO123"
str.toLowerCase();      // "hello123"
str.replace("l", "L");  // "HeLLo123"
str.substring(0, 5);    // "Hello"
```

---

## 3. 정규식 (Pattern, Matcher)

### 3.1 기본 사용법
```Java
import java.util.regex.Pattern;
import java.util.regex.Matcher;

// Pattern 컴파일 (재사용 가능)
Pattern pattern = Pattern.compile("^[0-9]+$");

// 매칭 확인
pattern.matcher("12345").matches();  // true
pattern.matcher("123a5").matches();  // false
```

### 3.2 자주 쓰는 정규식 패턴

| **패턴**           | **설명**  | **예시**               |
| ---------------- | ------- | -------------------- |
| `^[0-9]+$`       | 숫자만     | "123" (O), "12a" (X) |
| `^[1-9][0-9]*$`  | 양의 정수   | "5" (O), "05" (X)    |
| `^[a-zA-Z]+$`    | 영문자만    | "abc" (O), "ab1" (X) |
| `^\w+@\w+\.\w+$` | 간단한 이메일 | "a@b.com" (O)        |
| `^\s*$`          | 공백 문자만  | " " (O), " a" (X)    |

### 3.3 그룹 추출
```Java
Pattern p = Pattern.compile("(\\d{4})-(\\d{2})-(\\d{2})");
Matcher m = p.matcher("2024-01-15");

if (m.matches()) {
    String year = m.group(1);   // "2024"
    String month = m.group(2);  // "01"
    String day = m.group(3);    // "15"
}
```

---

## 4. Arrays 유틸리티 클래스

`import java.util.Arrays;`


```Java
int[] numbers = {3, 1, 4, 1, 5};

// 정렬
Arrays.sort(numbers); // [1, 1, 3, 4, 5]

// 검색 (정렬된 배열에서)
int index = Arrays.binarySearch(numbers, 3); // 2

// 배열 비교
int[] other = {1, 1, 3, 4, 5};
Arrays.equals(numbers, other); // true

// 배열 → 문자열
Arrays.toString(numbers); // "[1, 1, 3, 4, 5]"

// 배열 → 스트림
Arrays.stream(numbers).sum(); // 14

// 배열 채우기
Arrays.fill(numbers, 0); // [0, 0, 0, 0, 0]
```

---

## 5. Stream API

### 5.1 생성
```Java
// 배열에서
Arrays.stream(new int[]{1, 2, 3});

// 컬렉션에서
List.of(1, 2, 3).stream();

// 직접 생성
Stream.of("a", "b", "c");
IntStream.range(1, 5); // 1, 2, 3, 4
```

### 5.2 중간 연산 (Intermediate Operations)
```Java
List<String> names = List.of("Alice", "Bob", "Charlie", "David");

names.stream()
    .filter(name -> name.length() > 3)  // 길이 3 초과만
    .map(String::toUpperCase)           // 대문자 변환
    .sorted()                           // 정렬
    .distinct()                         // 중복 제거
    .limit(2);                          // 최대 2개
```

### 5.3 최종 연산 (Terminal Operations)
```Java
// 수집
List<String> list = stream.collect(Collectors.toList());
Set<String> set = stream.collect(Collectors.toSet());
String joined = stream.collect(Collectors.joining(", "));

// 집계
long count = stream.count();
Optional<String> first = stream.findFirst();
boolean anyMatch = stream.anyMatch(s -> s.startsWith("A"));
boolean allMatch = stream.allMatch(s -> s.length() > 2);

// 축약
Optional<String> max = stream.max(String::compareTo);
int sum = intStream.sum();
double avg = intStream.average().orElse(0.0);
```

---

## 6. 배열 vs 컬렉션 비교

### 6.1 차이표

| **특징** | **배열 (Array)**   | **컬렉션 (Collection)**  |
| ------ | ---------------- | --------------------- |
| 크기     | **고정** (생성 시 결정) | **가변** (동적 조절)        |
| 타입     | 기본형 + 참조형        | **참조형만** (wrapper 필요) |
| 길이 확인  | `length` (필드)    | `size()` (메서드)        |
| 다차원    | 지원 `int[][]`     | 중첩 필요 `List<List<>>`  |
| 성능     | 약간 빠름            | 약간 느림 (오버헤드)          |

### 6.2 코드 비교

**배열 사용**
```Java
// 선언 및 초기화
int[] arr = new int[5];
String[] names = {"Alice", "Bob"};

// 접근
arr[0] = 10;
int value = arr[0];

// 크기 확인
int length = arr.length;

// 순회
for (int i = 0; i < arr.length; i++) {
    System.out.println(arr[i]);
}

// 문제점
// 1. 크기 변경 불가
// 2. 요소 추가/삭제 불편
// 3. contains, indexOf 같은 메서드 없음
```

**컬렉션 사용**
```Java
// 선언 및 초기화
List<Integer> list = new ArrayList<>();
List<String> names = List.of("Alice", "Bob"); // 불변

// 접근
list.add(10);
int value = list.get(0);

// 크기 확인
int size = list.size();

// 순회
for (String name : names) {
    System.out.println(name);
}
// 또는
names.forEach(System.out::println);

// 장점
// 1. 크기 자동 조절
// 2. 풍부한 API (add, remove, contains, indexOf...)
// 3. Stream API와 자연스럽게 연계
```

### 6.3 언제 무엇을 사용할까?

**배열을 사용하는 경우:**

- 크기가 명확하고 변하지 않을 때
- 다차원 구조가 필요할 때 (예: `int[][] matrix`)
- 기본형 데이터를 대량으로 다룰 때 (성능 중요)
- 가변 인자 메서드의 매개변수 (`int... numbers`)


**컬렉션을 사용하는 경우:**

- 크기가 가변적일 때
- 요소 추가/삭제가 빈번할 때
- API 활용이 필요할 때 (검색, 정렬, 스트림 등)
- 대부분의 일반적인 상황

---

## 7. Collections 유틸리티 클래스

`import java.util.Collections;`

```Java
List<Integer> list = new ArrayList<>(List.of(3, 1, 4, 1, 5));

// 정렬
Collections.sort(list); // [1, 1, 3, 4, 5]
Collections.reverse(list); // [5, 4, 3, 1, 1]

// 검색
int index = Collections.binarySearch(list, 3); // 정렬된 리스트에서
int max = Collections.max(list); // 5
int min = Collections.min(list); // 1

// 섞기
Collections.shuffle(list);

// 불변 컬렉션 (수정 불가)
List<Integer> unmodifiable = Collections.unmodifiableList(list);
// unmodifiable.add(1); // UnsupportedOperationException

// 채우기
Collections.fill(list, 0); // 모든 요소를 0으로

// 빈도
int frequency = Collections.frequency(list, 1); // 1이 몇 개?
```

---

## 8. 컬렉션 프레임워크 전체 구조

### 8.1 주요 인터페이스

```
Collection (인터페이스)
├── List (순서O, 중복O)
│   ├── ArrayList (동적 배열)
│   ├── LinkedList (연결 리스트)
│   └── Vector (동기화, 레거시)
│
├── Set (순서X, 중복X)
│   ├── HashSet (해시 테이블)
│   ├── LinkedHashSet (삽입 순서 유지)
│   └── TreeSet (정렬됨)
│
└── Queue (FIFO)
    ├── LinkedList
    ├── PriorityQueue (우선순위)
    └── ArrayDeque (양방향)

Map (별도 인터페이스, Collection 상속X)
├── HashMap (해시 테이블)
├── LinkedHashMap (삽입 순서 유지)
└── TreeMap (키 정렬)
```

### 8.2 간단한 예시
```Java
// List - 순서 보장, 중복 허용
List<String> list = new ArrayList<>();
list.add("A");
list.add("B");
list.add("A"); // 중복 가능
// [A, B, A]

// Set - 중복 제거
Set<String> set = new HashSet<>();
set.add("A");
set.add("B");
set.add("A"); // 무시됨
// [A, B] (순서 보장 X)

// Map - 키-값 쌍
Map<String, Integer> map = new HashMap<>();
map.put("Alice", 25);
map.put("Bob", 30);
map.get("Alice"); // 25
```

---

## 9. 실전 화용

### 9.1 불변 컬렉션 만들기
```Java
// Java 9+
List<String> immutable = List.of("A", "B", "C");
Set<String> immutableSet = Set.of("A", "B");
Map<String, Integer> immutableMap = Map.of("A", 1, "B", 2);

// 이전 방식
List<String> list = Collections.unmodifiableList(Arrays.asList("A", "B"));
```

### 9.2 리스트 초기화
```Java
// 방법 1: Arrays.asList (고정 크기)
List<String> list1 = Arrays.asList("A", "B", "C");

// 방법 2: new ArrayList + addAll
List<String> list2 = new ArrayList<>();
Collections.addAll(list2, "A", "B", "C");

// 방법 3: List.of (불변, Java 9+)
List<String> list3 = List.of("A", "B", "C");

// 방법 4: Stream
List<String> list4 = Stream.of("A", "B", "C").collect(Collectors.toList());
```

### 9.3 컬렉션 복사
```Java
List<String> original = new ArrayList<>(List.of("A", "B"));

// 얕은 복사 (같은 객체 참조)
List<String> copy1 = new ArrayList<>(original);
List<String> copy2 = original.stream().collect(Collectors.toList());

// 방어적 복사로 반환
public List<String> getItems() {
    return Collections.unmodifiableList(items); // 외부 수정 방지
    // 또는
    return new ArrayList<>(items); // 새 리스트 반환
}
```

