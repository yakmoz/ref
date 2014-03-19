# cocoa 프로그래밍 여러책 정리

# 타입

### IBOutlet 은 아무것도 하지 않는 void??
UI 를 그린뒤 h 에 넣을때 Outlet 으로 넣으면 이 값으로 설정된다. (void 와 같은 의미?) 
이 Connection 은 action 이 아닌 UI 객체에 접근하기 위해 정의한다라고 생각해도 된다. 이 값으로 이 UI 를 가르키는 포인터가 필요한데 이 과정으로 프로퍼티가 생성됨. 여기서 name 을 정하는데 컴파일러는 `_이름` 으로 변수를 자동으로 만든다. 이건 @synthesize 를 통해서 Accessor Method 로 인해 가능해지는것 같은데 말야.

### IBAction 은 void 와 같다
UI 를 그린뒤 h 에 넣을때 Action 으로 넣으면 이 값으로 설정된다.(void 와 같은 의미?) 인터페이스 빌더에서 사용한다. 버튼을 눌러서 액션이 일어나야 할경우 Connection 을 Action 으로 하면된다. 

### id??
id 는 objective-c 에 존재하는 특별한 형이다. void* 와 유사하다고 하네. 
단, 객체에 대해서만 사용한다고 한다. objective-c 에서는 메소드 호출시 객체의 타입은 알 필요가 없단다. 단지 메소드만 있으면된다? 
즉, 어떤 함수를 호출하기 위해서 그 객체의 형을 반드시 알 필요는 없다. 

### nil 은 NULL 과 같다 
객체를 가리키는 포인터로 NULL대신 nul 을 사용한다. 완전 같은건 아닌걸로 안다. 다시 정리하겠다.

### Universal Type Identifiers(UTIs) 
파일의 타입을 구분할 수 있도록 마련된 파일의 유형확인 문자열. 어플리케이션의 info.plist 파일안에 설정함으로써 해당 어플리케이션이 어떤 UTI를 읽고 쓰는게 가능한지 알게 된다.

info.plist 는 키-밸류의 쌍 딕셔너리로 된 xml 파일이다.


# U I
### UI 파일은 XIB 파일
zib 발음

### xib vs nib
xib 는 사용자가 달루고, nib 는 컴파일된 파일로 애플리케이션에서 쓴다.

### NSControl ?
NSButton, NSSlider, NSTextView, NSColorWell은 NSControl의 서브클래스들이다.

### 계층도?
위에서 부터 아래로 서브들이다. 

- NSObject(객체임. retain,release,dealloc,init) 
	- NSResponder(이벤트제어. mouseDown:,keyDown: etc...)
		- NSView (윈도에 나타남. drawRect:, window etc...)
			- NSControl (타킷과 액션..., setTarget:, setAction etc...)
				- NSButton, NSTextField, NSSlider ... etc 
				
### 버튼의 액션을 실시간으로 바꿀 수 있다?
가능한것으로 보인다. setAction 이 가능하다. 현재 버튼에 걸린 셀렉터 도 조회가능하다.

``` objectivec
// 지정하는법 
SEL mySelector;
mySelector = @selector(drawMickey:) ;
[myButton setAction:mySelector];


//교체
SEL mySelector;
mySelector = NSSelectorFromString(@"drawMickey:");
[myButton setTarget:someObjectWithDrawMickeyMethod] ; 
[myButton setAction:mySelector];
```
### UI 객체를 고를때 ctrl 버튼을 누른채 UI 컴포넌트를 클릭하면 메뉴가 뜬다. 
여기서 delegate 나 datasource 등을 설정할 수 있다. delegate 후 메소드를 오버라이드 해서 원하는 실행을 만들 수 있...다?

## 작성순서
1. UI 를 그린다.
2. UI 에 대한 액션을 만드는 방법으로는 
    - 오른쪽 아래 콤포넌트 고르른곳에서 파란색 Object 를 골라서 xib 파일을 선택하면 보이는 메인창의 왼쪽 콤포넌트가 있는곳에 두어 ctrl or 왼쪽 마우스 버튼으로 연결하는 방법
    - 또는 Assistant Ediotr 를 선택해서 바로 소스로(즉 위의 파란색 오브젝트) 연결해주는 방법

    2가지가 있다. 
3. 우선 UI 객체들을 interface 에 넣자.  


# 문자
### 문자열  
Objective-C는 `@"..."`을 이용해 7비트 ASCII 인코딩에서 문자열 객체를 간편하게 생성할수 있다.
	
```objectivec 
NSString *temp = @"this is a constant string";
```

또는 

