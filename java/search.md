# Java 코딩테스트 문법 & 내장 함수 정리

> 알고리즘 유형별로 실제 코딩테스트에서 바로 쓸 수 있는 Java 문법과 표준 라이브러리(API)를 정리한 문서입니다.
> 각 섹션은 **기본 문법 → 자주 쓰는 내장 함수 → 알고리즘 적용 예제** 순서로 구성했습니다.

---

## 목차

0. [입출력 & 기본 세팅](#0-입출력--기본-세팅)
1. [배열 / 문자열 다루기](#1-배열--문자열-다루기)
2. [컬렉션 프레임워크 총정리](#2-컬렉션-프레임워크-총정리)
3. [정렬 & Comparator](#3-정렬--comparator)
4. [탐색 알고리즘](#4-탐색-알고리즘)
5. [그래프 알고리즘](#5-그래프-알고리즘)
6. [트리 알고리즘 & 구조](#6-트리-알고리즘--구조)
7. [자료구조별 핵심 패턴](#7-자료구조별-핵심-패턴)
8. [자주 틀리는 함정 & 성능 팁](#8-자주-틀리는-함정--성능-팁)

---

## 0. 입출력 & 기본 세팅

### 0-1. 입력: `Scanner` vs `BufferedReader`

코딩테스트에서 `Scanner`는 입력량이 많으면 **시간 초과**가 나기 쉽습니다. 입력이 몇천 줄 이상이면 `BufferedReader`를 기본으로 사용하세요.

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

// 한 줄 통째로 읽기
String line = br.readLine();

// 공백으로 구분된 여러 값 읽기 (StringTokenizer가 split보다 빠름)
StringTokenizer st = new StringTokenizer(br.readLine());
int n = Integer.parseInt(st.nextToken());
int m = Integer.parseInt(st.nextToken());

// 여러 줄에 걸친 정수 입력 예시
int[] arr = new int[n];
st = new StringTokenizer(br.readLine());
for (int i = 0; i < n; i++) arr[i] = Integer.parseInt(st.nextToken());
```

| 방법 | 속도 | 편의성 | 언제 쓰나 |
|---|---|---|---|
| `Scanner` | 느림 | 편함 (`nextInt()`, `nextLine()`) | 입력이 아주 적을 때 |
| `BufferedReader` + `StringTokenizer` | 빠름 | 약간 번거로움 | **기본 선택**, 대량 입력 |

### 0-2. 출력: `System.out.println` vs `StringBuilder`

반복문 안에서 `System.out.println`을 여러 번 호출하면 느립니다. 결과를 `StringBuilder`에 모았다가 한 번에 출력하세요.

```java
StringBuilder sb = new StringBuilder();
for (int i = 0; i < n; i++) {
    sb.append(arr[i]).append('\n');
}
System.out.print(sb);

// 더 빠른 출력이 필요하면 BufferedWriter 사용
import java.io.BufferedWriter;
import java.io.OutputStreamWriter;
BufferedWriter bw = new BufferedWriter(new OutputStreamWriter(System.out));
bw.write(sb.toString());
bw.flush();
```

### 0-3. 정수 오버플로 주의

Java `int`는 약 ±21억 범위. 곱셈·합산이 커질 가능성이 있으면 `long`을 기본으로 쓰는 습관을 들이세요.

```java
long sum = 0L; // 초기값에 L을 붙여야 long 연산으로 처리됨
int a = 1_000_000_000;
long result = (long) a * a; // 캐스팅 필수 (안 하면 int 연산 후 오버플로)
```

---

## 1. 배열 / 문자열 다루기

### 1-1. 배열 관련 `java.util.Arrays`

```java
import java.util.Arrays;

int[] arr = {5, 3, 1, 4, 2};

Arrays.sort(arr);                       // 오름차순 정렬, O(n log n)
Arrays.fill(arr, 0);                    // 전체를 0으로 채움
int[] copy = Arrays.copyOf(arr, 10);    // 길이 10으로 복사(부족분 0 채움)
int[] sub = Arrays.copyOfRange(arr, 1, 3); // [1,3) 구간 복사
System.out.println(Arrays.toString(arr));  // 배열 출력용, 디버깅 필수템
boolean eq = Arrays.equals(arr, copy);  // 배열 내용 비교

// 2차원 배열 채우기 / 출력
int[][] grid = new int[3][3];
for (int[] row : grid) Arrays.fill(row, -1);
System.out.println(Arrays.deepToString(grid));

// 이진 탐색 (정렬된 배열 필수)
int idx = Arrays.binarySearch(arr, 4); // 못 찾으면 (-삽입지점-1) 반환
```

**정렬 시간복잡도 주의**: `Arrays.sort(int[])`는 **Dual-Pivot Quicksort**(비교 기반, O(n log n) 평균)를 쓰지만, `Arrays.sort(Object[])`(래퍼 타입 배열, 예: `Integer[]`)는 **TimSort**를 사용해 안정 정렬(stable sort)이 보장됩니다. Comparator를 써야 한다면 `int[]`가 아니라 `Integer[]`나 리스트를 써야 합니다.

### 1-2. 문자열 관련 `String` / `StringBuilder`

```java
String s = "hello world";

s.length();                     // 길이
s.charAt(0);                    // 특정 위치 문자
s.substring(1, 4);              // [1,4) 부분 문자열
s.split(" ");                   // 문자열 배열로 분리 (정규식 기반이라 특수문자 주의)
s.toCharArray();                // char[] 로 변환 → 정렬/순회에 유용
s.indexOf('o');                 // 첫 등장 위치, 없으면 -1
s.contains("wor");               // 포함 여부
s.replace('o', '0');            // 문자/문자열 치환
s.trim();                       // 양끝 공백 제거
s.equals(other);                // 문자열 내용 비교 (== 쓰면 안 됨!)
Character.isDigit(c);           // 숫자 여부
Character.isLetter(c);          // 알파벳 여부
Character.toUpperCase(c);       // 대문자 변환

// 문자열은 불변(immutable) → 반복적인 연결(+=)은 O(n^2), 반드시 StringBuilder 사용
StringBuilder sb = new StringBuilder();
sb.append("abc").append(123);
sb.reverse();                   // 문자열 뒤집기 (팰린드롬 문제에 자주 사용)
sb.deleteCharAt(0);             // 특정 인덱스 문자 삭제
sb.insert(0, "x");              // 특정 위치에 삽입
String result = sb.toString();
```

**핵심 트레이드오프**: `String`은 불변이라 매번 새 객체가 생성되므로, 문자열을 반복적으로 수정해야 하는 경우(슬라이딩 윈도우, 문자열 빌드) 반드시 `StringBuilder`를 사용해야 시간 초과를 피할 수 있습니다.

---

## 2. 컬렉션 프레임워크 총정리

### 2-1. 한눈에 보는 선택 가이드

| 자료구조 | 클래스 | 중복 | 순서 | 주 용도 |
|---|---|---|---|---|
| 동적 배열 | `ArrayList` | O | O(입력순) | 인덱스 접근, 값 목록 |
| 연결 리스트 | `LinkedList` | O | O | 앞/뒤 삽입삭제가 잦을 때 |
| 해시셋 | `HashSet` | X | X | 존재 여부 O(1) 확인 |
| 정렬셋 | `TreeSet` | X | O(정렬) | 범위 탐색, 최솟값/최댓값 |
| 해시맵 | `HashMap` | key 유일 | X | key-value 빠른 조회 |
| 정렬맵 | `TreeMap` | key 유일 | O(정렬) | 정렬된 key 순회 |
| 스택 | `ArrayDeque` (권장) | O | O | LIFO |
| 큐/덱 | `ArrayDeque` | O | O | FIFO, BFS |
| 우선순위 큐 | `PriorityQueue` | O | 힙 순서 | 최솟값/최댓값 우선 추출 |

> **주의**: `Stack` 클래스는 `Vector`를 상속한 레거시 클래스라 느립니다. 코딩테스트에서는 스택도 큐도 **`ArrayDeque`**를 쓰는 것이 표준입니다.

### 2-2. List — `ArrayList`, `LinkedList`

```java
import java.util.*;

List<Integer> list = new ArrayList<>();
list.add(3);
list.add(0, 1);          // 특정 위치에 삽입 O(n)
list.get(0);              // 인덱스 접근 O(1)  (ArrayList 기준)
list.set(0, 5);           // 값 변경
list.remove(0);           // 인덱스로 제거 O(n)
list.remove(Integer.valueOf(5)); // 값으로 제거 (int 오토박싱 주의!)
list.contains(3);         // 포함 여부 O(n)
list.size();
Collections.sort(list);   // 정렬
Collections.reverse(list);// 뒤집기

// 리스트 ↔ 배열 변환
Integer[] arr = list.toArray(new Integer[0]);
List<Integer> fromArr = Arrays.asList(1, 2, 3); // 크기 고정 리스트(add/remove 불가)
List<Integer> mutable = new ArrayList<>(Arrays.asList(1, 2, 3)); // 가변 리스트로 감싸기
```

**ArrayList vs LinkedList**: `ArrayList`는 내부가 배열이라 인덱스 접근이 O(1), 하지만 중간 삽입/삭제는 O(n). `LinkedList`는 양방향 연결 리스트라 앞/뒤 삽입삭제가 O(1)이지만 인덱스 접근은 O(n). 코딩테스트 대부분은 **ArrayList가 기본 선택**이고, LinkedList는 덱처럼 양끝을 다룰 때만 고려합니다(그마저도 `ArrayDeque`가 더 빠른 경우가 많음).

### 2-3. Set — `HashSet`, `TreeSet`, `LinkedHashSet`

```java
Set<Integer> set = new HashSet<>();
set.add(1);
set.contains(1);   // O(1) 평균 — 존재 여부 확인의 핵심 도구
set.remove(1);

TreeSet<Integer> ts = new TreeSet<>();
ts.add(5); ts.add(1); ts.add(3);
ts.first();          // 최솟값
ts.last();           // 최댓값
ts.ceiling(2);       // 2 이상 중 최소값
ts.floor(2);         // 2 이하 중 최대값
ts.higher(3);        // 3보다 큰 값 중 최소값
ts.lower(3);         // 3보다 작은 값 중 최대값
ts.headSet(3);       // 3 미만 부분집합
ts.tailSet(3);       // 3 이상 부분집합
```

**HashSet vs TreeSet**: `HashSet`은 순서 없이 O(1) 조회, `TreeSet`은 항상 정렬 상태를 유지하며(내부는 Red-Black Tree) O(log n) 조회. "그냥 중복 제거·존재 확인"이면 HashSet, "정렬된 상태로 범위 탐색"이 필요하면 TreeSet을 선택합니다.

### 2-4. Map — `HashMap`, `TreeMap`

```java
Map<String, Integer> map = new HashMap<>();
map.put("a", 1);
map.get("a");                      // 없으면 null 반환 (NPE 주의)
map.getOrDefault("b", 0);          // 없으면 기본값 반환 — 카운팅에 필수
map.containsKey("a");
map.remove("a");

// 빈도수 카운팅 패턴 (문자열/배열 문제에서 매우 자주 쓰임)
for (char c : s.toCharArray()) {
    map.put(c, map.getOrDefault(c, 0) + 1);
}

// merge를 쓰면 더 간결하게 카운팅 가능
map.merge('a', 1, Integer::sum);

// 순회
for (Map.Entry<String, Integer> entry : map.entrySet()) {
    entry.getKey();
    entry.getValue();
}

// TreeMap: key 기준 정렬 순회 + 범위 검색이 필요할 때
TreeMap<Integer, String> tm = new TreeMap<>();
tm.firstKey(); tm.lastKey();
tm.ceilingKey(5); tm.floorKey(5);
```

### 2-5. Deque — `ArrayDeque` (스택 + 큐 + 덱을 한 번에)

```java
Deque<Integer> deque = new ArrayDeque<>();

// 스택으로 사용 (LIFO)
deque.push(1);   // = addFirst
deque.pop();     // = removeFirst
deque.peek();    // = peekFirst

// 큐로 사용 (FIFO)
deque.offer(1);      // = addLast, 뒤에 삽입
deque.poll();        // = removeFirst, 앞에서 제거
deque.peekFirst();

// 양쪽 다 조작 가능 (덱, 슬라이딩 윈도우 최댓값 등에 사용)
deque.addFirst(0);
deque.addLast(9);
deque.pollFirst();
deque.pollLast();
```

`offer`/`poll`/`peek` 계열은 실패 시 예외 대신 `null`/`false`를 반환하므로 반복문에서 안전하게 쓸 수 있습니다(`add`/`remove`/`element`는 실패 시 예외 발생).

### 2-6. PriorityQueue (힙)

```java
// 기본: 최소 힙 (오름차순으로 꺼내짐)
PriorityQueue<Integer> minHeap = new PriorityQueue<>();
minHeap.offer(5);
minHeap.offer(1);
minHeap.poll();   // 1 (최솟값부터 꺼냄)

// 최대 힙: 역순 Comparator 전달
PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());

// 커스텀 정렬 힙 (예: 다익스트라에서 [노드, 거리] 쌍을 거리 기준 오름차순으로)
PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> a[1] - b[1]);
pq.offer(new int[]{node, dist});
```

배열 정렬(O(n log n) 한 번)과 힙(삽입/삭제마다 O(log n))의 트레이드오프: **"전체 정렬"이 아니라 "최솟값/최댓값을 반복적으로 꺼내야 하는 상황"**(예: K번째 최솟값, 다익스트라, 우선순위 스케줄링)이라면 힙이 유리합니다.

---

## 3. 정렬 & Comparator

```java
// 기본 오름차순
int[] arr = {3, 1, 2};
Arrays.sort(arr);

// 객체 리스트: Comparable 구현 또는 Comparator 전달
List<int[]> list = new ArrayList<>();
list.sort((a, b) -> a[0] - b[0]);              // 첫 값 기준 오름차순
list.sort(Comparator.comparingInt(a -> a[0])); // 위와 동일, 가독성 ↑

// 다중 조건 정렬: 첫 값 오름차순, 같으면 두 번째 값 내림차순
list.sort(Comparator.comparingInt((int[] a) -> a[0])
                     .thenComparing(a -> -a[1]));

// 내림차순
Arrays.sort(arr2, Comparator.reverseOrder()); // 단, arr2는 Integer[]여야 함 (int[]는 불가)

// 객체(사용자 클래스) 정렬: Comparable 구현
class Point implements Comparable<Point> {
    int x, y;
    public int compareTo(Point o) { return this.x - o.x; }
}
```

**주의**: 기본형 배열(`int[]`)은 `Comparator`를 받는 `sort` 오버로드가 없습니다. 커스텀 정렬이 필요하면 `Integer[]`나 `List<Integer>`, 또는 2차원 배열(`int[][]`)을 써야 합니다. `int[][]`는 `Comparator<int[]>`로 정렬 가능합니다.

---

## 4. 탐색 알고리즘

### 4-1. 이진 탐색 (Binary Search)

```java
// 라이브러리 사용 (정렬된 배열/리스트 필수)
int idx = Arrays.binarySearch(arr, target);

// 직접 구현 — "조건을 만족하는 경계값 찾기" 패턴이 코테에서 더 자주 나옴
int lo = 0, hi = arr.length - 1;
while (lo <= hi) {
    int mid = lo + (hi - lo) / 2; // (lo+hi)/2 는 오버플로 위험, 이 방식이 안전
    if (arr[mid] == target) { idx = mid; break; }
    else if (arr[mid] < target) lo = mid + 1;
    else hi = mid - 1;
}

// Parametric Search 패턴 (예: "최대 얼마까지 가능한가?")
int lo = 1, hi = maxValue, answer = 0;
while (lo <= hi) {
    int mid = lo + (hi - lo) / 2;
    if (isPossible(mid)) { answer = mid; lo = mid + 1; }
    else hi = mid - 1;
}
```

### 4-2. BFS / DFS

```java
// BFS: ArrayDeque를 큐로 사용
boolean[] visited = new boolean[n];
Deque<Integer> queue = new ArrayDeque<>();
queue.offer(start);
visited[start] = true;
while (!queue.isEmpty()) {
    int cur = queue.poll();
    for (int next : graph.get(cur)) {
        if (!visited[next]) {
            visited[next] = true;
            queue.offer(next);
        }
    }
}

// DFS: 재귀 또는 스택(ArrayDeque)
void dfs(int cur, boolean[] visited, List<List<Integer>> graph) {
    visited[cur] = true;
    for (int next : graph.get(cur)) {
        if (!visited[next]) dfs(next, visited, graph);
    }
}
```

**BFS vs DFS 선택 기준**: 최단 거리(간선 가중치 없음)가 필요하면 BFS, 단순 도달 가능 여부나 모든 경로 탐색·백트래킹이면 DFS. 재귀 DFS는 그래프가 깊으면 `StackOverflowError` 위험이 있으므로, 노드 수가 많은 경우(수만 개 이상)엔 명시적 스택을 쓴 반복문 DFS를 고려하세요.

### 4-3. Union-Find (Disjoint Set)

```java
int[] parent = new int[n];
for (int i = 0; i < n; i++) parent[i] = i;

int find(int x) {
    if (parent[x] == x) return x;
    return parent[x] = find(parent[x]); // 경로 압축(Path Compression)
}

void union(int a, int b) {
    a = find(a); b = find(b);
    if (a != b) parent[a] = b;
}
```

경로 압축만 적용해도 대부분의 코테 시간 제한 안에 충분합니다. 사이클 판별, 크루스칼 MST, 네트워크 연결 여부 판단에 필수 도구입니다.

### 4-4. 백트래킹 (Backtracking)

```java
List<List<Integer>> result = new ArrayList<>();
boolean[] used = new boolean[n];

void backtrack(int[] nums, List<Integer> current) {
    if (current.size() == nums.length) {
        result.add(new ArrayList<>(current)); // 반드시 복사본을 담아야 함
        return;
    }
    for (int i = 0; i < nums.length; i++) {
        if (used[i]) continue;
        used[i] = true;
        current.add(nums[i]);
        backtrack(nums, current);
        current.remove(current.size() - 1); // 되돌리기(가지치기 복구)
        used[i] = false;
    }
}
```

`result.add(current)`가 아니라 `result.add(new ArrayList<>(current))`로 **복사본**을 넣는 것이 핵심 함정입니다. 그냥 넣으면 이후 백트래킹 과정에서 같은 리스트 객체가 계속 변형되어 결과가 전부 빈 리스트로 남습니다.

### 4-5. 다익스트라 (Dijkstra)

```java
int[] dist = new int[n];
Arrays.fill(dist, Integer.MAX_VALUE);
dist[start] = 0;

PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> a[1] - b[1]);
pq.offer(new int[]{start, 0});

while (!pq.isEmpty()) {
    int[] cur = pq.poll();
    int node = cur[0], d = cur[1];
    if (d > dist[node]) continue; // 이미 더 짧은 경로로 갱신된 경우 스킵
    for (int[] edge : graph.get(node)) {
        int next = edge[0], weight = edge[1];
        if (dist[node] + weight < dist[next]) {
            dist[next] = dist[node] + weight;
            pq.offer(new int[]{next, dist[next]});
        }
    }
}
```

---

## 5. 그래프 알고리즘

### 5-1. 그래프 표현: 인접 리스트 vs 인접 행렬

```java
// 인접 리스트 (간선이 적은 희소 그래프에 유리, 대부분의 코테에서 기본 선택)
List<List<int[]>> graph = new ArrayList<>(); // [노드, 가중치]
for (int i = 0; i < n; i++) graph.add(new ArrayList<>());
graph.get(a).add(new int[]{b, weight});

// 인접 행렬 (노드 수가 적고 간선이 촘촘한 경우, 두 노드 연결 여부를 O(1)로 확인해야 할 때)
int[][] adjMatrix = new int[n][n];
adjMatrix[a][b] = weight;
```

**트레이드오프**: 노드 수 V, 간선 수 E일 때 인접 리스트는 공간 O(V+E), 인접 행렬은 공간 O(V²). 노드가 수만 개 이상이면 인접 행렬은 메모리 초과 위험이 크므로 인접 리스트가 사실상 표준입니다.

### 5-2. 위상 정렬 (Topological Sort) — Kahn's Algorithm

```java
int[] indegree = new int[n];
for (List<Integer> edges : graph) for (int next : edges) indegree[next]++;

Deque<Integer> queue = new ArrayDeque<>();
for (int i = 0; i < n; i++) if (indegree[i] == 0) queue.offer(i);

List<Integer> order = new ArrayList<>();
while (!queue.isEmpty()) {
    int cur = queue.poll();
    order.add(cur);
    for (int next : graph.get(cur)) {
        if (--indegree[next] == 0) queue.offer(next);
    }
}
// order.size() != n 이면 사이클 존재
```

---

## 6. 트리 알고리즘 & 구조

### 6-1. 이진 트리 순회

```java
class TreeNode {
    int val;
    TreeNode left, right;
    TreeNode(int val) { this.val = val; }
}

// 전위(Preorder): 루트 → 왼쪽 → 오른쪽
void preorder(TreeNode node) {
    if (node == null) return;
    System.out.print(node.val + " ");
    preorder(node.left);
    preorder(node.right);
}

// 중위(Inorder): 왼쪽 → 루트 → 오른쪽 — BST에서는 정렬된 순서로 나옴
void inorder(TreeNode node) {
    if (node == null) return;
    inorder(node.left);
    System.out.print(node.val + " ");
    inorder(node.right);
}

// 레벨 순회(BFS): ArrayDeque 사용
void levelOrder(TreeNode root) {
    Deque<TreeNode> queue = new ArrayDeque<>();
    queue.offer(root);
    while (!queue.isEmpty()) {
        TreeNode cur = queue.poll();
        System.out.print(cur.val + " ");
        if (cur.left != null) queue.offer(cur.left);
        if (cur.right != null) queue.offer(cur.right);
    }
}
```

### 6-2. Trie (트라이)

```java
class TrieNode {
    TrieNode[] children = new TrieNode[26];
    boolean isEnd = false;
}

class Trie {
    TrieNode root = new TrieNode();

    void insert(String word) {
        TrieNode node = root;
        for (char c : word.toCharArray()) {
            int idx = c - 'a';
            if (node.children[idx] == null) node.children[idx] = new TrieNode();
            node = node.children[idx];
        }
        node.isEnd = true;
    }

    boolean search(String word) {
        TrieNode node = root;
        for (char c : word.toCharArray()) {
            int idx = c - 'a';
            if (node.children[idx] == null) return false;
            node = node.children[idx];
        }
        return node.isEnd;
    }
}
```

### 6-3. 세그먼트 트리 (Segment Tree) — 구간 합

```java
int[] tree;
int n;

void build(int[] arr) {
    n = arr.length;
    tree = new int[4 * n];
    build(arr, 1, 0, n - 1);
}

void build(int[] arr, int node, int start, int end) {
    if (start == end) { tree[node] = arr[start]; return; }
    int mid = (start + end) / 2;
    build(arr, node * 2, start, mid);
    build(arr, node * 2 + 1, mid + 1, end);
    tree[node] = tree[node * 2] + tree[node * 2 + 1];
}

int query(int node, int start, int end, int l, int r) {
    if (r < start || end < l) return 0;
    if (l <= start && end <= r) return tree[node];
    int mid = (start + end) / 2;
    return query(node * 2, start, mid, l, r) + query(node * 2 + 1, mid + 1, end, l, r);
}

void update(int node, int start, int end, int idx, int val) {
    if (start == end) { tree[node] = val; return; }
    int mid = (start + end) / 2;
    if (idx <= mid) update(node * 2, start, mid, idx, val);
    else update(node * 2 + 1, mid + 1, end, idx, val);
    tree[node] = tree[node * 2] + tree[node * 2 + 1];
}
```

### 6-4. 펜윅 트리 (Fenwick Tree / BIT) — 구간 합, 더 가볍게

```java
int[] bit; // 1-indexed
int n;

void update(int i, int delta) {
    for (; i <= n; i += i & (-i)) bit[i] += delta;
}

int query(int i) { // 1 ~ i 누적합
    int sum = 0;
    for (; i > 0; i -= i & (-i)) sum += bit[i];
    return sum;
}

int rangeQuery(int l, int r) {
    return query(r) - query(l - 1);
}
```

**세그먼트 트리 vs 펜윅 트리**: 둘 다 구간 합/최솟값 등을 O(log n)에 처리하지만, 펜윅 트리는 코드가 훨씬 짧고 메모리도 적게 씁니다. 다만 펜윅 트리는 "구간 합" 같은 결합·역연산이 가능한 연산에 최적화되어 있고, 세그먼트 트리는 구간 최솟값/최댓값처럼 역연산이 없는 질의나 구간 업데이트(lazy propagation)가 필요한 복잡한 문제에서 더 유연합니다. 단순 구간 합 문제라면 펜윅 트리로 충분합니다.

---

## 7. 자료구조별 핵심 패턴

### 7-1. 투 포인터 & 슬라이딩 윈도우

```java
// 투 포인터: 정렬된 배열에서 두 수의 합 찾기
int left = 0, right = arr.length - 1;
while (left < right) {
    int sum = arr[left] + arr[right];
    if (sum == target) { /* 처리 */ break; }
    else if (sum < target) left++;
    else right--;
}

// 슬라이딩 윈도우: 고정 크기 K 구간의 최댓값 합
int windowSum = 0;
for (int i = 0; i < k; i++) windowSum += arr[i];
int maxSum = windowSum;
for (int i = k; i < arr.length; i++) {
    windowSum += arr[i] - arr[i - k];
    maxSum = Math.max(maxSum, windowSum);
}
```

### 7-2. 모노토닉 스택 (Monotonic Stack)

```java
// 예: 각 원소보다 오른쪽에서 처음으로 더 큰 값의 인덱스 찾기
int[] result = new int[arr.length];
Arrays.fill(result, -1);
Deque<Integer> stack = new ArrayDeque<>(); // 인덱스를 저장

for (int i = 0; i < arr.length; i++) {
    while (!stack.isEmpty() && arr[stack.peek()] < arr[i]) {
        result[stack.pop()] = i;
    }
    stack.push(i);
}
```

스택에 "아직 답을 찾지 못한 인덱스"만 감소(또는 증가) 순서로 유지하기 때문에 전체 시간복잡도가 O(n)입니다. 이중 반복문으로 짜면 O(n²)이 되는 문제를 O(n)으로 줄여주는 대표적 패턴입니다.

### 7-3. LRU 캐시 (LinkedHashMap 활용)

```java
class LRUCache extends LinkedHashMap<Integer, Integer> {
    int capacity;

    LRUCache(int capacity) {
        super(capacity, 0.75f, true); // accessOrder=true: 접근 순서 유지
        this.capacity = capacity;
    }

    protected boolean removeEldestEntry(Map.Entry<Integer, Integer> eldest) {
        return size() > capacity; // 용량 초과 시 가장 오래된 항목 자동 제거
    }
}
```

`LinkedHashMap`의 `accessOrder=true` 옵션과 `removeEldestEntry` 오버라이드만으로 LRU 캐시를 몇 줄에 구현할 수 있습니다. 직접 이중 연결 리스트 + 해시맵을 구현하는 방식(더 로우레벨한 이해가 필요할 때)과의 트레이드오프를 알아두면 좋습니다.

### 7-4. Floyd 사이클 탐지 (연결 리스트)

```java
boolean hasCycle(ListNode head) {
    ListNode slow = head, fast = head;
    while (fast != null && fast.next != null) {
        slow = slow.next;
        fast = fast.next.next;
        if (slow == fast) return true; // 토끼와 거북이가 만나면 사이클
    }
    return false;
}
```

---

## 8. 자주 틀리는 함정 & 성능 팁

| 함정 | 설명 |
|---|---|
| `==` vs `.equals()` | `String`, `Integer` 등 객체 비교는 반드시 `.equals()`. `Integer`는 -128~127 범위에서만 캐싱되어 `==`이 우연히 통과할 수 있으니 습관적으로 `.equals()` 사용 |
| 오토박싱 오버헤드 | `List<Integer>`는 int마다 박싱/언박싱 비용 발생. 성능이 민감하면 원시 배열(`int[]`) 사용 고려 |
| `list.remove(int)` | `remove(5)`는 "인덱스 5"를 제거. 값 5를 제거하려면 `remove(Integer.valueOf(5))` |
| 배열 크기 오버플로 | `new int[n][m]`에서 n, m이 매우 크면 `OutOfMemoryError`. 문제의 제약 조건을 항상 확인 |
| 재귀 깊이 | Java 기본 스택 크기는 재귀가 깊은 DFS(수만 단계)에서 `StackOverflowError` 유발 가능 → 반복문 전환 고려 |
| `HashMap` 순서 미보장 | 입력 순서가 필요하면 `LinkedHashMap`, 정렬 순서가 필요하면 `TreeMap` |
| `String.split` 정규식 | `split("|")`처럼 정규식 특수문자를 구분자로 쓰면 오류 → `split("\\|")`로 이스케이프 필요 |
| `PriorityQueue`는 정렬된 배열이 아님 | `peek()`으로 최솟값만 O(1) 확인 가능, 내부 배열 순서 자체는 정렬되어 있지 않음 |

---

### 참고: 시간복잡도 요약

| 자료구조 | 삽입 | 삭제 | 탐색 |
|---|---|---|---|
| ArrayList | O(1)~O(n) | O(n) | O(1) (인덱스), O(n) (값) |
| LinkedList | O(1) (양끝) | O(1) (양끝) | O(n) |
| HashMap/HashSet | O(1) 평균 | O(1) 평균 | O(1) 평균 |
| TreeMap/TreeSet | O(log n) | O(log n) | O(log n) |
| PriorityQueue | O(log n) | O(log n) (poll) | O(1) (peek) |
| ArrayDeque | O(1) (양끝) | O(1) (양끝) | O(n) |
