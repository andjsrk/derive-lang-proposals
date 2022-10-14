# `operator` 수정자

## 예시

```py
operator def merge(first, second):
    return "*{first}*{second}"

result = "Hello, " merge "World!"

Stdout.print(result) # Hello, World!
```

```py
class Item:
    # ...
class Player:
    def init(self):
        self.inventory = []
    operator def picks(self, target):
        self.inventory.append(target)

player = new Player()
item = new Item()
player picks item
```

## 상세
기존의 중간자 함수를 위해 사용되던 `infix` 수정자의 텍스트를 `operator`로 변경합니다.

이 제안은 [연산자 프로토콜](/proposals/operator-protocol.md)과 [중간자 함수](https://github.com/derive-lang/Concepts/tree/5fe6a9226ee818089bd3ab63d6001be7bec3ae6a#%EC%A4%91%EA%B0%84%EC%9E%90-%ED%95%A8%EC%88%98)를 대체합니다.
