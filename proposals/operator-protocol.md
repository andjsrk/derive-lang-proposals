# 연산자 프로토콜

## 예시

```py
class Item:
    ...
class World:
    ...

protocol PlayerOperation(Operation.BasicOperator):

    def [new Operation.OperatorName("picks")](self, target),
    def [new Operation.OperatorName(">>")](self, target)

class Player<PlayerOperation>:

    def init(self):
        self.inventory = []

    def [new Operation.OperatorName("picks")](self, target):
        self.inventory.append(target)

    def [new Operation.OperatorName(">>")](self, target):
        target.add_child(self.items.shift())

world = new World()
player = new Player()
item = new Item()

player picks item
player >> world
```

## 상세

> 이 제안은 다른 제안 '프로토콜에서의 선택적 멤버 지정'에 종속적입니다.

**연산자 프로토콜**은 `Operation` 네임스페이스를 통해 실현되는 연산자 오버로딩 방법입니다.  

`>` `+` 와 같은 기존 연산자뿐만 아니라 `abcd` `pick` `give` `destroy`등과 같은 단어들이 모두 연산자가 될 수 있으며,  
이를 통해 **누산기 프로토콜**을 종속적으로 정의하고 **중간자 함수(infix function)** 를 대체할 수 있습니다.  

기존의 방법 안에서 연산자 프로토콜의 실현은 다소 복잡한 과정을 거칩니다.  

### 내부
```py
namespace Operation:
    export class OperatorName(Unique):
        ...
    export protocol BasicOperator:
        def [new Operation.OperatorName("+")]?(self, target),
        def [new Operation.OperatorName("-")]?(self, target),
        ...
```
우선 `Operation` 네임스페이스가 다음과 같은 형태라고 가정합니다.    

클래스가 구현할 _기본_ 연산자를 미리 프로토콜에 명시하기 위해서, 연산자의 형태를 인자로 받는 `OperatorName` 클래스가 필요합니다.  


`def [...]`의 안에는 `Unique` 인스턴스 혹은 문자열만이 올 수 있으며, 따라서 `OperatorName` 클래스는 `Unique` 클래스를 상속합니다.  

### 연산자의 정의
`BasicOperator` 프로토콜은 클래스가 구현하지 않아도 작동하는 _기본_ 연산자들에 대한 정보를 담고 있습니다.  

```py
protocol ExtendedOperator(BasicOperator):
    def [new Operation.OperatorName("pick")](self, target)

class A<ExtendedOperator>:
    def [new Operation.OperatorName("pick")](self, target):
        ...
```

새로운 연산자를 정의할 때는 `BasicOperator` 프로토콜을 상속하는 새로운 프로토콜을 생성한 뒤 이를 준수하는 클래스가 적절한 구현을 가져야 합니다.  

### `operator` 키워드
그러나 연산자를 정의할 때 사용하는 `new Operation.OperatorName("...")` 형식의 문법은 너무 길기 때문에, `operator` 키워드를 통한 문법 설탕을 제공합니다.  

`operator "..."`는 `new Operation.OperatorName("...")`를 반환하여 동일한 동작을 간결하게 수행할 수 있도록 도와줍니다.

```py
class A<ExtendedOperator>:
    def [operator "+"](self, target):
        ...
    def [operator "pick"](self, target):
        ...
```

### 누산기 프로토콜

연산자 프로토콜을 사용하면 누산기 프로토콜을 다음과 같이 종속적으로 정의할 수 있습니다.  

```py
namespace Data:
    ...
    export accumulator = operator "->"
    export protocol Accumulator:
        def [Data.accumulator](self, target)
```


### 과제

그러나 연산자 프로토콜에는 다음과 같은 문제점이 존재합니다.  

- 사용자 정의를 위해 프로토콜을 매번 생성하는 것이 번거로움
- Derive 인터프리터에게 연산자를 구현함을 알리기 위해 `BasicOperator`를 사용해야 함

프로토콜은 클래스의 구현 규칙이기도 하면서 Derive의 인터프리터에게 정보를 제공하는 수단이기도 합니다.  
앞서 제안한 내용에서는 `BasicOperator` 프로토콜을 사용하여 기본 연산자들을 나타냄과 동시에,  
 이를 준수하는 클래스가 연산자에 대한 새로운 구현을 가지고 있음을 Derive 인터프리터에게 알리는 방법을 사용했습니다.  

그러나 `BasicOperator`를 사용했다고 하더라도 항상 기본 연산자를 오버라이드 하는 것은 아닙니다.  

다시말해 `BasicOperator`는 두 방면에서 사용되고 있지만 다소 모호한 역할을 취하고 있다는 것입니다.  

따라서 `BasicOperator`에게서 기본 연산자를 나타내는 역할을 제거하고, Derive 인터프리터에게 정보를 제공하는 목적이 조금 더 초점을 맞추는 방안 또한 고려해볼 수 있습니다.  

### `should` 키워드를 사용한 연산자 프로토콜

`BasicOperator`를 상속하는 새로운 프로토콜을 생성하는 방식과는 다소 차이점이 존재하는 아이디어입니다.  

```py
namespace Operation:
    export class OperatorName(Unique):
        ...
    export protocol Operator:
        def [should OperatorName](self, target)
        ...
```

이전과는 다르게 `should` 키워드를 사용하면 `Operation` 네임스페이스는 다음과 같이 간략하게 정의됩니다.  

### `should` 키워드
`should`는 `def [...]`의 내부에서만 사용할 수 있는 특별한 키워드로,  
클래스가 프로토콜을 준수할 때 특정한 클래스의 인스턴스이면  
 메서드의 이름으로 모두 허용함을 나타냅니다.  

`should`로 지정하는 클래스는 반드시 `Unique` 클래스를 상속해야 합니다.  

### `BasicOperator` 프로토콜 없이 수행하는 연산자 정의

```py
class Player<Operation.Operator>:

    def [operator "picks"](self, target):
        ...

    def [operator ">>"](self, target):
        ...
```

`BasicOperator`를 상속하는 새로운 프로토콜을 생성해야 한다는 점을 제외하면 기존과 동일합니다.





