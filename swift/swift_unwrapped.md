
from [http://stackoverflow.com/questions/24034483/what-is-an-unwrapped-value-in-swift](http://stackoverflow.com/questions/24034483/what-is-an-unwrapped-value-in-swift)

우선 Optional type 이 뭔지에 대한 이해가 필요하다. 옵셔널 타입은 기본적으로 값이 nil 이 될 수 있다는것을 의미한다.

``` swift
var canBeNil : Int? = 4 
canBeNil = nil
```

? 표가 canBeNil 이 nil 이 될수 있음을 의미한다.

다음은 동작하지 않는다.

``` swift
var cantBeNil : Int = 4
cantBeNil = nil	// 할 수 없다.
```

만약 옵셔널 변수로 부터 값을 얻기 위해서는 그것을 unwrap 해야한다. 느낌표를 끝에 두면된다.

``` swift
var canBeNil : Int? = 4
println(canBeNil!)
```

이것도 가능하다.

``` swift
let optionalSquare : Square? = Square(sideLength: 2.5,name: "optional square")
let sideLength = optionalSquare!.sideLength
```

자동으로 unwrap 하기위해서 ? 대신 ! 마크를 이용해서 옵셔널을 선언할 수 도 있다.

``` swift
var canBeNil : Int! = 3
print(canBeNil)	// unwrap 이 필요 없다. 
```

다른형태로 소스를 수정해보면...

``` swift
let optionalSquare : Square! = Square(sideLength:2.5, name: "optional square")
let sideLength = optionalSquare.sideLength
```

