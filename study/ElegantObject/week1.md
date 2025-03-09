# 1장 - 출생(Birth)
## 객체

- 살아있는 유기체 (책에선 he로 사용될 예정)
- 자신의 자신의 `가시성 범위` 안에서 살아감

```java
if (price < 100) {
	Cash extra = new Cash(5);
	price.add(extra);
}
```

- extra 객체는 if 블록 내부가 extra 객체의 가시성 범위가 됨 (외부에서는 사용되지 않기 때문에)
- price → 객체 외부에 존재
- 숫자 5 → 객체 내부에 존재

## 1. 1 -er로 끝나는 이름을 사용하지 마라

### 객체 vs 클래스

- **클래스는 객체의 팩토리이다.**
- 클래스는 객체를 생성하며, 일반적으로 클래스가 객체를 인스턴스화 한다라고 표현

```java
class Cash {
	public Cash(int dollars) {
		//...
	}
}

Cash five = new Cash(5);
```

- 위 코드는 Factory 패턴과 다름
- 단지, Java에서 제공하는 new 연산자는 충분히 강력하지 않기 때문이다.
    - new를 통해유사한 객체라 존재하거나, 재사용 가능한 지 확인하지 않으며, new가 동작하는 방식을 변경할 수 있는 어떤 매개 변수도 존재하지 않는다.
- 즉, new 는 객체의 팩토리를 제어할 수 있는 **원시적인 도구**

<aside>
💡

그렇다면 어떻게 new 연산자보다 더 강력하고 유연하게 Factory 패턴을 사용할 수 있을까?

</aside>

```java
class Shapes {
	public Shape make(String name) {
		if (name.equals("circle")) {
			return new Circle();
		}
		
		if (name.equals("rectangle")) {
			return new Rectangel();
		}
		
		throw new IllegalArgumentException("not found");
	}
}
```

- 전형적인 Factory 패턴으로 타입 이름 기반으로 객체를 인스턴스화 함

- 클래스를 객체의 템플릿으로 설명하는 것은 완전히 잘못됐다. (붕어빵과 붕어빵틀.. 같은 것인가)
    - 단순히 필요한 시점에 어딘가에서 복사되는 수동적이고 멍청한 코드 덩어리로 격하시키기 때문
    - 클래스는 객체의 팩토리다.
        - 클래스를 객체의 능동적인 관리자로 생각해야 한다.

→ 즉, 객체를 꺼내거나 다시 돌려놓을 수 있는 장소이기에 클래스를 `저장소(storage unit)` 또는 `웨어하우스` 라고 불러야 한다.

### 클래스 이름

- 잘못된 방법: 객체들이 무엇을 하고 있는(doing) 지를 살펴본 후 기능(function)에 기반해서 이름을 짓는 것.
    
    ```java
    class CashFormatter {
    	private int dollars;
    	
    	CashFormatter(int dlr) {
    		this.dollars = dlr;
    	}
    	
    	public String format() {
    		return String.format("$ %d", this.dollars);
    	}
    }
    ```
    
    → 당연히 캐쉬 포맷의 역할을 할 것이라고 예측할 수 있음.
    
    → 절대 클래스의 이름을 객체가 `노출하고 있는 기능에` 기반하지 마라.
    
- 올바른 방법: 클래스의 이름은 무엇을 하는지(what he does) 가 아니라 `무엇인지(what he is)에 기반`해야 한다.
    
    ```java
    class Cash {
    	private int dollars;
    	
    	Cash(int dlr) {
    		this.dollars = dlr;
    	}
    	
    	public String usd() {
    		return String.format("$ %d", this.dollars);
    	}
    }
    ```
    
    - 클래스의 객체들이 무엇을 캡슐화할 것인지 관찰하고 이 요소들에 붙일 적합한 이름을 찾는 것.
    - 외부에서 목록을 이용해서 어떤 일을 해야 한다면  `객체에게 그 일을 하도록 요청`하고, 수신한 요청을 처리하기 위해 `객체 스스로 무엇을 할지`를 결정해야 한다.
        - 만약, 객체가 원하지 않는다면, 어떤 일이 일어날 지는 객체에게 달려있다.
- 여기 숨어있는 악마는 접미사 ‘`-er`’이다.
    
    <aside>
    👿
    
    Manager, Controller, Helper, Writer, Validator(’`-or`’ 역시 악마)
    
    </aside>
    
    - 객체는 객체의 외부 세계와 내부 세계를 이어주는 연결장치가 아니다.
    - 객체는 내부에 캡슐화된 데이터를 다루기 위해 요청할 수 있는 절차의 집합이 아니다.
    - 연결장치는 존중 받지 못한다. 정보를 수정하거나 스스로 어떤 일을 수행할 만큼 똑똑하지 않고, 정보만 전달할 뿐이기 때문이다.

<aside>
📝

새로운 클래스에 이름을 붙일 때는 무엇을 하는지가 아닌, `무엇인지` 생각하기

- 리스트 → 인덱스 번호를 통해 특정 위치의 요소를 선택할 수 있다.
- HTML → 브라우저에서 HTML을 렌더링 할 수 있다.
</aside>

+) 좋지 않은 클래스 이름 `Util` / `Utils` → 유틸리티 클래스이며 3.2.3에서 다뤄질 예정

