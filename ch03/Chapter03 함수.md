# Chapter03. 함수

> [@goo314](https://github.com/goo314), 2022-07-09

## 작게 만들어라

- 함수 코드 길이가 짧을 수록 좋다.
- _블록과 들여쓰기_ : if/else문 while문에 들어가는 블록은 한 줄이어야 한다.

  ```tsx
  function renderPageWithSetupsAndTeardowns: string (pageData: PageData, inSuite: boolean) {
      if(isTestPage(perPage))
        includeSetupAndTeardownPage(pageData, isSuite);
      return pageData.getHtml;
  }
  ```

## 한 가지만 해라

- 함수는 한 가지를 해야 한다. 그 한 가지를 잘해야 한다. 그 한 가지만을 해야한다.
- 지정된 함수 이름 아래에서 추상화 수준이 하나인 단계만 수행한다면 그 함수는 한가지 작업만 한다.
- 의미있는 이름으로 다른 함수를 추출할 수 있다면 그 함수는 여러 작업을 하는 셈이다.
- _함수 내 섹션_ : 한 가지 작업만 하는 함수는 섹션으로 나누기 어렵다.

## 함수 당 추상화 수준은 하나로!

- 함수가 확실히 한 가지 작업만 하려면 함수 내 모든 문장의 추상화 수준이 동일해야 한다.
  - 추상화 수준 : `getHtml` > `String pagePathName = PathParser.render(pagepath);` > `.append("\n")`
- _내려가기 규칙_
  - 한 함수 다음에는 추상화 수준이 한 단계 낮은 함수가 온다.
  - 위에서 아래로 프로그램을 읽으면 함수 추상화 수준이 한 번에 한 단계씩 낮아진다.

## Switch 문

- switch문은 작게 만들기 어렵다.
  - 아래의 코드는 함수가 길고, 한 작업만 수행하지 않는다.
  - 새 직원 유형을 추가할 때마다 코드를 변경해야 한다.
  - 아래의 함수와 구조가 동일한 함수가 무한정 존재한다.
  ```tsx
  function calculatePay: Money (e: Employee){
      switch(e.type){
          case COMMISSIONED:
              return calculateCommissionedPay(e)
          case HOULY:
              return calculateHourlyPay(e)
          case SALARIED:
              return calculateSalariedPay(e)
          default:
              new InvalidEmployeeType(e.type)
      }
  }
  ```
- 다형성(polymorphism)을 이용한다.
  - 각 switch문을 저차원 클래스에 숨기고 절대로 반복하지 않는다.
- 추상 팩토리(Abstract Factory)에 꽁꽁 숨긴다.
  - 팩토리는 적절한 파생 클래스의 인스턴스를 생성한다.

## 서술적인 이름을 사용하라!

- 길고 서술적인 이름이 짧고 어려운 이름보다, 길고 서줄적인 주석보다 좋다.
- 여러 단어가 쉽게 읽히는 명명법을 사용하고, 여러 단어를 사용해 함수 기능을 잘 표현하는 이름을 선택한다.
- 이름을 붙일 때는 일관성이 있어야 한다.
  - 모듈 내에서 함수 이름을 같은 문구, 명사, 동사를 사용한다.

## 함수 인수

- 함수에서 이상적인 개수는 0개, 다음으로 1개(단항), 2개(이항)이다.
  - 3개(삼항)은 가능한 피하는 편이 좋고 4개 이상(다항)은 사용하면 안된다.
- _많이 쓰는 단항 형식_
  - 하나는 인수에 질문을 던지는 경우다.
    - `function fileExists: boolean ("MyFile")`
  - 하나는 인수로 뭔가를 변환해 결과를 반환하는 경우다.
    - `function fileOpen: InputStream("MyFile")`
  - 아주 유용한 단항 함수 형식은 이벤트이다.
    - 이벤트 함수는 입력 인수만 있고 출력 인수는 없다.
    - `passwordAttemptFailedNtimes(int attemps)`
  - 위 경우가 아니라면 단항 함수는 가급적 피한다.
    - `function includeSetupPageInfo: void (pageText: StringBuffer)` 👎
    - `function transform: StringBuffer (in: StringBuffer)` 👍
- _플래그 인수_
  - 함수로 boolean값을 넘기는 관례는 끔찍하다.
  - true이면 함수가 한꺼번에 여러가지를 처리한다는 의미이므로.
- _이항 함수_
  - 인수가 2개인 함수는 인수가 1개인 함수보다 이해하기 어렵다.
  - 직교 좌표계 점은 이항함수가 적절하다.
    - `p: Point = new Point(0, 0)`
  - 두 요소에 자연적인 순서가 있기도 하고 없는 경우도 있다.
- _삼항 함수_
  - 순서, 주춤, 무시로 야기되는 문제가 발생한다.
    - `assertEquals(message, expected, actual)`
- _인수 객체_
  - 인수가 2-3개 필요하다면 일부를 독자적인 클래스 변수로 선언할 가능성을 짚어 본다.
    - `makeCircle: Circle (x: float, y: float, radius: float)` 👎
    - `makeCircle: Circle (center: Pointer, radius: float)` 👍
- _인수 목록_
  - 때로는 인수 개수가 가변적인 함수도 필요하다.
  - 가변 인수를 취하는 함수는 단항, 이항, 삼항 함수로 취급하고 이를 넘어서는 인수를 사용할 경우에는 문제가 있다.
- _동사와 키워드_
  - 단항 함수는 함수의 인수가 동사/명사 쌍을 이뤄야 한다.
  - 또 다른 방법은 함수에 인수이름을 넣는 키워드를 추가하는 형식이다.

## 부수 효과를 일으키지 마라!

- 함수에서 한 가지를 하겠다고 약속하고선 남몰래 다른 짓도 하는 부수 효과는 거짓말이다.
- _출력 인수_
  - 객체 지향 언어에서는 출력 인수를 사용할 필요가 거의 없다.
    - appendFooter(s) 👎
    - report.appendFooter() 👍
  - 일반적으로 출력 인수는 피해야 한다.

## 명령과 조회를 분리하라!

- 함수는 뭔가를 수행하거나 뭔가에 답하거나 둘 중 하나만 해야한다.
  - `function set: boolean(attribute: string, value: string)` 이름이 attribute인 속성을 찾아 값을 value로 설정한 후 성공하면 true를 반환하고 실패하면 false를 반환한다.
  - `if(set("username", "unclebob"))...` 👎
  - `if(attrubuteExits("username")){setAttribute("username", "unclebob");}` 👍
  - 명령과 조회를 분리해 혼란을 뿌리 뽑는다.

## 오류 코드보다 예외를 사용하라!

- 오류 코드를 반환하면 호출자는 오류 코드를 곧바로 처리해야 한다는 문제에 부딪힌다.
- 오류 코드 대신 예외를 사용하면 오류 처리 코드가 원래 코드에서 분리되어지므로 코드가 깔끔해진다.
- _Try/Catch 블록 뽑아내기_
  - 정상 동작과 오류 처리 동작을 분리하면 코드를 이해하고 수정하기 쉬워진다.
- 오류 처리도 한 가지 작업이다.
  - 오류를 처리하는 함수는 오류만 처리해야 마땅하다.
- Error.java 의존성 자성
  - 오류 코드를 반환한다는 이야기는 어디선가 오류 코드를 정의한다는 뜻이다.
  - 새 오류 코드를 추가하는 대신 기존 오류 코드를 재사용한다.
  - 오류 코드 대신 예외를 사용하면 새 예외는 Exception 클래스에 파생된다.

## 반복하지 마라!

- 객체 지향 프로그래밍은 코드를 부모 클래스로 몰아 중복을 없앤다.

## 구조적 프로그래밍

- 함수와 함수 내 모든 블록에 입구와 출구가 하나만 존재해야 한다. - 데이크스트
- 함수는 return문이 하나여야 한다.
  - 루프 안에서 break나 continue를 사용해서 안된다.
  - goto는 절대로 안된다.
- 함수를 작게 만든다면 return, break, continue를 여러 차례 사용해도 괜찮다.
  - goto문은 큰 함수에서만 의미가 있다.

## 함수를 어떻게 짜죠?

- 처음에는 길고 복잡하다.
- 코드를 다듬고, 함수를 만들고, 이름을 바꾸고, 중복을 제거한다.