```objectivec
NSString *str = [NSString stringWithString:@"문자열"];
```


stringWithString 은 벼누에 문자열을 대입시키는 메소드(메소드 호출을 메세지를 보낸다는 표현을 쓰더라...)

### 문자열 변경?
NSString 은 기본적으로 값변경이 안된다. 그러면? 주소변경하는거지 뭐
즉 새로운 값을 새로운 주소에 선언하고 그 주소를 대입해버리는 것이다. 

### 문자열 에 문자열추가?
NSString 의 stringByAppendingString 을 이용하면 된다. 
``` objectivec
NSString *s1 = @"ABC";
NSString *s2 = [s1 stringByAppendingString:@"DEF"];
```
### 문자열 비교
isEqualString
``` objectivec
NSString *s1 = @"ABC";
NSString *s2 = @"ABC";
BOOL c = [s1 isEqualToString: s2];
```


### 문자열 추출
subStringToIndex:4		? 처음부터 4문자 추출
subStrinfFromIndex:10	? 11번째 부터 마지막까지 
subStringWithRange:NSMakeRange(5,4)	? 

### sprintf 가 존재한다.
NSString 의 initWithFormat 이 바로 그것이다.
``` objectivec
//예제 
NSString* cntStr = [[NSString alloc] initWithFormat:@"%d",cnt];

//위보다는
NSString *text = [NSString stringWithFormat:@"'%@' has %d characters.",string,[string length]];
```
	
### 배열?
``` objectivec
// 사실상 하나짜리 배열
NSArry *arr = [NSArray arrayWithObject:@"ABC"];

// n 개... 끝을 반드시 nil 로 직접 지정해야하는건가?
NSArry *arr = [NSArray arrayWithObject:@"ABC",@"DEF",@"GHI",nil];
```
	
# 할당, 선언 및 호출

### 클래스 with 변수
클래스가 인스턴스 변수를 가지는데 그건 @interface 선언하고 {} 안에 정의해주어라.

### objective-c 의 클래스
인터페이스와 구현으로 나뉘어져 있다.
@interface 를 .h 에 두고 그걸 import 하여 @implements 하는 .m 으로 구성된다.
.h / .m 파일의 끝을 @end 로 끝낸다. 

### 생성과 초기화
기본적으로 공간을 만들고, 객체를 만든뒤, 초기화해서 쓴다.
``` objectivec
NSString *str; // obj-c 에서는 객체는 무조건 포인터 선언
str = [NSString alloc];	// 객체생성
str = [str init];		// 초기화 

// 위의 3가지 과정을 보통 한꺼번에
NSString *str2 = [[NSString alloc] init];
```
	
그냥 init 이 아니라 initWith... 등으로 된 것을 지정초기화 라고 하네?
자바로 치면 기본생성자외의 생성자이다. 
``` objectivec
self = [super init]; // 지정생성자 안에서 기본생성자를 호출해야 한다. 
if(self) {....}		// 그리고 이 짓을 보통 하더라. 제대로 초기화 됐는지 확인한다.
```


### setter/getter
objective-c 를 공부해보면 알겠지만 변수에 대해 setXxx 하지만 getXxx는 하지 않는다. 그냥 xxx 로 getter 이름을 정의한다. 사실 이렇게 하지 않으면 안되는게 **키-밸류** 코딩에서는 setXxx, xxx 를 setter/getter 로 자동처리하므로 그렇게 지켜서 메소드 정의를 해야한다!!!!

**참고로 이 작업을 property 와 synthesize 를 이용해 자동처리할 수 있다!!**

### property ?
오브젝트 안의 데이터를 참조 및 대입하기 위한 메소드이다. 인터페이서에 선언한다.

@property (속성) 형 이름;
```objectivec
ex) 
@property (readwrite) int num;
```
	
속성에는 여러가지 값이 있으니 찾아보기바란다. 필요 없으면 생략 가능하다.

- assign : 기본갑시. 단순히 대입만한다. 보통 정수형,실수형같은 스칼라형에만 사용함. (포인터형 아닌)
- strong : 강한 참조를 의미함. 포인터 값이 설정되어 있는 동안 메모리에서 해제되는것을 방지.ARC 에서 사용함. ARC 를 사용하지 않는다면 retain 속성과 동일하게 동작
- weak : 약한참조. assign 과 비슷하지만,메모리에서 해제시 nil 로 설정된다는 점이다르다. ARC 에서만 지원. 
- copy : 새로운 값을 복사하고 변수에 복사한 값을 대입한다. 

속성은 nonatomic 설정 가능하다고한다... 더 찾아보도록 


