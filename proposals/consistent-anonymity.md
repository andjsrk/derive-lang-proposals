# 일관된 익명성

## 예시

### 함수
> 이전
```py
def func(self):
    pass
```
> 이후
```py
func = (self):
    pass
```

### 클래스
> 이전
```py
class A:
    def init(self):
        self.member = 1
```
> 이후
```py
A = class:
    init = (self):
        self.member = 1
```

### 네임스페이스
> 이전
```ts
namespace A:
    export foo = 1
```
> 이후
```ts
A = namespace:
    export foo = 1
```

## 상세
함수, 클래스, 네임스페이스 등의 *선언 후 값을 갖는 동시에 별도의 선언 문법이 있는 것들*의 선언 문법에서 식별자를 제거하고 결과 값을 가지도록 해 단일 식으로 취급하게 합니다.
이로 인해 함수, 클래스, 네임스페이스 등의 선언 문법이 기존의 변수 선언 문법과 차이가 없어지므로 일관성이 높아집니다.
