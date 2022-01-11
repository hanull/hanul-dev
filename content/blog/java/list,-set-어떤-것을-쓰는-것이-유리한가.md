---
title: List, Set 어떤 것을 쓰는 것이 유리한가
date: 2022-01-11 11:01:33
category: java
thumbnail: { thumbnailSrc }
draft: false
---

### 배경
알고리즘 문제([여기에 정리](https://hanul-dev.netlify.app/algorithms/%EB%B0%B1%EC%A4%8013902_%EA%B0%9C%EC%97%852/))를 풀다가 LinkedList 와 HashSet 의 contains() 속도 차이가 크다는 것을 알게 되었다. 

직접 테스트 해보고 이를 정리하려고 한다.

### 속도 비교
10000000의 숫자를 ArrayList, LinkedList, HashSet에 각각 저장했고, 특정 위치에 저장할 때 걸리는 시간과 5000000 값이 존재하는지 확인하는데 걸리는 시간을 측정해봤다.

```java
int size = 10000000;
List<Integer> arrayList = new ArrayList<>();
List<Integer> linkedList = new LinkedList<>();
Set<Integer> hashSet = new HashSet<>();

for (int i = 0; i <= size; i++) {
    arrayList.add(i);
    linkedList.add(i);
    hashSet.add(i);
}

System.out.println("1. add() test : ArrayList vs LinkedList");
// ArrayList add test
Instant start = Instant.now();
arrayList.add(3000, -1);
Instant end = Instant.now();
long time = Duration.between(start, end).toMillis();
System.out.println("ArrayList add(3000, -1) time : " + time);

// LinkedList add test
start = Instant.now();
linkedList.add(3000, -1);
end = Instant.now();
time = Duration.between(start, end).toMillis();
System.out.println("LinkedList add(3000, -1) time : " + time);

System.out.println();
System.out.println("2. contains() test : ArrayList vs LinkedList vs HashSet");
int targetNumber = 5000000;

// ArrayList contains test
start = Instant.now();
arrayList.contains(targetNumber);
end = Instant.now();
time = Duration.between(start, end).toMillis();
System.out.println("ArrayList search time : " + time);

// linkedList contains test
start = Instant.now();
linkedList.contains(targetNumber);
end = Instant.now();
time = Duration.between(start, end).toMillis();
System.out.println("linkedList search time : " + time);

// HashSet contains test
start = Instant.now();
hashSet.contains(targetNumber);
end = Instant.now();
time = Duration.between(start, end).toMillis();
System.out.println("HashSet search time : " + time);
```

```
1. add() test : ArrayList vs LinkedList
ArrayList add(3000, -1) time : 5
LinkedList add(3000, -1) time : 0

2. contains() test : ArrayList vs LinkedList vs HashSet
ArrayList search time : 20
linkedList search time : 54
HashSet search time : 0
```

### 특징
왜 위와 같은 결과가 나왔을까?

`ArrayList`는 중복을 허용하고 순서를 보장하여 데이터를 저장하고 배열과 같이 인덱스로 내부의 객체를 관리한다. 따라서 특정 위치의 데이터에 접근하는 속도가 빠르다. 하지만, 특정 위치(i)에 삽입을 할 때는 i + 1 번 데이터부터 끝까지의 데이터를 한 칸씩 이동해야 하기 때문에 느리고, 삭제 또한 마찬가지이다. 또한, contains 메소드 실행 시 처음 순차적으로 데이터 탐색을 진행하면서 값을 찾기 때문에 시간이 오래 걸린다.

`LinkedList`는 노드 간에 연결을 통해서 내부 객체를 관리한다. 삽입이나 삭제 시, 해당 노드의 위치에 연결된 정보만 바꿔주면 되기 때문에 속도가 빠르다. 하지만, 데이터에 접근할 때는 맨 앞의 노드부터 순차적으로 접근해야하기 때문에 느리다. 

contains() 속도가 ArrayList가 LinkedList보다 빠른 것은 ArrayList가 내부적으로 배열을 사용하고 인덱스로 접근하기 때문인 것 같다.


`HashSet`은 비선형 구조로 순서가 없고 인덱스도 존재하지 않는다. 또한, 중복을 자동으로 제거해준다. 데이터를 있는지 확인할 때는 모든 데이터를 찾아보는 것이 아니라 데이터를 key로 순서와 상관없이 바로 확인한다. 따라서 List보다 속도가 훨씬 빠르다는 것을 알 수 있다.

**HashSet은 어떻게 중복 값을 걸러내는가?**
- HashSet은 내부적으로 HashMap을 호출해서 구현한다.
- 저장하기 전에 먼저 hashCode() 메소드를 호출해서 같은 해시 코드가 있는지 확인을 한다. 만약 같은 해시 코드가 있다면, equals() 메소드로 중복 여부를 검사한다.


|                   | ArrayList | LinkedList | HashSet |
| ----------------- | --------- | ---------- | ------- |
| add()             | O(1)      | O(1)       | O(1)    |
| add(index, value) | O(N)      | O(1)       |         |
| remove()          | O(N)      | O(1)       | O(1)    |
| contains()        | O(N)      | O(N)       | O(1)    |
| get()             | O(1)      | O(N)       |         |


### 그래서 언제 어떤 자료구조를 쓰는 것이 좋다는거야?
상황마다 다를 것이다. 순서를 보장해야하고 중복을 허용한다면 List 를 사용할 것이고, 중복을 허용하지 않고 순서를 보장할 필요가 없다면 Set을 쓸 것이다. 하지만 **List를 사용하거나 Set을 사용하거나 동작에 차이가 없는 상황이라면**, `HashSet`을 사용하는 것이 훨씬 유리하다.