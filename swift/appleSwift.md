# Swift (apple)

###간단한 values

상수는 let 으로, 변수는 var 를 이용해 생성한다.

```
var myVariable = 42
myVariable = 50
let myConstant = 42
```

값에 따라서 타입이 정해지긴하지만(컴파일러가 추측함) 명시하는것도 가능하다. 
```
let implicitInteger = 70
let implicitDouble = 70.0
let explicitDouble: Double = 70
```

명칭 선언뒤에 콜론(:) 으로 구분하고 타입을 정해주면 된다.

형변환은 은연중에 일어나는 형태가 아니다.변환이 필요하면 원하는 형태로 명시해야한다.


``` 
let label = "The width is "
let width = 94
let widthLabel = label + String(width)
```

문자중에 숫자를 가져 오는 더 간단한 방법은 역슬래쉬(\)뒤에 괄호를 하고 값을 넣으면된다.

```
let apples = 3
let oranges = 5
let appleSummary = "I have \(apples) apples."
let fruitSummary = "I have \(apples + oranges) pieces of fruit."
```

배열과 사전형은 [] 를 이용해서 만들고 인덱스나, 키명으로 조회한다.

참고로 사전형이란 자바로 치면 맵과 같다고 생각해도 된다. 사전처럼 단어같은 키가 있고 그 뜻에 해당하는 값이 있는 구조이다. 

```
let emptyArray = String[]()
let emptyDictionary = Dictionary<String, Float>()
```

### 분기처리

if 나 switch , for-in , for,while ,do-while 등이다. 흔하디 흔한...

if 와 let 로 값이 없을지도 모를 변수를 처리하는것이 가능하다. 
그 값은 옵션인데 값이 담겨 있거나 nil 이다. 값을 optional 로 표시하려면 값의 타입을표시하고 뒤에 ? 마크를 쓴다. 

```
var optionalString: String? = "Hello"
optionalString == nil
 
var optionalName: String? = "John Appleseed"
var greeting = "Hello!"
if let name = optionalName {
    greeting = "Hello, \(name)"
}
```

만약 optional 값이 nul 이라면 조건은 false 가 되고 코드를 뛰어넘게 된다.(skip) true 라면 optional 값은 꺼내져 let 뒤의 상수에 할당된다. 
let 은 

