# 구간 합 (Prefix Sum)

### 개념
'구간 합' 또는 '누적 합' 은 합 배열을 통해 특정 구간의 합을 구하는 알고리즘
<br>

### 합 배열 정의
> // 기존 배열을 arr, 합 배열을 sumArr 라고 한다면
1) sumArr[i] = arr[0] + arr[1] + ... + arr[i - 1] + arr[i]
2) sumArr[i] = sumArr[i - 1] + arr[i]

<br>

예시 1)

|||||||
|:---:|:---:|:---:|:---:|:---:|:---:|
|array|5|4|3|2|1|
|sumArr|5|9|12|14|15|
|||||||

만약 2번 ~ 4번 숫자의 합을 구해야 한다면 간단하게 4 + 3 + 2 = 9 라는 것을 알 수 있다.

하지만 각 인덱스를 순회하면서 합을 구하는 방법의 시간 복잡도는 O(N)이 된다.

이러한 경우 합 배열을 사용하면 O(1)로도 합을 구할 수 있음
<br><br>

### 구간 합 구하는 방법
> // i에서 j까지의 구간 합
sumArr[j] - sumarr[i - 1]

<br>

예시 2)

|||||||||
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|array|2|11|9|7|3|13|5|
|sumArr|2|13|22|29|32|45|50|
|||||||

<br>

만약 3번 ~ 5번 숫자의 합을 구한다면 실제 배열에선 2번 인덱스부터 4번 인덱스까지의 합을 구하는 것이다.

즉, arr[2] + arr[3] + arr[4] = 9 + 7 + 3 = 19 이 된다.

1) 처음 ~ 5번 숫자의 합 : sumArr[4] = arr[0] + arr[1] + arr[2] + arr[3] + arr[4] = 32
2) 처음 ~ 2번 숫자의 합 : sumArr[1] = arr[0] + arr[1] = 13
3) sumArr[4] - sumArr[1] = arr[2] + arr[3] + arr[4] = 32 - 19 = 13

<br>

즉, 구하고자 하는 구간까지의 합을 구한 뒤, 구하고자 하는 범위 전까지의 합을 빼는 것.<br>
따라서 i에서 j까지의 구간 합 공식이 sumArr[j] - sumArr[i - 1] 이 된 것.<br>
이렇게 한 번의 뺄셈을 통해 원하는 값을 구할 수 있기 때문에 O(1)

여기서 주의할 점은 기존 배열은 상관 없지만, 합 배열은 기존 배열의 크기보다 +1 로 설정해야 함.<br>
만약 0 ~ n번 숫자의 합을 구한다면 sumArr[0 - 1] = sumArr[-1] 이 되면서 인덱스 범위에 벗어남.<br>
아래는 N 크기를 가진 배열에서  a ~ b 까지의 구간 합을 구하는 코드이다.
<br><br>

```java
import java.util.*;
import java.io.*;

public class Main {
    public static void main(String[] args) throws IOException {
        BufferedReader bf = new BufferedReader(new InputStreamReader(System.in));
        StringTokenizer stk = new StringTokenizer(bf.readLine());

        int N = Integer.parseInt(stk.nextToken());

        stk = new StringTokenizer(bf.readLine());
        int[] numbers = new int[N + 1];

        // 구간 합 계산
        for (int i = 1; i <= N; i++) {
            numbers[i] = numbers[i - 1] + Integer.parseInt(stk.nextToken());
        }

        int a = Integer.parseInt(stk.nextToken());
        int b = Integer.parseInt(stk.nextToken());
        System.out.println(numbers[b] - numbers[a - 1]);
    }
}
```