### Accessor method 접근자 메소드 (액세서 메소드)
프로퍼티값을 참조 및 설정하려면 `액세서메소드` 가 필요하다. 직접 코딩할 수 있지만 `@synthesize` 를 implementation 에 추가하여 자동생성 가능하다.

``` objectivec
@synthesize num; // num 의 액세스 메소드가 생성된다. 
```

자동이 아니면 속성부분에 getter / setter 로 메소드명 지정이 가능하다만...
전에 언급했지만 getter 의 경우 실제 이름은 ...getter 로 하지말고 그냥 그 메소드 이름으로 getter 를 만들라. 
	
변수에 접근하는 방법은 `.` 을 사용한다. 위의 경우 `@synthesize num` 을 했으므로, `.num` 접근 가능하다. 물론 `@property` , `@synthesize` 등으로 추가된  대상의 경우이다. 

**주의할점은 id 형의 경우는 `.` 참조가 안됨.**

### 그럼.. 즉 property(.h) 와 synthesize(.m) 는 한쌍 처럼 움직일 수 있다?
분위기가 그러한데? property 로 참조 및 대입하기 위한 메소드를 .h에 선언하고 그걸 자동화하는 synthesize 를 .m 에 선언한다.


### 카테고리
클래스명을 동일하게 하고 인터페이스부에 `()`로 안에 카테고리 명을 넣어서 구분하게 된다.

	
### 클래스확장?
() 안에 카테고리를 지정하지 않는 방법이다. 클래스 확장을 하면 안팎에서 프로퍼티의 속성을 바꿀 수 있다. 
	

### .h 파일의 역할은? 
인스턴스 변수와 메서드들이 추가된다?

- outlet(아웃렛) : 다른객체들을 가르키는 인스턴스 변수
- action : 사용자 인터페이스 객체가 호출하는 메소드들

### extends 는 : 이다 
super 는 : 뒤에 옴. 물론 선언에서 얘기다.

### 파라미터라는 의미도 : 이다.
아래에 보면 메소드 호출방법에 대해서 나오는데 받는 파라미터를 `메소드명:파라미터명` 으로 처리한다. 

### 클래스 선언이 @interface 로 시작한다
@ 심벌은 c 코드와 objective-c 코드가 충돌하는 것을 최소화 하고자 objective-c 키워드 앞에 붙인것이다. 

### 메소드 선언이 색다르다.
 `- (리턴타입)메소드명:파라미터 {...}`  
 
 아래코드는 c++ or java 코드이다.
``` objectivec
public void increment(Object sender) 
{ 
    count++;
    textField.setIntValue(count);
}
```
		
이걸 objective-c 로 구현하면?
``` objectivec
- (void)increment:(id)sender 
{
    count++;
    [textField setIntValue:count];
}
```

메소드는 모두 공개 메소드이고, 인스턴스 변수는 전부 보호된다.
인스턴스 변수는 중골호 안에 선언되어 있다. 

### 메소드 .. 메시지??
obj-c 는 정의한 메소드를 직접 호출하여 실행하는것이 아니라 클래스 오브젝트에게 메시지를 보내서 실행시킨다고 한다.
대부분의 경우 메시지명과 똑같은 이름의 메소드를 호출한다.

### 인터페이스는 의 끝은?
@end 로 끝난다. 

### 메소드에 +, - ??
- `-` : 인스턴스에서 사용되는 메소드
- `+` : 클래스에서 사용되는 메소드. 즉 인스턴스가 아니라 그 클래스와 연관된다. 보통 static 으로 클래스필드임을 암시해준다. 해서 인스턴스 생성없이 호출 가능하다. 즉 자바의 static 이라고 생각해도 된다. 
	
### 메소드 선언 

	// 리턴타입은 void , int 등... 원하는 리턴타입이 적히면 된다. 
	-(리턴타입) 메소드명:(인수타입) 인수이름;   	

	// 여러개이면?
	-(리턴타입) 메소드명:(인수타입) 인수이름 라벨명:(인수타입) 인수이름;

라벨명이란? 거기에 값을 적용해주기위해 호출할때 그 라벨명을 쓰게 된다. `:라벨명` 
	
### 메소드 호출은 [  ]
바로 위에 예제도 있지만 `textField.setlntValue(count);` 를 

objective-C 로 바꾸면 `[textField setIntValue:count]` 이다. 

인수가 여러개 이면, 한칸 띄우고 `라벨명:값` 이런식으로 계속 추가하면된다. 한칸 띄우는거다.
라벨명은 선언시에 정해진다.
``` objectivec
// test 는 인수값, encoding 은 라벨: 뒤의 값은 인수값
NSString *str = [NSString stringWithString:test encoding:NSUTF8StringEncoding];
```
	
