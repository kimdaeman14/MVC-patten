# MVC-patten

1. Model

앱이 무엇인지에 대해 관심을 가집니다.

UI와 독립되어 있습니다.



2. Controller

어떻게 화면에 표시할 것인지에 대해 관심을 가집니다.



3. View

UIButton, UIViewController, UILabel와 같은 UI와 관련된 것이고 Controller의 통제를 받게 됩니다.



서로의 관계



Model과 Controller

Controller는 모델에 직접적으로 접근할 수 있지만, Model은 Controller에 Notification & KVO 방식을 통해

모델의 변화를 알립니다.



Model과 View

Model은 UI에 독립적이며 View와 소통할 수 없으며, View 또한 불가능합니다.



View와 Controller

Controller는 View에 대해 outlet을 이용해 View에게 직접적으로 접근할 수 있습니다. =

View는 Controller에게 구조적으로 미리 정해진 방식으로 Controller에게 행위에 대한 요청(delegate)과

데이터에 대한 요청(data source)을 할 수 있습니다. 뿐만 아니라, action(View) - target(controller)의 구조로

사용자의 행위에 따라 필요한 함수를 호출할 수도 있습니다.



아래와 같은 MVC 패턴이 여러개 모여 하나의 앱을 만들게 됩니다  . 

 
MVC 구현하기


(https://user-images.githubusercontent.com/34432988/43676823-329d54a8-9833-11e8-9374-ea4a471e61d5.png)
 

1. Concentration model 만들기

Model을 구현해 봅시다.

Model은 말씀 드렸던 것처럼 UI에 관심이 없습니다. 스위프트파일을 선택하여 만들게 됩니다.

class를 선언하고 프로퍼티와 메소드를 생각해 봅시다.

카드의 묶음에 대한 데이터를 포함하고 있는 배열을 하나 만들어야 할 것이고,

카드를 선택하는 것에 대한 메소드가 있어야 되겠지요.

아래와 같은 코드를 스위프트 파일에 넣으면 될 것입니다.

class Concentration {
    var cards: Array<Card>
    
    func chooseCard(at index: Int) {
        
    }
}


2. 구조체로 card 만들기

구조체는 상속이 없습니다.

구조체는 값타입이고, 클래스는 참조타입이라는 것입니다.

값타입은 사용하게 될때 복사되게 됩니다.

값이 계속 복사 된다면 불필요한 리소스가 사용된다고 생각 할 수도 있지만

스위프트는 영리하게도 모두 복사하는 것이 아니라 누군가가 값을 변경했을 때, 복사하게 됩니다.

이를 ‘쓰기 시 복사’ 전달 체계라고 합니다.









