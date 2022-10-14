# `do` / `action` 표현식

## 예시

```py
three = do
    two = 2
    return 1 + two
# three = 3
```

```py
print_and_sum = (a, b): do
    Stdout.print(a)
    Stdout.print(b)
    return a + b
```

```py
print_three_times = (x): action
    print(x)
    print(x)
    print(x)
```

## 상세
### `do` 표현식
때때로 단일 표현식이 아닌 코드를 실행한 뒤에 단일 표현식과 유사하게 결과값을 얻길 원하는 경우가 있습니다.
단순히 가변 변수를 선언하고 나중에 그 가변 변수의 값을 덮어쓰기엔 불변 변수일 수 있던 변수가 가변 변수가 된다는 점에서 낭비가 있습니다.
다른 언어들은 각각 다음을 사용해 이를 달성합니다.
 - [IIFE](https://developer.mozilla.org/docs/Glossary/IIFE), JavaScript
 - [Block Expression](https://doc.rust-lang.org/reference/expressions/block-expr.html), Rust
 - [`run` function](https://kotlinlang.org/docs/scope-functions.html#run), Kotlin

이를 Derive에서 달성하기 위해, `do` 표현식을 제안합니다.
`do` 표현식은 여러 문장을 수행한 뒤 단일 표현식을 반환하기 위한 문법입니다.
`do` 표현식이 생김에 따라,
 - 함수의 `(...args): {}` 문법은 제거되고, 단일 표현식만 반환할 수 있게 됩니다.
 - `branch`를 제거하고, `match`와 `do` 표현식을 혼합해 `branch`를 대체합니다.

#### 문법
```py
do
    # statement1
    # statement2
    # ...
    # statementN
```

#### 특징
do 표현식은 다음의 특징들을 갖습니다.
 - `do` 표현식 내부에서 반환한 결과값을 `do` 표현식의 결과값으로 갖습니다.
 - *항상* 결과값이 존재하는 것이 아닌 경우 오류가 발생합니다.
    예를 들어, 다음 코드는 모든 경우에 결과값이 존재하는 것이 아니므로 오류가 발생합니다.
    ```py
    foo = do
        if something == 0:
            return 1
    ```

### `action` 표현식
하지만, `do` 표현식을 사용하면 반환 값이 없는 함수를 작성할 때 문제가 생깁니다.
'없음'을 나타내는 값(이하 `None`)을 반환하면 되지만, 일일이 `return None`과 같은 코드를 작성하는 것은 꽤 번거롭습니다.
```py
# 기존 문법 사용
print_three_times = (x): {
    print(x)
    print(x)
    print(x)
}

# do 표현식 사용
print_three_times = (x): do
    print(x)
    print(x)
    print(x)
    return None
```
이와 같은 경우를 위해 `action` 표현식을 제안합니다.

#### 문법
`action` 표현식은 다음 코드의 문법 설탕입니다.
```py
do
    # statement1
    # statement2
    # ...
    # statementN
    return None
```
이제 위에서 `do` 표현식을 이용해 작성했던 함수 `print_three_times`를 다음과 같이 재작성할 수 있습니다.
```py
print_three_times = (x): action
    print(x)
    print(x)
    print(x)
```

#### 특징
 - `action` 표현식의 결과값은 `None`이어야 합니다.
    즉, 다음 코드는 올바르지 않은 코드입니다.
    ```py
    action
        return 1
    ```