#### <u>**인수의 수와 메서드명의 ':' 의 수는 같개 된다네.**</u>



### 오브젝트가 자기 자신에 선언되어있는 메소드 호출할때?
[self 메소드명];


### .h 는 .m 에서 구현하더라
``` objectivec
#import <Foundation/Foundation.h>
@interface RandomController : NSObject 
{ 
    // 이 안은 인터페이스 변수부분. 메소드에서 바로 접근 가능 
    IBOutlet NSTextField *textField; 
}
-(IBAction)seed:(id)sender;
-(IBAction)generate:(id)sender; 
@end
```
	
이와 같이 선언하고  .m 에서

``` objectivec
import "RandomController.h" 

@implementation RandomController

- (IBAction)generate: (id)sender 
{
    //1부터 100 사이의 수 생성
    int generated;
    generated = (int)(random() % 100) + 1;

    NSLog(@"generated = %d", generated);
    // 텍스트 펼드에 현재 출력 중인 것을 바꾸라고 요청
    [textField setlntValue:generated];
}

- (IBAction)seed:(id)sender
{
    // 시간을 초기값으로 난수 발생 
    [textField setStringValue:@"Generator seeded"];
}
@end
```

뭐.. 소스내용을 이해하지 못해도 상관없다. interface 를 implementation 하는 형태를 봐둬라. 

### 클래스의 인스턴스 변수 범위
다른 언어처럼 당연히 범위가 존재한다. private,public,protected,package 등...
참고로 public 으로 선언하여 오브젝트 밖에서도 접근할 수 있다면 -> 로 접근한다.
왜냐구? 어차피 모든 오브젝트는 * 선언을 하게 되고 , 알다시피 포인터 변수에서 멤버에 접근할때는
`.` 가 아니라 `->` 를 쓴다.

슈퍼클래스에서... 

- public : 서브 클래스에서도 자기 클래스의 인스턴스 변수처럼 사용할 수 있음
- private : 서브 클래스에서 액세스 할 수 없음
- protected : 서브 클래스에서 액세스할 수 있지만, 다른 오브젝트에서는 안됨 
 

### init?
[object init]은 객체 안에 있는 여러 변수들의 값을 초기화하는 생성자 호출이다. 이 메소드는 [Fraction alloc]에 의해서 반환된 인스턴스가 호출한다. 보통 이 과정 은 다음과 같은 형태로 한 줄로 이뤄진다. `Object* var = [[Object alloc]init];`

오브젝티브-C에서의 생성자는 단순한 "init" 메소드이다. C++이나 자바에서처럼 특별 한 생성자가 있는 것은 아니다.

### 인자대입
`[frac setNumerator: 1]` 은 간단하다. frac객체의 setNumerator 함수를 호출하면서 인 자 1을 전달한다.

### if(self) ???
if(self) 라고 쓰는 것은 if ( self != nil ) 와 동일하다. 부모 클래스의 생성자로부터 성공적으로 새 객체를 생성해서 받아왔다는 것을 보장하기 위해서 사용한 것이다. 

값들을 초기화 한 후에는 return self;를 통해서 자기 자신의 객체를 반환해야 한다.

### delegate (델리게이트)
프로토콜 처럼 다른 오브젝트에게 처리를 맡기는 것을 델리게이트 라고 한다. (프로토콜은 다른 오브젝트에서의 호출에 따라 자신이 만든 메서드에서 독자적인 처리를 수행하는 경우에 시용됨)

은... 닥치고 단어상의 의미처럼 대리함을 의미한다. 즉 어떤 행위에 추가적 처리를 하고 싶을때 해당 클래스에 delegate 프로토콜 선언 해주면 컴파일러가 이걸 보고 추가 실행에 대한.. 걸 알게 되는???


## 프로토콜(protocol)
클래스에 메소드나 프로퍼티의 처리를 강제하는 장치?. 말이 좀 애매한데 간단히 말해서 프로토콜이란건 여러행위를 묶어놓은거라고 보면된다. 해서 **인터페이스의 일부**를 잘라내어 모아놓은 목록이라고 표현하더라.

프로토콜을 정의하려면 @protocol 과 @end 사이에 메소드나 프로퍼티를 기술한다. 인터페이스와 같다.(= 형식프로토콜 이라 함)
``` objectivec
@protocol FruitsInfo
@required 		// 이 이후에 있는 메소드등을 처리할 필요가 있음 
-(void) showBrand;
@optional		// 이 이후의 메소드는 반드시 처리할 대상은 아님 
-(void) showCalorie;
@end  
```