## 1. 2 생성자 하나를 주 생성자로 만들어라

- `constructor(ctor)`: 새로운 객체에 대한 `진입점`
    - 몇 개의 인자들을 전달받아 이들을 이용해서 어떤 일을 수행한 후, 객체가 자신의 의무를 수행할 수 있도록 준비시킨다.

```java
class Cash {
	private int dollars;
	
	Cash(int dlr) {
		this.dollars = dlr;
	}
}
```

- 올바르게 객체 설계를 한다면, ctor의 수가 메서드의 수보다 많아질 것이다.
    - 2~3개의 메서드 / 5~10개의 ctor
- ctor의 수가 많아질 수록, 클라이언트가 클래스를 더 유연하게 사용할 수 있다.

```java
new Cash(30);
new Cash("$29.95");
new Cash(29.95d);
new Cash(29.95f);
new Cash(29.95, "USD");
```

- 위 문장들은 모두 동일한 객체를 생성한다.
    - 동일하다는 말은 생성된 객체들이 동일하게 행동한다는 것
- 반면에 function 의 수가 많아지면 초점은 흐려지며, SRP를 위반한 것
- 주 생성자 1개 / 부 생성자 여러 개 만들어라
    - 중복 코드를 방지하고 설계를 더 간결하게 만들 수 있기 때문이다.

## 1. 3 생성자에 코드를 넣지 마라

- 여러 ctor를 가진 클래스가 있다고 가정하면, 여기서의 ctor들은 완전해야 한다.
    - 완전하다? → 어떤 것도 누락되지 않으며, 중복되는 정보도 없다는 것.
- **인자에 손대지 말라**

```java
// 인자에 손 댄 경우
class Cash {
	private int dollars;
	
	Cash(String dlr) {
		this.dollars = Integer.parseInt(dlr);
	}
}
```

- 문자열 → 정수형으로 변환시키는 작업을 ctor 안에서 처리한다. → 잘못됨.
- 객체 초기화에는 `코드가 없어야 하고` 인자를 건드려서는 안 된다.

```java
class Cash {
	private Number dollars;
	
	Cash(String dlr) {
		this.dollars = new StringAsInteger(dlr);
	}
}

class StringAsInteger implements Number {
	private String source;
	
	StringAsInteger(String src) {
		this.source = src;
	}
	
	int intValue() {
		return Integer.parseInt(this.source);
	}
}
```

- 첫 번째 예제에서는 객체를 초기화 하는 시점에 텍스트를 숫자로 변환,
- 두 번째 예제에서는 Cash 클래스의 객체를 실제로 사용할 때까지 변환 작업 연기

```java
class Cash {
	private Number dollars;
	
	Cash(String dlr) { // 주생성자
		this(new StringAsInteger(dlr));
	}
	
	Cash(Number dlr) { // 부생성자
		this.dollars = dlr;
	}
}
```

- 가끔씩 필요한 클래스들을 직접 만들어야 할 때 → `의사코드`

1. 객체를 인스턴스화 하고
2. 객체가 우리를 위해 작업하도록 한다.

→ 이 두 가지는 겹쳐서는 안 된다.

### 조언을 지지하는 기술적인 이유

1. ctor에 코드가 없을 경우 실행 속도를 빠르게 만들 수 있다.

```java
class StringAsInteger implements Number {
	private String text;
	
	public StringAsInteger(String txt) {
		this.text = txt;
	}
	
	public int intValue() {
		return Integer.parseInt(this.text);
	}
}

Number num = new StringAsInteger("123");
num.intValue();
num.intValue();
```

위 코드와

```java
class StringAsInteger implements Number {
	private int num;
	
	public StringAsInteger(String txt) {
		this.num = Integer.parseInt(txt);
	}
	
	public int intValue() {
		return this.num;
	}
}
```

아래 코드를 비교하면, 아래 코드가 파싱은 객체 초기화 동안만 단 한 번 수행하기에 실제로 효율적이다.

→ 하지만, ctor 에서 직접 파싱 수행하는 두 번째 예제에서는 최적화가 불가능하다. 또한, intValue()를 호출할 필요가 없는 경우에도 CPU는 파싱을 위해 시간을 소모한다.

- 그렇다면, 파싱이 여러 번 수행되지 않기 위해서 데코레이터를 추가하면 된다. (결과를 캐싱하자)

```java
class CachedNumber implements Number {
	private Number origin;
	private Collection<Integer> cached = new ArrayList<>(1);
	
	public CachedNumber(Number num) {
		this.origin = num;
	}
	
	public int intValue() {
		if (this.cached.isEmpty()) {
			this.cached.add(this.origin.intValue());
		}
		
		return this.cached.get(0);
	}
}
```

```java
Number num = new CachedNumber(
	new StringAsInteger("123")
);
num.intValue(); // 첫 번째 파싱
num.intValue(); // 파싱 수행 X
```

- 하지만, 이렇게 되면 그냥 ctor 에서 쓰면 안 되냐?
    - 코드의 `일관성` 이라는 이유로 반대한다.
    - 또한, 나중에 리팩토링 하기 어려워 질 것이다.
2. 는 없다. 앞에서 설명한 이유로 이미 충족이 된다.
