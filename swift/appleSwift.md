# Swift (apple)


## A Swift Tour


### 간단한 values

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


### Functions and Closures


### Objects and classes


#### second name 
기본적으로는 메소드의 파라미터들은 호출할때나 메소드 내부에서 같은 이름을 가진다. 내부에서 사용할때는 두번째 이름을 정의할 수 도 있다. 

아래의 `times` 가 바로 second name , 즉 또다른 두번째 이름이다. 
``` 
class Counter {
    var count: Int = 0
    func incrementBy(amount: Int, numberOfTimes times: Int) {
        count += amount * times
    }
}
var counter = Counter()
counter.incrementBy(2, numberOfTimes: 7)
```

클래스의 메소드나 프로퍼티,subscripting 등의 옵셔널값을 줄때는 ? 를 쓸 수 있다.

> subscripts? 컬렉션, 리스트나 시퀀스의 멤버원소에 접근하기위한 shortcuts


### Enumerations and Structures
열거형을 만들기 위해서는 enum 을 생성한다. 클래스나 모든 다른 타입 열거형은 메소드와 함께 조합할 수 있다.

```
// 이거 열거형이다. case 로 선언된 순서대로 1,2,3,4... 가 매겨진다. 해서 Ace가 1 이고 다음 순서대로 번호를 대입하므로 ten 은 10이 된다. 첫번째 값만 정의하면 된다. 
enum Rank: Int {
    case Ace = 1
    case Two,Three,Four,Five,Six,Seven,Eight,Nine,Ten
    case Jack,Queen, King
    func simpleDescription() -> String {
        switch self {
        case .Ace:
            return "ace"
        case .Jack:
            return "jack"
        case .Queen:
            return "queen"
        case .King:
            return "king"
        default:
            return String(self.toRaw())
        }
    }
}

let ace = Rank.Ace
let aceRawValue = ace.toRaw()

let two = Rank.Two
let twoRawValue = two.toRaw()

let nine = Rank.Nine
let nineRawValue = nine.toRaw()

let queen = Rank.Queen
queen.toRaw()
queen.simpleDescription()

two.simpleDescription()
```
toRaw , fromRaw 함수로 서로간의 컨버팅이 가능하다. 

```
// raw type 없이 선언한 경우. 해서 toRaw 등은 안된다.
enum Suit {
    case Spades, Hearts, Diamonds, Clubs
    func simpleDescription() -> String {
        switch self {
        case .Spades:
            return "spades"
        case .Hearts:
            return "hearts"
        case .Diamonds:
            return "diamonds"
        case .Clubs:
            return "clubs"
        }
    }
}
```

#### struct / class
struct 은 코드로값을 복제해서넘겨주고, class 는 참조로 넘겨준다.

```
struct Card {
    var rank:Rank
    var suit:Suit
    
    func simpleDescription() -> String {
        return "the \(rank.simpleDescription()) of \(suit.simpleDescription())"
    }
}

let threeOfSpades = Card(rank: .Three, suit: .Spades)
let threeOfSpadesDescription = threeOfSpades.simpleDescription()
```

열거형 멤버의 인스턴스는 인스턴스와 연결된 값을 가질 수 있다. 같은 열거형 멤버의 인스턴스는 다른 값으로 연결될 수 있다. 인스턴스를 생성할때 연결된 값을 준다. 열거형 멤버의 생(raw)은 모든 자신의 인스턴와 같고, 열거형을 정의할때 생(raw) 값을 제공해야 한다.

예제로 일출과 일볼 시간을 요청받는 서버를 고려해보자. 서버는 정보를 응답하거나, 몇몇 에러정보를 응답한다.

```
enum ServerResponse {
    case Result(String,String)
    case Error(String)
}

let success = ServerResponse.Result("6:00 am","8:09 pm")
let failure = ServerResponse.Error("Out of cheese.")

switch success {
case let .Result(sunrise, sunset):
    let serverResponse = "Sunrise is at \(sunrise) and sunset is at \(sunset)."
    
case let .Error(error):
    let serverResponse = "Failure... \(error)"
    
}
```
### Protocols and Extensiions