프로토콜을 적용하는 방법은 인터페이스 선언부에 `<프로토콜명>` 를 기술하면된다.
```objectivec
@interface Orange: NSObject <FruitsInfo>
{
....
}
@end 
```

이렇게 선언된것을 구현할때 `@required` 로 마킹된것은 구현해야한다. (아니면 경고 나옴 )

> 자바의 **인터페이스격**으로 오브젝티브시에서 쓰이는것이 바로 프로토콜이다. 프로토콜은 메소드 선언의 목록이다. 프로토콜을 구현한 클래스를 생성하려면 그 클래스는 프로토콜에 선언된 메소드를 모두 구현해야 한다.


## 바인딩(Binding)
많은 그래픽 오브젝트는 바인딩되어있다. 특정키(키-벨류의 키)에 바인드를 하면 그래픽 오브젝트의 속성 즉, 그 값이나 폰트색상,뷰 등이 자동적으로 동기화 될 것이다. 
  
바인딩을 쓸 경우 프로토콜을 구현하지 않아도 될때가 있다. 

## Notification 노티피케이션 
자.. 슬라이더와 변수를 옵저빙해놓으면 슬라이더의 변화에 따라 변수의 표시값도 잘 변한다. 허나 버튼에 값변화 바인딩을 해놓으면 버튼을 클릭한다고 해서 변수의 화면 표시값은 변하지 않는다. 이를때 직접변경하기 위해서는 노티피케이션(notification) 을 직접 발생시켜야 한다.

시작과 끝 두번호출하면되며,
``` objectivec
[self willChangeValueForKey:@"fido"];	// noti start
fido++;
NSLog(@"fido is now %d", fido);
[self didChangeValueForKey:@"fido");	// noti end
```

실행중인 어플리케이션에는 모두 NSNotificationCenter 의 인스턴스가 있단다. 특정 내용에(notification) 관심이 있다고 등록함 등록객체를 옵저버라고 함. 다른 객체들이 노티피케이션 센터에 노티페케이션을 알림. 그러면 관심있다고 등록한 객체에 해당 내용을 전달함.

간단히, 노티피케이션 센터에 노티피케이션을 보낸다. 그 노티피케이션을 받아서 처리를 하게 된다.

### observer
오브젝트는 관심있어하는 노티피케이션을 등록한다. 이렇게 등록된것(오브젝트)를 observer 라고 부른다. 

### NSNotification
노티피케이션은 옵저버가 필요로 하는 정보를 넣은 봉투와 같다. 노티피케이션에는 두 가지 중요한 인스턴스 변수인 name과 object가 있다. 

### NSNotificationCenter
NSNotificationCenter는 명령을 내 리는 두뇌와 같다. NSNotificationCenter는 옵저버 객체를 등록하거나 노티피케이션을 알리고 옵저버 등록을 해제하는 일을 한다.
``` objectivec
(void) addObserver: (id)anObserver 
          selector: (SEL)aSelector
              name: (NSString *)notificationName 
            object: (id)anObject
```

이 메소드의 의미는
1. notificationName 이라는 이름, anObject 라는 객체 로 이루어진 노티피케이션을 받을 대상 anObserver를 노티피케이션 센터에 등록한다.
2. 노티피케이션센터에 1로 정의한 노티피케이션이 오면 이 노티피케이션을 인수로 하는 aSelector 메시지를 anObserver 에 보낸다.
3. 만약 notificationName이 nil이면 노티피케이션 센터는 옵저버에게 anObejct에 해당하는 객체에게 모든 노티피케이션을 보낸다. 
4. 만약 anObject가 nil이변 노티피케이션 센터는 옵저버 에게 notificationName 이란 이름을 지닌 모든 노티피케이션을 보낸다.

`postNotification` 혹은 `postNotificationName` 메소드로 노티피케이션을 노티피케이션 센터에 알린다. 자세한건 api 참조바람. 지우는건 `removeObsever`로 지운다.

### observer 등록
이전에 센터에 등록하던 양식에 맞춰 호출한다. 아래는 예 이다.
``` objectivec
-(id)init
{
	self = [super init]; 
	if (self) {
		employees = [[NSMutableArray alloc] init];
		NSNotificationCenter *nc = [NSNotificationCenter defaultCenter];
		[nc addObserver:self 
			selector:@selector(handleColorChange:)
			name:BNRColorChangedNotification 
			object: nil] ;
		NSLog(@"Registered with notification center") ; 
	}
	return self;
}

// 아래는 삭제 
(void)dealloc {
	[[NSNotificationCenter defaultCenter] removeObserver:self];
}
```

