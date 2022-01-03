---
title: 백준 카우버거 알바생 JAVA
date: 2021-10-19 20:10:02
category: algorithms
draft: false
---

## 문제

[카우버거 알바생](https://www.acmicpc.net/problem/17208)



### 처음에 생각했던 방법

1. N개의 주문 중 남은 치즈버거 개수, 남은 감자튀김 개수를 초과하지 않은 경우를 모두 List에 담는다.
2. 예외를 처리한다.
   - 담긴 list의 크기가 1이하일 경우, 주문할 수 있는 경우가 없거나 하나의 주문만 할 수 있는 경우이기 때문에 list.size( )를 출력해주고 종료한다.
   - 모든 주문이 다 사용가능한 주문이고(list.size( ) == N)  남은 치즈버거와 감자튀김으로 모든 주문을 충당 가능할 경우, 모든 주문의 수(N) 을 출력해주고 종료한다.
3. i 번의 주문을 처리할 경우와 처리하지 않을 경우를 나누어서 dfs를 돌린다.

```java
private static void dfs(int burger, int fries, int index, int total) {
  if (burger > maxBurger || fries > maxFries) return;
  if (index == list.size()) {
    answer = Math.max(answer, total);
    return;
  }

  // 주문을 선택할 경우
  dfs(burger + list.get(index).burger, fries + list.get(index).fries, index + 1, total + 1);

  // 주문을 선택하지 않을 경우
  dfs(burger, fries, index + 1, total);
}
```

해당 방법으로 코드를 작성하고 제출했을 때, `시간초과` 가 나왔다. 어찌보면 당연한 결과였다. 왜냐하면 N의 범위는 최대 100이고, O(2^100) 의 시간 복잡도를 가지게 되기 때문이다. 

시간 제한이 3초이기 때문에 넉넉하겠지 라고 생각했고, N의 범위와 시간복잡도를 고려하지 않은 결과이다. 



시간 초과를 해결하기 위해서는 `메모이제이션` 을 통해 중복되는 호출을 방지해야한다. `dp[치즈버거 수][감자튀김 수][주문번호]` 배열을 만들어서 (1) 주문을 할 수 있는 경우, (2) 주문을 할 수 없는 경우로 나누어서 최대 주문수를 구하고, 갱신해줬다. 이 과정에서 dp배열에 초기값(-1)이 아닌 갱신된 값이 저장되어 있으면 바로 return 해서 이미 계산된 값을 반환해줬다.

아래는 최종적으로 작성한 코드이다.

### 최종 소스 코드

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.StringTokenizer;

public class Main {

    static int N, maxBurger, maxFries;
    static int[][][] dp;
    static List<Order> list = new ArrayList<>();
    static class Order {
        int burger, fries;

        public Order(int burger, int fries) {
            this.burger = burger;
            this.fries = fries;
        }
    }

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StringTokenizer st = new StringTokenizer(br.readLine());
        N = stoi(st.nextToken());
        maxBurger = stoi(st.nextToken());
        maxFries = stoi(st.nextToken());
        dp = new int[maxBurger + 1][maxFries + 1][N];
        initDP();
        int totalBurger = 0;
        int totalFries = 0;
        for (int i = 0; i < N; i++) {
            st = new StringTokenizer(br.readLine());
            int burger = stoi(st.nextToken());
            int fries = stoi(st.nextToken());
            totalBurger += burger;
            totalFries += fries;
            if (burger > maxBurger || fries > maxFries) continue;
            list.add(new Order(burger, fries));
        }
        if (list.size() <= 1) {
            System.out.println(list.size());
            System.exit(0);
        }
        if (list.size() == N && totalBurger <= maxBurger && totalFries <= maxFries) {
            System.out.println(N);
            System.exit(0);
        }
        System.out.println(dfs(0, 0, 0, 0));
    }

    private static void initDP() {
        for (int i = 0; i <= maxBurger; i++) {
            for (int j = 0; j <= maxFries; j++) {
                for (int k = 0; k < N; k++) {
                    dp[i][j][k] = -1;
                }
            }
        }
    }

    private static int dfs(int burger, int fries, int index, int total) {
        if (index == list.size()) return 0;
        if (dp[burger][fries][index] >= 0) return dp[burger][fries][index];

        int answer = 0;
        // 주문을 선택할 경우
        if (burger + list.get(index).burger <= maxBurger && fries + list.get(index).fries <= maxFries) {
            answer = dfs(burger + list.get(index).burger, fries + list.get(index).fries, index + 1, total + 1) + 1;
        }
        // 주문을 선택하지 않을 경우
        answer = Math.max(answer, dfs(burger, fries, index + 1, total));
        return dp[burger][fries][index] = answer;
    }

    private static int stoi(String input) {
        return Integer.parseInt(input);
    }
}

```

