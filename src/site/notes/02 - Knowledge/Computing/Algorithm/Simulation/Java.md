---
{"dg-publish":true,"permalink":"/02 - Knowledge/Computing/Algorithm/Simulation/Java/","tags":["type/study","context/studies","theme/algorithm","status/in-progress"]}
---


## 1. 입출력 (I/O)

### Scanner — 간단한 입력
```java
import java.util.Scanner;

Scanner sc = new Scanner(System.in);
int num = sc.nextInt();
String word = sc.next();
sc.nextLine(); // 버퍼 비우기
String line = sc.nextLine();

while (sc.hasNextInt()) {
    int nextNum = sc.nextInt();
}
sc.close();
```

### BufferedReader — 빠른 입력 (대량 입력 시 필수)
```java
import java.io.*;
import java.util.StringTokenizer;

BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
String line = br.readLine();
StringTokenizer st = new StringTokenizer(line, " ");
int num = Integer.parseInt(st.nextToken());
br.close();
```

### BufferedWriter — 빠른 출력 (출력이 많을 때 TLE 방지)
```java
import java.io.*;

BufferedWriter bw = new BufferedWriter(new OutputStreamWriter(System.out));
bw.write("Hello\n");
bw.flush();
bw.close();

// 또는 StringBuilder로 모아서 한 번에 출력
StringBuilder sb = new StringBuilder();
sb.append("Hello\n");
sb.append("World\n");
System.out.print(sb);
```

---

## 2. 자료구조

### List (ArrayList)
```java
import java.util.*;

List<Integer> list = new ArrayList<>();
list.add(10);
list.add(0, 5);        // 0번 인덱스에 삽입
list.set(0, 1);        // 값 변경
int val = list.get(0);
list.remove(0);                    // 인덱스로 삭제
list.remove(Integer.valueOf(10));  // 값으로 삭제
list.size();
list.isEmpty();
list.contains(10);
list.clear();
```

### Map (HashMap / TreeMap)
```java
// HashMap — O(1), 순서 없음
Map<String, Integer> map = new HashMap<>();
map.put("apple", 1);
map.get("apple");
map.getOrDefault("orange", 0);  // 키 없으면 기본값
map.remove("apple");
map.containsKey("apple");
map.containsValue(3);
map.size();

for (Map.Entry<String, Integer> entry : map.entrySet()) {
    System.out.println(entry.getKey() + ": " + entry.getValue());
}

// TreeMap — O(log N), 키 기준 정렬 + 범위 탐색
TreeMap<Integer, String> treeMap = new TreeMap<>();
treeMap.put(3, "c");
treeMap.floorKey(4);     // 4 이하 최댓값 키
treeMap.ceilingKey(2);   // 2 이상 최솟값 키
treeMap.firstKey();      // 가장 작은 키
treeMap.lastKey();       // 가장 큰 키
treeMap.subMap(1, 5);    // 1 이상 5 미만 키의 부분 Map
```

### Set (HashSet / TreeSet)
```java
// HashSet — O(1), 순서 없음
Set<Integer> set = new HashSet<>();
set.add(10);
set.remove(10);
set.contains(10);

// TreeSet — O(log N), 정렬 + 범위 탐색
TreeSet<Integer> treeSet = new TreeSet<>();
treeSet.add(3);
treeSet.floor(4);    // 4 이하 최댓값
treeSet.ceiling(2);  // 2 이상 최솟값
treeSet.first();
treeSet.last();
treeSet.subSet(1, 5); // 1 이상 5 미만
```

### Queue / Deque / Stack
```java
// Queue (FIFO)
Queue<Integer> q = new LinkedList<>();
q.offer(10);
q.peek();   // 확인
q.poll();   // 꺼내기

// Stack (LIFO) — ArrayDeque 사용 권장
Deque<Integer> stack = new ArrayDeque<>();
stack.push(10);
stack.peek();
stack.pop();

// Deque (양방향)
Deque<Integer> dq = new ArrayDeque<>();
dq.addFirst(10);
dq.addLast(20);
dq.pollFirst();
dq.pollLast();
```

### PriorityQueue (Heap)
```java
// 최소 힙 (기본)
PriorityQueue<Integer> minHeap = new PriorityQueue<>();
minHeap.add(10);
minHeap.poll(); // 가장 작은 값

// 최대 힙
PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());

// 객체 기준 정렬
PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> a[0] - b[0]); // 0번 인덱스 기준
```