위의 로직에 따르면 handleColorChange 를 호출하게 되어있다.

``` objectivec
-(void)handleColorChange:(NSNotification *)note {
NSLog(@"Received notification:%@", note);
}

```

### userInfo dictionary
노티피케이션을 알릴때 더 알리고 싶은게 있다면 userInfo Dictionary 를 이용하면된다. 모든 노티피메이션은 userInfo 라는 변수를 가지고 있다.


## 키-밸류
간단히 맵처럼 변수값을 변수의 이름을 이용해 읽고 변경하는 방법.
키에 값이 매핑되어있는 케이스. 근데 여기서는 보통 값을 먼저 말하고 forKey 형태로 키를 대입하더라.
``` objectivec
@interface Student NSObject {
    NSString *firstName;
}
...
@ends

// set
Student *s = [[Student alloc] init];
[s setValue:@"Larry" forKey:@"firstName"];

// get
NSString *x = [s valueForKey:@"firstName"];
```

setValue: forKey: 와 valueForKey: 는 NSObject 에 정의되어있다. 즉 ... 모든 객체가 다 가지고 있게 되는건가???



## 키-밸류 옵저빙 (key value observing)
변수의 값이 바뀌었을 때를 이것을 알리는 것. 사실 특별한 api 등이 존재하는건 아니고 설정을 통해서 (즉 액션후 호출등에 의한) 값이 동기화 되는 형태로 보인다.

### .h 임포트 대신 @class
소스에 `@class 클래스이름` 으로 적으면 해당 클래스가 있음을 알리는 역할을 한다. 그러면 임포트 없이 선언할 수 있다. import 는 컴파일러가 더 많은 파일을 분석하도록 요청하기 때문에 @class 가 빌드시 속도가 좀 더 빠를 수 있다네?


## NSBundle


## NSDictionary , NSMutableDictionary
뭐 이미 이름에서 풍기겠지만 mutable 은 변경가능하고 그냥 dictionary 는 변경 불가이다.

Dictionary 는 hash테이블로 구현되어있다. 심지어 for-in 은 바로 딕셔너리에서 키를 꺼내서 루프를 돌 수도 있다. 

NSMutableDictionary 에 같은 키로 새로 값을 넣으면 이전 값을 release 해버리고 retain 후 넣는다. 

# 언어비교
###  자바와 objective-c 비교
java 버전
```java
import com.megacorp.Bar;
import com.megacorp.Baz;

public class Rex extends Bar implements Baz { 
//... 메서드와인스턴스변수들..
}
```

objective-c version
``` objectivec
public void increment(Object sender) 
{ 
    count++;
    textField.setlntValue(count);
}
```
### 자바와 마찬가지로 == , isEqual 은 다르더라
`==` 는 포인터의 비교가 되는거고(객체), `isEqual` 은 그 값의 비교가 되더라.


### 오브젝티브-C 에서는 반드시 객체는 포인터를 통해서 다뤄야 한다.


#메모리
간단히 말하자면 ARC 형태로 작성하라. 

### Objective-C Automatic Reference Counting
scala 였던가? 참고 카운팅을 기록해두었다가 0이 되면 해제 처리. 
이와 비슷한 형태로 보인다. 이를 off 해두는 경우 메모리 낭비나 위험요소등에 주의해야 한다.

### 자원 해제 형태
사실상 2가지이다. 하나는 다른 언어에서도 사용하는 참조카운트에 관련된 2가지와 나머지 하나는 가비지 컬렉터에 의한 해두는제이다. 

허나 malloc등으로 직접 할당한 메모리는 특별히 처리(???) 하지 않는한 관리되지 못한다고 하네?

그러고보니.. 해서 라이언(10.7) 과 ios 5 를 기준으로 지원하니 안하니 등의 말이 나왔던거였다; 참조카운트 ARC(automatic reference counting) 가 10.7/iOS 5 에 소개된거라고 한다. 

의외로 가비지 컬렉터는 10.5 에서 소기된거라고 하네?

- 가비지 컬렉터 : 대가를 치뤄야 한다. 비용이 많이듬. 실시간 처리가 필요한 곳에서는 딜레이를 일으킬 수 있다.

- ARC : 최상의 방법일 수 있으나 참조 수 순환 문제가 있고, 직접 할당한 메모리는 관리하지 않는다. 

