# 프로토콜에서의 선택적 멤버 지정

## 예시
```py
protocol Model:
    def update?(self, time),
    def render(self, container),
    optional_property?

class A<Model>:
    def render(self, container):
        ...
```

## 상세

반드시 구현해야만 하는 것이 아닌 멤버를 명시하고 싶은 경우  
 `?` 문자를 사용하여 선택적인 멤버를 지정할 수 있습니다.  

선택적인 멤버는 클래스에서 구현하지 않아도 오류를 발생시키지 않습니다.