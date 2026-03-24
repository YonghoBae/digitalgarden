---
{"dg-publish":true,"permalink":"/02 - Knowledge/Computing/Java/Stream/","tags":["type/study","context/studies","theme/java","status/completed"]}
---

좋은 지적이에요.  
**Obsidian 기준으로 “깨지지 않는 순수 Markdown”**으로 다시 정리해드릴게요.

아래 원칙을 지켰습니다.

- ❌ 이모지, 특수 기호, 인용 블록(`>`)
    
- ❌ 과도한 장식
    
- ❌ HTML, 확장 문법
    
- ✅ Obsidian / GitHub / Notion 공통 호환
    
- ✅ 제목(`#`), 목록(`-`), 코드블록(````java`)만 사용
    

---

# 2. 스트림 / 람다 / Collectors (최종코테 기준)

---

## 2-0. 최종코테에서의 스트림 사용 원칙

최종코테에서 스트림은 **능력 과시용이 아니라 사고를 정리하는 도구**다.

사용 기준:

- 컬렉션을 다른 컬렉션으로 변환할 때
    
- 필터링, 매핑, 집계가 한눈에 보일 때
    
- 한 줄로 의도가 명확할 때
    

피해야 할 기준:

- 스트림 내부에서 상태 변경
    
- 스트림 내부에서 예외 처리
    
- 람다가 길어지는 경우
    
- 디버깅이 어려운 경우
    

읽어서 바로 이해되면 스트림, 아니면 for문이 정답이다.

---

## 2-1. Stream 생성 템플릿

### 컬렉션에서 생성

```java
list.stream();
set.stream();
map.entrySet().stream();
```

### 배열에서 생성

```java
String[] arr = {"a", "b", "c"};
Arrays.stream(arr);
```

### 숫자 스트림

```java
IntStream.range(0, n);        // 0 ~ n-1
IntStream.rangeClosed(1, n);  // 1 ~ n
```

---

## 2-2. 중간 연산 템플릿

### filter

```java
List<String> result =
    names.stream()
         .filter(name -> name.length() >= 3)
         .toList();
```

가독성을 높이려면 boolean 메서드와 조합한다.

```java
.filter(Name::isValid)
```

---

### map

```java
List<Integer> lengths =
    names.stream()
         .map(String::length)
         .toList();
```

---

### flatMap

중첩 컬렉션을 펼칠 때만 사용한다.

```java
List<String> tokens =
    lines.stream()
         .flatMap(line -> Arrays.stream(line.split(",")))
         .map(String::trim)
         .toList();
```

---

### distinct / sorted

```java
List<String> uniqueSorted =
    names.stream()
         .distinct()
         .sorted()
         .toList();
```

---

### peek (디버깅 전용)

```java
list.stream()
    .peek(System.out::println)
    .toList();
```

실제 로직에 사용하지 않는다.

---

## 2-3. 종단 연산 템플릿

### count

```java
long count = list.stream().count();
```

---

### anyMatch / allMatch / noneMatch

```java
boolean anyInvalid =
    list.stream().anyMatch(x -> x < 0);

boolean allPositive =
    list.stream().allMatch(x -> x > 0);
```

---

### max / min

```java
Optional<Integer> max =
    scores.stream().max(Integer::compareTo);

int maxScore = max.orElseThrow();
```

---

### sum

```java
int sum =
    scores.stream()
          .mapToInt(Integer::intValue)
          .sum();
```

---

## 2-4. Collectors 핵심 템플릿

### groupingBy (가장 중요)

값을 리스트로 그룹핑

```java
Map<String, List<Player>> byTeam =
    players.stream()
           .collect(Collectors.groupingBy(Player::team));
```

값의 개수로 그룹핑

```java
Map<String, Long> countByTeam =
    players.stream()
           .collect(Collectors.groupingBy(
               Player::team,
               Collectors.counting()
           ));
```

값의 합계로 그룹핑

```java
Map<String, Integer> sumByTeam =
    players.stream()
           .collect(Collectors.groupingBy(
               Player::team,
               Collectors.summingInt(Player::score)
           ));
```

---

### toMap (중복 키 주의)

```java
Map<String, Integer> map =
    items.stream()
         .collect(Collectors.toMap(
             Item::name,
             Item::price
         ));
```

중복 키가 가능하면 반드시 merge 함수를 제공한다.

```java
.collect(Collectors.toMap(
    Item::name,
    Item::price,
    Integer::sum
));
```

---

### joining (출력용)

```java
String result =
    names.stream()
         .collect(Collectors.joining(", "));
```

---

## 2-5. 일급 컬렉션과 스트림 결합 패턴

스트림은 외부에서 직접 쓰지 말고, 일급 컬렉션 내부로 이동시킨다.

### 나쁜 예

```java
names.stream()
     .filter(...)
     .map(...)
     .toList();
```

---

### 좋은 예

```java
public final class Names {
    private final List<String> values;

    public Names(List<String> values) {
        this.values = List.copyOf(values);
    }

    public Names longerThan(int length) {
        return new Names(
            values.stream()
                  .filter(name -> name.length() >= length)
                  .toList()
        );
    }

    public boolean anyStartsWith(String prefix) {
        return values.stream()
                     .anyMatch(name -> name.startsWith(prefix));
    }
}
```

호출부에서는 도메인 언어처럼 읽히게 된다.

---

## 2-6. 스트림과 for문 전환 기준

다음 상황에서는 for문이 더 낫다.

- 예외를 던져야 할 때
    
- 중간에 break 해야 할 때
    
- 상태를 변경해야 할 때
    

```java
for (String s : list) {
    if (!isValid(s)) {
        throw new IllegalArgumentException();
    }
}
```

최종코테에서는 이 선택이 감점을 줄여준다.

---

## 2-7. 최종코테 스트림 체크리스트

반드시 익숙해져야 하는 것:

- filter / map / flatMap
    
- anyMatch / allMatch
    
- groupingBy
    
- toMap + merge
    
- joining
    
- mapToInt().sum()
    

주의할 것:

- peek 남용
    
- 스트림 내부에서 예외 처리
    
- 의미 없는 람다
    

---

다음 단계로 이어가면 가장 이상적인 흐름은 다음입니다.

- 3. 날짜/시간(java.time) 사용 vs 직접 도메인
        
- 4. 예외 처리와 재입력 구조
        
- 5. 입력/출력 템플릿
        
- 6. 최종코테용 OOP 패턴 묶음
        

원하시면 **3번을 같은 Obsidian 친화 Markdown**으로 바로 이어서 작성하겠습니다.