위에서 말한 할당의 2가지 유형
``` objectivec
// auto release 대상이 됨
NSString *str2 = [NSString stringWithString: @"string managed by the pool"];
// 직접 메모리에 할당해서 만든 함수 
NSString *str3 = [[NSString alloc] initWithString: @"self managed string"];
```
	
**alloc 으로 직접 할당한 메모리는 역시 직접 해제해야 한다.**

### 메모리 관리 형태 설정위치
#### Xcode
1. 왼쪽의 프로젝트 선택
2. 본창에 프로젝트 빌드 설정(Build Settings)이 뜬다. 선택
3. Objective C 부분에 ARC(Automatic Reference Counting) 설정을 NO 로 해두면 ARC 를 끄는것이다. ON 해두면 별도의 release 가 필요없다. 


### 강한 참조의 필요성
하나의 객체(A)가 있고 그 안에 메모리할당된 또다른 객체(B)가 있다고 치자.
A 는 과연 B 의 객체가 메모리에서 release 되지 않을거라는걸 보장해줄 수 있는가?
못한다. 해서 이 둘을 강한참조로 묶을 필요도 있다.

바로 객체를 만들어서 retain 하여 B 에 대입하는것이다.  

상황에 따라 약한 참조가 더 편리할 수 있다. 약한 참조는 참조순환을 피하기 위해서 사용된다. 예로 보통 부모가 그 자식을 리테인하지만 자식이 부모를 리테인 하진 않을 것이다.

### 접근자 메소드의 메모리 처리 
Accessor Method 라고 부르고... 사실상 자바에서 getter/setter 를 의미한다고 봐도 무방하다. 즉, 객체 내부의 변수에 직접 접근하지 못하므로 이를 통해서 접근하는 것이다. 단! getter 의 경우 getXxx 처럼은 하지 않기를 권하더라. objective-c 계열에서는 그냥 이름으로 적어서 get 을 구현한다. set 에는 set 을 붙이더라. 

만약 set 을 하게 된다면... 그리고 그 값이 객체라면? 리테인과 릴리즈를 주의해야한다.
``` objectivec
// 방법 1. 만약 리테인,릴리즈 순서에 주의한다. x,foo 가 같은 객체를
// 가르키는 포인터라면 0이 되어 메모리에서 해제되기 때문이다.
-(void)setFoo:(NSDate *)x
{
    [x retain];
    [foo release];
    foo = x;
}


// 방법 2. 변경하기 전에 확인한다? 즉 다른값일때만 변경한다.
void)setFoo:(NSDate *)x
{
    if (foo != x) {
        [foo release] ; // 왜 릴리즈를 먼저 하지 -ㅅ-?
        foo = [x retain];
    }
}


// 방법3. 이전값을 오토릴리즈 한다.
- (void)setFoo:(NSDate *)x 
{
    [foo autorelease];
    foo = [x retain];
}
```

1,2 번을 추천하네? 3번의 경우 성능저하도 있고 버그추적이 어렵다고 한다. 

### 참조순환
A ,B 둘이 서로를 리테인하여 절대 해제 되지 못하는 악순환을 의미함 

### ARC 참조2가지
- 강한참조 : 기본적으로 참조는 강한 참조이다. (직접 retain 등을 하는것이지?) 
해당 참조가 새로운 값으로 변경되면 이전 객체는 릴리즈 되고 새로운 객체가 리테인된다.
해서 고민없이 다음과 같이 작성하면된다.
	``` objectivec
    - (void)setEntryDate:(NSDate *)date {
        entryDate = date;
    }
    ```
- 약한참조 : 직접 참조수를 관리하는 포인터와 비슷하다. 약한 참조는 리테인 하지 않고 메모리에 있는 포인터의 값만 변경한다. 단, 이렇게 되면 해당 참조대상이 릴리즈되어 해제 되면 쓰레기 포인터가 발생되게 된다.(실제로는 참조할게 없는) ARC 는 약한 참조들이 가리키는 객체가 메모리에서 해제될때 약한 참조들을 nil 로 자동 설정해서 이를 피한다.

약한 참조는 참조순화을 피하기 위해서 사용한다. 
``` objectivec
@interface Person : NSObject {
    Person *parent; / / 이런! 이는강한참조순환을발생 시킴! NSMutableArray *children;
} @end
```
	
기본설정이 강한참조이기 때문에 강한참조가 일어난다고 한다.
```objectivec
@interface Person : NSObject {
    __weak Person *parent; // 좋군!강한참조순환이 없다 
    NSMutableArray *children;
} @end
```
	