---

## 3. 배열 (Arrays)

```java
import java.util.Arrays;

int[] arr = {3, 1, 5, 2, 4};
Arrays.sort(arr);              // 오름차순
Arrays.sort(arr, 0, 3);       // 부분 정렬 (0~2)

// 2D 배열 정렬
int[][] arr2d = {{3, 1}, {1, 2}, {2, 3}};
Arrays.sort(arr2d, (a, b) -> a[0] - b[0]); // 0번 기준 오름차순

// 다중 조건 정렬
Arrays.sort(arr2d, (a, b) -> {
    if (a[0] != b[0]) return a[0] - b[0]; // 첫 번째 기준
    return a[1] - b[1];                    // 두 번째 기준
});

int index = Arrays.binarySearch(arr, 3); // 이진탐색 (정렬 필수)
Arrays.fill(arr, 0);                     // 전체 채우기
int[] copy = Arrays.copyOf(arr, arr.length);
int[] range = Arrays.copyOfRange(arr, 1, 4);

System.out.println(Arrays.toString(arr));       // 1D 출력
System.out.println(Arrays.deepToString(arr2d)); // 2D 출력
```

---

## 4. 문자열 (String / StringBuilder / Character)

### String
```java
String str = "Hello World";
str.length();
str.charAt(0);               // 'H'
str.substring(6);            // "World"
str.substring(0, 5);         // "Hello"
str.split("-");              // 분리
str.contains("World");
str.startsWith("He");
str.endsWith("ld");
str.indexOf("l");            // 첫 번째 위치
str.lastIndexOf("l");
str.replace("l", "p");
str.toLowerCase();
str.toUpperCase();
str.trim();                  // 앞뒤 공백 제거
str.toCharArray();
String.valueOf(123);         // 숫자 → 문자열
str.equals("Hello World");   // 문자열 비교 (== 쓰지 말 것)
```

### StringBuilder
```java
StringBuilder sb = new StringBuilder("Hello");
sb.append(" World");
sb.insert(0, "Java ");
sb.delete(0, 5);
sb.deleteCharAt(0);
sb.setCharAt(0, 'h');
sb.reverse();
sb.length();
sb.toString();
```

### Character
```java
Character.isDigit('5');       // true
Character.isLetter('a');      // true
Character.isUpperCase('A');   // true
Character.isLowerCase('a');   // true
Character.toUpperCase('a');   // 'A'
Character.getNumericValue('5'); // 5
```

---

## 5. 숫자 유틸리티

### Integer / Long 상수 & 변환
```java
int maxInt = Integer.MAX_VALUE;   // 2,147,483,647
int minInt = Integer.MIN_VALUE;   // -2,147,483,648
long maxLong = Long.MAX_VALUE;    // 9,223,372,036,854,775,807

Integer.parseInt("123");          // String → int
Long.parseLong("123456789012");   // String → long
Integer.toString(123);            // int → String
String.valueOf(123L);             // long → String
```

### 비트 연산 (비트마스크 문제)
```java
Integer.toBinaryString(10);   // "1010"
Integer.bitCount(10);         // 1인 비트 개수 → 2
Integer.highestOneBit(10);    // 가장 높은 비트 → 8
n & (1 << i);   // i번째 비트 확인
n | (1 << i);   // i번째 비트 설정
n ^ (1 << i);   // i번째 비트 토글
```

### Math
```java
Math.max(10, 20);
Math.min(10, 20);
Math.abs(-10);
Math.pow(2, 3);    // 8.0
Math.sqrt(9);      // 3.0
Math.round(3.5);   // 4
Math.ceil(3.1);    // 4.0
Math.floor(3.9);   // 3.0
Math.log(Math.E);  // 1.0
```

---

## 6. Collections 유틸리티

```java
List<Integer> list = new ArrayList<>(Arrays.asList(3, 1, 5, 2, 4));

Collections.sort(list);                          // 오름차순
Collections.sort(list, Collections.reverseOrder()); // 내림차순
Collections.reverse(list);
Collections.shuffle(list);
Collections.max(list);
Collections.min(list);
Collections.frequency(list, 3);                 // 3의 개수
Collections.binarySearch(list, 3);              // 이진탐색 (정렬 필수)
Collections.swap(list, 0, 1);
Collections.fill(list, 0);
Collections.nCopies(5, 0);                      // 0이 5개인 리스트
```
