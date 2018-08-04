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



아래와 같은 MVC 패턴이 여러개 모여 하나의 앱을 만들게 됩니다. 

 
MVC 구현하기

 

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

Card라는 모델에 들어가야할 필수적인 프로퍼티들을 아래와 같이 정합니다.

Concentration에서 초기화로 인한 에러가 계속 발생하는 것을 확인할 수 있습니다.

struct Card {
    var isFaceUp = false
    var isMatched = false
    var identifier: Int
}
아래와 같이 빈 배열로 초기화 해줍니다.

var cards = [Card]()


카드 초기화 하기



1. 초기화

class는 모든 프로퍼티가 초기화 되면 인수가 없는 init을 자동으로 가지게 됩니다.

struct는 모든 프로퍼티를 초기화 할 수 있는 init을 자동으로 가지게 됩니다.

Init 함수는 통상적으로 인자의 내, 외부명이 동일하게 사용합니다.

//Struct

    init(identifier: Int) {
        self.identifier = identifier
    }

//Class

    init(numberOfPairsOfCards: Int) {
        for identifier in 0..<numberOfPairsOfCards {
            let card = Card(identifier: identifier)
            cards += [card, card]
        }
카드의 identifier를 Concentration 클레스에서 정하는 것이 맞을까요?

Card 구조체에서 정하는 것이 더욱 적절할 것으로 보입니다.



Static

Static은 타입에 여러분이 원하는 프로퍼티나 메소드를 연결할 수 있습니다.

인스턴스를 만들 필요 없이 타입에 dot operator를 사용해서 프로퍼티와 메소드에

접근할 수 있습니다.

static var identifierFactory = 0
    
    static func getUniqueIdentifier() -> Int {
        identifierFactory += 1
        return identifierFactory
    }
    
    init() {
        self.identifier = Card.getUniqueIdentifier()
    }


카드 뒤집기



1.  Concentration 사용하기

프로퍼티 이니셜라이져 안에서 인스턴스 멤버인 cardButtons 사용할 수 없습니다. Concentration도 초기화 중이고, cardButtons도 초기화 중이기 때문입니다.

서로가 불완전한 상태에서 상호 의존적인 관계에 있습니다. 이를 해결하기 위해서 lazy를 이용해서

game이 사용될때 초기화를 해주도록 합시다.

lazy var game = Concentration(numberOfPairsOfCards: (cardButtons.count + 1) / 2)
2. updateViewFromModel 메소드 만들기

특정 인덱스의 카드가 선택되었음을 확인시켜 주고, 카드가 매칭 되었을 때는 투명한 색으로 설정하고 그렇지 않은 경우에는 카드를 다시 뒤집는 코드 입니다.

    func updateViewFromModel() {
        for index in cardButtons.indices {
            let button = cardButtons[index]
            let card = game.cards[index]
            if card.isFaceUp {
                button.setTitle(emoji(for: card), for: UIControlState.normal)
                button.backgroundColor =  colorLiteral(red: 1.0, green: 1.0, blue: 1.0, alpha: 1.0)
            } else {
                button.setTitle("", for: UIControlState.normal)
                button.backgroundColor = card.isMatched ?  colorLiteral(red: 1, green: 0.5763723254, blue: 0, alpha: 0) :  colorLiteral(red: 1, green: 0.5763723254, blue: 0, alpha: 1)
            }
        }
    }
3. 이모티콘 넣기

이모티콘의 목록에서 우리가 원하는 이모티콘을 선택해서 보여주는 역할을 합니다. 컨트롤러는 UI로직이 어떻게 될 것인지에 대해 신경을 씁니다.

    var emojiChoices = ["🦇", "😱", "🙀", "👿", "🎃", "👻", "🍭", "🍬", "🍎"]
    
    func emoji(for card: Card) -> String {
        return "?"
    }
4. Concentration 모델에서 카드 뒤집기 코드 완성하기

func chooseCard(at index: Int) {
        if cards[index].isFaceUp {
            cards[index].isFaceUp = false
        } else {
            cards[index].isFaceUp = true
        }
}


이모티콘 나오게 하기



딕셔너리

딕셔너리는 key, value의 쌍으로 이루어져 있는 데이터 구조 입니다.

인자의 명을 정할 때는 글을 읽듯이 읽히는 것이 좋습니다.

카드 인자에 대해 emoji라는 함수가 호출되면 String 타입을 반환하게 됩니다.

만약 유일한 구분자를 가지고 있는 카드에 매칭되는 이모지가 존재하지 않고, 아직 emojiChoices에 이모지가 남아 있다면,

emojiChoices의 갯수 -1 까지 생성되는 임의의 숫자의 인덱스에 해당하는 emojiChoices내의 이모지를 지우면서 emoji 딕셔너리에 할당합니다.

그렇지 않은 경우에는 ?를 반환합니다.

    func emoji(for card: Card) -> String {
        if emoji[card.identifier] == nil, emojiChoices.count > 0 {
            let randomIndex = Int(arc4random_uniform(UInt32(emojiChoices.count)))
            emoji[card.identifier] = emojiChoices.remove(at: randomIndex)
        }
        return emoji[card.identifier] ?? "?"
    }


Concentration Model 로직 구성하기

뒤집혀진 카드의 숫자를 Tracking 하는 변수를 하나 설정하겠습니다. 어떤 카드도 뒤집혀 있지 않은 상태(== nil)가 있을 수 있기 때문에 옵셔널로 타입을 정합니다.

var indexOfOneAndOnlyFaceUpCard: Int?
첫번째 분기에서, matchIndex에 뒤집혀진 카드의 index 값을 넣고, 뒤집힌 카드가 동일한 카드임을 방지하기 위해 matchIndex != index를 선언합니다. 만약 두개의 조건이 사실이라면,

실제 두개의 카드가 일치하는지 확인하게 됩니다.

일치하기 때문에 각각의 카드가 가지고 있는 isMatched에 대한 Bool 값을 true로 바꿔줍니다.