이런 형태는 자주 쓰이는 패턴이라고 한다.
이 패턴은 Objective-C에서 자주 시용된다. 부모-자식 관계는 강한 참조인 반면, 자식-부모 관계는 약한 참조다.
ARC로 컴파일한 클래스들만이 약한 참조들로 이 클래스들을 할당할 수 있다. 만약 __weak 변수로 약한 참조를 지원하지 않는 클래스를 할당하려고 하면 예외가 발생할 것이다. 


### ARC로의 손쉬운 변환
Xcode는 기존에 작업하던 프로젝트를 ARC로 변환하는 도구를 제공한다. 이 도구는 Edit 메뉴 아래 `Refactor ->Convert to Objective-C Automatic Reference Counting` 에서 찾을 수 있다.
유의할 점은 _**ARC 코드는 Mac OS X 10.6과 iOS4에서 동작하지만 약한 참조는 해당 플랫폼 들을지원하지않는다.**_

### 메모리 풀 이용
메모리의 자동해제 풀이다. (ARC 는 아님)
``` objectivec
NSAutoreleasePool *pool = [[NSAutoreleasePool alloc]init];
...
[pool release];
return 0;
```

### 자동 참조 카운팅 ARC 
NSAutoreleasePool 을 쓰지 않는다. @autoreleaseppol 을 쓴다. 
사용하는데 몇가지 규칙이 있다.
```objectivec
int main(int argc,const char* argv[]) {
    @autoreleasepool
    {
        @autoreleasepool 
        {
            .... // 이중 선언도 가능함 
        }
        ....
    }
}
```

- 강한참조 약한참조
	- 보통의 객체 생성/참조 는 강한참조임. 자동카운팅이 안됨. 강한 참조를 나타내는 경우에 변수앞ㅁ에 __strong 수식자를 붙임. 기본이므로 안붙여도 된다.
	- `__week` 를 붙여서 만들고, 불필요하다고 판단되면 자동으로 카운팅되어 release 된다. 
	- ARC 는 NSAutoreleasePool 을 사용하지 못함
	- alloc/init 할 수 있지만 retain,release , autorelease,retainCount 호출안됨
	- dealloc 호출 안됨(오버라이딩호 [super dealloc] 안됨 

### 가비지 콜렉션 
간단히 말해 deprecated 되었다. 후에 자바처럼 고민안하고 쓸 수 있는 버전이 나올려나?
사실 힘들지도 모름. 왜냐면 어차피 c 기반인지라, 자바와는 비교하기가 좀 그러함. 

# Foundation Framework 클래스 
C++ 의 STL 과 유사하단다. 자료구조,네트워킹,스레드등 여러가지 라이브러리가 포함되어 있단다.

### 배열 
- NSArray(상수형) 와 NSMutableArray(가변형) 두종류가 있다.
- nil 은 끝을 의미 (0 이고 false 의 의미라네?)


### NSDictionary
키 기반 데이터 저장소. 
주의할점은 `키:값` 이 아니라 `값:키` 이다. 
중요하다고 생각하는 Dictionary 만 기록해놨다. 나머지는 직접 책에서 봐라. 
``` objectivec
NSDictionary *dic;		
dic  = [NSDictionary dictionaryWithObjectsAndKeys:@"apple"	,@"A"
                                                , @"banana"	,@"B"
                                                , @"cherry" ,@"C"
                                                , nil];
// 찾을때
NSString *str = [dic objectForKey:@"B"];	

// 넣을때 
[dic setObject:@"ABCDEFG" forKey:@"A"];
```

### 래퍼클래스

- NSNumber
	``` objectivec
    NSNumber *num;
    int n = 123; 
    num = [NSNumber numberWithInt:nJ;
    float f =[num floatValue];
    ```
		
- NSValue 
	NSNumber 클래스의 부모 클래스로 구조체도 저장할 수 있다. 
	
	
# undo...
이거 ... 생각보다 복잡하네 (이해만 하면 되는 내용인것 같긴한데, 뭔가 서로 맵핑하고 물리는게 많다)

#아카이브 (archive)
바이트의 스트림(stream of bytes) 같은 오브젝트의 집합(graph of objects) 을 아카이브라고 한다. 
(아하... 이게 자바로 치면 serialization .. 직렬화네)

반대의 과정이 언아카이브이다(unarchive).

어플리케이션은 시작시 NIB 파일에서 객체들을 언아카이브 한다. 
객체들은 인스턴스 변수들과 메소드들을 다 가지고 있는데, 인스턴스 변수와 클래스이름만 아카이브 한다네?

NSCoder 는 바이트 스트림을 추상화한 것이다. 그리고 추상 클래스이다.
 



