# 그리디 알고리즘

## 개념
### 현재 상황에서 가장 좋은 것을 선택하는 방법

#### 예시 1) 거스름돈 - 조건: 최소 개수의 동전 사용하기

**[상황]** 

잔액 n 원을 500원, 100원, 50원, 10원 4가지 종류의 동전을 사용하여 거슬러줘라. 

**[조건]** 

동전 개수 count를 최소로 해야한다.

**<주어진 정보>**

동전 종류 list, 잔액 n원




```python
n = 1260
count = 0

coin_types = [500, 100, 50, 10]

for coin in coin_types:
    count += n // coin # count에는 n을 나눈 몫이 더해짐
    n %= coin # n 은 해당 coin으로 나눈 나머지가 됨
    
print(count)
```

```python
6
```

**핵심 아이디어: 가장 큰 단위의 동전부터 차례대로 거슬러준다.**

**!!Point!! 단, 큰 단위가 항상 작은 단위의 배수일 때만 정당한 알고리즘 (ex. 500원은 100원, 50원, 10원의 배수이다. 100원은 50원, 10원의 배수이다)**

**<특수예시>**

가정상황: coin_types = [500, 400, 100] 이고, n = 800

위 알고리즘의 결과: 500원 1개, 100원 3개 --> count = 4

실제 정답: 400원 2개 --> count = 2



#### 예시 2) 큰 수의 법칙

**[상황]**

다양한 수로 이루어진 배열이 있을 때, 주어진 수들을 m번 더하여 가장 큰 수를 만들어야 함.

**<주어진 정보>** 

자연수 배열 list, 배열의 크기 n, 숫자가 더해지는 횟수 m, 같은 원소가 더해질 수 있는 최대 횟수 k

**Ex)**

**(Input)**  n = 5, m = 8, k =3, 배열 [2, 4, 5, 4, 6] 

**(Output)** 46

**(과정)** 6 + 6 + 6 + 5 + 6 + 6 + 6 + 5 = 46 

```python
n,m,k = map(int, input().split())
data = list(map(int, input().split()))

# start_time = time.time()

data.sort()
first = data[n-1]
second = data[n-2]

result = 0

while True:
    for i in range(k):
        if m == 0:
            break
        result += first
        m -= 1
    if m == 0:
        break
    result += second
    m -= 1
    
print(result)

# end_time = time.time()

# print("실행 시간:", end_time - start_time)
```
```python
5 8 3
2 4 5 4 6
46
실행 시간: 0.0010001659393310547
```

**핵심 아이디어: 가장 큰 수를 k번 더하고, 두번째로 큰 수를 1번 더한다** 

위의 코드는 더 효율적으로 만들 수 있다.

**위 코드의 비효율적인 점:** 현재가 k번째 반복인지 세면서 하나씩 정직하게 숫자를 더해간다. 

(result += first 가 예시다)

**더 효율적으로 만드는 방법:** 미리 first를 얼마나 더해야하는지, second를 얼마나 더해야하는지 세어본 뒤, 곱하기를 사용하면 연산 수를 줄일 수 있다. (result = count * first + (m-count) * second)



```python
# 더 효율적인 버전

n,m,k = map(int, input().split())
data = list(map(int, input().split()))

# start_time = time.time()

data.sort()
first = data[n-1]
second = data[n-2]

count = int(m / (k+1)) * k 
# (k+1) 은 k개의 first와 1개의 second로 이루어진 수열의 길이를 의미한다.
# 수열의 예시: k=3일 때, (first + first + first + second) 
# int(m / (k+1)) 는 해당 수열의 갯수를 의미한다.
count += m % (k+1)
# m이 수열의 길이 (k+1)로 나누어떨어지지 않을 때에는, 나머지만큼 first를 result에 더해주면 된다.

result = 0
result += (count) * first
result += (m-count) * second

print(result)

# end_time = time.time()

# print("실행 시간:", end_time - start_time)
```

```python
5 8 3
2 4 5 4 6
46
실행 시간: 0.002000093460083008
```

위의 예시에서는 더 효율적인 버전의 코드가 실행 시간이 더 오래걸렸다. 그 이유는 n, m, k가 작아서이다. 해당 변수들이 충분히 크다면, 더 효율적인 버전의 코드가 더 실행 시간이 적을 것이다.

#### 예시 3) 1이 될 때가지

**[상황]**

임의의 자연수 N이 1이 될 때까지 2가지 연산 중 하나를 반복적으로 선택하며 수행한다.

1. N에서 1을 뺀다.
2. N을 K로 나눈다.

**[조건]**

최소한의 연산 횟수로 진행하고, 이를 출력하라.

**<주어진 정보>**

임의의 자연수 N, K

**Ex)**

**(Input)** N = 25, K = 3

**(Output)** 6

**(과정)** 25 --> 24 --> 8 --> 7 --> 6 --> 2 --> 1

```python
n, k = map(int, input().split())
result = 0

while n >= k:
	# 나누어 떨어지지 않으면, 1씩 뺀다.
    while n % k != 0:
        n -= 1
        result += 1
    # 나누어 떨어지면, k로 나눈다.
    n //= k
    result += 1
# 더 이상 나눌 수 없을 때, 1만 반복적으로 빼서 마무리한다.
while n > 1:
    n -= 1
    result += 1

print(result)
```

```
25 3
6
```

**핵심 아이디어: 최대한 많이 나누기를 수행한다.**

위의 코드는 더 효율적으로 만들 수 있다.

**위 코드의 비효율적인 점:** n이 k로 나누어 떨어지는 수가 될 때까지 1씩 반복적으로 뺀다.

**더 효율적으로 만드는 방법:** 미리 k로 나누어 떨어지는 가장 가까운 수를 구한 뒤에, n에 할당한다.

```python
# 더 효율적인 버전

n, k = map(int, input().split())
result = 0

while True:
    target = (n//k) * k
    # (n을 k로 나눈 몫 * k) 가 k로 나누어 떨어지는 가장 가까운 수이다.
    result += (n - target)
    n = target
    if n < k:
        break
    result += 1
    n //= k
    
result += (n - 1)
print(result)
```

```python
25 3
6
```

