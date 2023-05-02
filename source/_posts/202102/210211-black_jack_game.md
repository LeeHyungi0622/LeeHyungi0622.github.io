---
title: 210211 Blackjack game Class Design
date: 2021-02-11 17:11:00
tags:
  - Self-Development
  - Python-Assignment
categories:
  - Python
---

<div align="center">
  <img src="/images/post_images/210211_blackjack_img.jpg" alt="Blackjack"/>
</div>

## Blackjack Class Design

<table>
  <tr>
    <td>
      <img src="/images/post_images/210211_black_jack_handwriting.png" alt="210211_black_jack_handwriting">
    </td>
  </tr>
  <tr>
    <td align="center">
      <b>< Blackjack class design Handwriting ></b>
    </td>
  </tr>
</table>

  <!-- more -->

<table>
  <tr>
    <td>
      <img src="/images/post_images/210211_blackjack_uml.png" alt="210211_blackjack_uml">
    </td>
  </tr>
  <tr>
    <td align="center">
      <b>< Blackjack class design UML ></b>
    </td>
  </tr>
</table>
이번 과제에서는 저번 시간의 수업시간의 개념을 최대한 반영해서 class를 설계한다.

<ins><b>우선, Player와 Dealer의 공통적 행위 및 요소를 `PersonInCasino 추상 클래스(Abstract class)에 정의`한다.</b></ins>
각 각의 Player, Dealer class는 PersonInCasino abstract class를 상속받아 공통된 행위에 대한 다른 행동을 method 오버라이드(override)를 통해서 method를 재정의한다.
method명은 Blackjack 게임의 룰에 따라 카드의 숫자의 합이 21을 넘지 않는 경우, 카드를 계속 뽑는 경우를 `Hit`, 멈추는 것을 `Stay`로 하기 때문에 `doHit()` `doStay()`로 정의하였다. 이외에 카드 숫자의 합이 21을 초과하는 경우 `Burst`라고 한다.

앞의 Handwriting의 Blackjack 게임의 룰에서도 정리를 하였지만, Dealer는 `Dealer hit rule`이라는 규칙을 가지고 있기 때문에, 카드의 숫자합이 16이하이면 Hit, 17이상이면 무조건 Stay하도록 한다. 이는 상대 Player의 카드 상태와 상관없이 무조건적으로 Dealer가 지켜야하는 Rule이다.

<ins><b>한 번 개인적으로 생각해서 재미있는 기능을 넣어보았는데, 이 기능은 사람 클래스에 각자의 인격을 정의할 수 있는 personality를 적용하는 것이다.</b></ins>

> PersonInCasino 추상 클래스에 personality라는 class variable이 있는데, 이는 사람 성격에 따라 21미만의 카드를 뽑은 상태에서 카드를 계속 뽑을지(`Hit`) 아니면 멈출지(`Stay`)를 각기 다른 확률을 적용한다. <br/> 예를들어 소심한 사람은 20\~30%, 평범한 사람은 50%, 욕심이 많은 사람은 80\~90%의 확률로 카드를 계속 뽑는 결정을 하게 된다. <br/> 구체적인 구현 방법은 Python의 randchoice method에서 주어진 리스트에 대해 각 각 weight를 부여해서 확률을 조정할 수 있는데, 이를 활용해서 각기다른 성격을 가진 사람들이 다른 확률로 `Hit`을 한다.

Hand class의 ring finger, middle finger, back of hand를 정의한 이유는 게임을 할때 player의 gesture가 있기 때문이다.
ring finger(검지)로 테이블을 톡 내려주는 gesture를 취하는 경우는 <ins><b>Hit</b></ins>, 검지와 중지(middle finger + ring finger)를 테이블에 향하여 친 후 손가락을 떼는 gesture는 <ins><b>Split</b></ins>을 의미한다. 그리고 손등(back of hand)이 위로 보이게 해서 손목스냅을 이용해 테이블을 한번 훑어주는 모션은 <ins><b>Stay</b></ins>이다.

<ins><b>has-A 의존관계 중에 클래스간의 응집력이 높은 `Composition의 형태`로 Hand, Card, Game class로 Player와 Dealer의 대응되는 각 각의 instance variable을 초기화시켜줬다.</b></ins>

Card class는 Player와 Dealer 모두 동일한 카드 뭉치로부터 카드를 받고 있기 때문에 Card class의 cardList는 class 변수로써 선언을 하였다.

가능한 한 설계 단계에서 생각할 수 있는 경우의 수를 토대로 클래스 설계를 해보았다. 좀 더 가시적으로 클래스 관계를 이해할 수 있도록 Class UML(Unified Modeling Language)를 만들어서 첨부하였다.

## 코드 구현

<ins><b>실제 게임으로 각 Player 객체를 Dealer 객체가 관리하기 위해 설계단계에서는 고려되지 않았던 `Player management class(PlayerMgmt)`를 새로 추가하였다. 그리고 기존에 설계했던 class의 일부 method를 새로 추가하였다.</b></ins>

```python
from abc import ABCMeta, abstractmethod
from random import choices, shuffle

# (PersonInCasino Abstract class)
# Abstract method
# doHit() : 카드를 계속 뽑는다. (단, 카드의 숫자가 21미만인 경우)
# doStay() : 카드를 뽑지 않고 머무른다. (단, dealer의 경우, Dealer hit rule 적용)
# checkStatusOfHand() : 손의 상태를 확인한다. (gesture : Hit, Stay, Split)
# checkStatusOfGame() : 게임의 진행상태를 확인한다. (Hit, Stay, Burst, Blackjack, Split, Double down)
# (단, Split과 Double down은 player를 위한 규칙이다)
# checkCardsOnDeck() : Deck위에 카드를 확인한다. (완전탐색)
# setPersonality() : 사람의 인격을 정의한다.

# Player와 Dealer는 모두 Blackjack 카드 게임에 참여하므로, Blackjack게임을 위한
# 공통 행동을 abstract class의 abstract method로 정의한다.
class PersonInCasino(metaclass=ABCMeta):

    @abstractmethod
    def doHit():
        raise NotImplementedError()

    @abstractmethod
    def doStay():
        raise NotImplementedError()

    @abstractmethod
    def checkStatusOfHand():
        raise NotImplementedError()

    @abstractmethod
    def checkStatusOfGame():
        raise NotImplementedError()

    @abstractmethod
    def checkCardsOnDeck():
        raise NotImplementedError()

    @abstractmethod
    def setPersonality():
        raise NotImplementedError()

# Dealer class
# Personality는 "timid"(25%확률로 Hit), "normal"(50%확률로 Hit), "greedy"(75%의 확률로 Hit)로 구분하며,
# timid => [0, 1] weight = [0.75, 0.25]
# normal => [0, 1] weight = [0.5, 0.5]
# greedy => [0, 1] weight = [0.25, 0.75]
class Dealer(PersonInCasino):

    def __init__(self, _personality):
        self.personality = _personality
        self.statusOfGame = Game()
        self.statusOfHand = Hand()
        self.cardDeck = CardDeck()
        # Dealer가 일괄적으로 Player를 관리할 수 있도록 playerMgmt instance variable을 갖는다.
        self.playerMgmt = PlayerMgmt()
        # Blackjack 게임에서 사용될 카드들
        self.card = Card()

    # Player의 카드숫자 합이 21을 초과한 경우에 Player의 Game Status를 Burst로 업데이트하고
    # Player리스트에서 제거한 뒤에 BurstPlayer리스트에 업데이트한다.
    # Burst를 제외한 게임상태의 업데이트는 Player로부터 한다.
    def updateBurstPlayer(self, index):
        self.playerMgmt.updateBurstPlayer(index)

    def doHit(self):
        self.statusOfGame.updateGameStatus("Hit")

    def doStay(self):
        self.statusOfGame.updateGameStatus("Stay")

    def checkStatusOfHand(self):
        return self.statusOfHand

    def checkStatusOfGame(self):
        return self.statusOfGame

    def checkCardsOnDeck(self):
        return self.cardDeck

    def getDecisionOfHit(self):
        weight = {"timid": [0.75, 0.25], "normal": [
            0.5, 0.5], "greedy": [0.25, 0.75]}
        return choices([0, 1], weights=weight[self.personality])

    # 게임 참가자 수 만큼 카드를 한 장씩 나눠주고 1회 턴이 돌았을때
    # 각 게임 참가자의 카드를 검사해서 카드 수의 총 합이 21 초과인 경우 burst player로 구분한다.
    def pickupAndGiveCardToPlayer(self):
        # card 뭉치에서 카드를 pop()으로 뽑느다.
        # 우선 카드정보 리스트를 정의할 필요가 있다.
        numOfPlayer = self.playerMgmt.playerList
        for i in range(len(numOfPlayer)):
            if self.card.cardList:
                card = self.card.pickupTheCard()
                self.playerMgmt.updateCard(i, card)
        # 카드를 전체적으로 한 장씩 나눠주고, 각 Player의 카드정보를 확인하여,
        # 카드 숫자의 합이 21을 초과하는 사람은 updateBurstPlayer() method를
        # 사용하여 burst player로 구분한다.
        for j in range(len(numOfPlayer)):
            player = self.playerMgmt.checkPlayer(j)
            cardList = player['cardDeck'].checkCardStatusOfUser()
            _sum = 0
            for n in cardList:
                if n == 'A':
                    if sum(cardList) > 10:
                        sum += 1
                    else:
                        sum += 11
                else:
                    sum += n
            if _sum > 21:
                burstPlayer = self.playerMgmt.playerList.pop(j)
                self.playerMgmt.burstPlayerList.append(burstPlayer)

# Player class
# Personality는 "timid"(25%확률로 Hit), "normal"(50%확률로 Hit), "greedy"(75%의 확률로 Hit)로 구분하며,
# timid => [0, 1] weight = [0.75, 0.25]
# normal => [0, 1] weight = [0.5, 0.5]
# greedy => [0, 1] weight = [0.25, 0.75]
class Player(PersonInCasino):

    def __init__(self, _personality):
        self.personality = _personality
        self.statusOfGame = Game()
        self.cardDeck = CardDeck()
        self.playerMgmt = PlayerMgmt()
        # Player 객체 정의와 동시에 Player정보를 player management class의
        # Player 정보 리스트에 담아준다.
        self.__updatePlayerInfo()

    def doHit(self):
        self.statusOfGame.updateGameStatus("Hit")

    def doStay(self):
        self.statusOfGame.updateGameStatus("Stay")

    def checkStatusOfHand(self):
        return self.statusOfHand

    def checkStatusOfGame(self):
        return self.statusOfGame

    def checkCardsOnDeck(self):
        return self.cardDeck

    def getDecisionOfHit(self):
        weight = {"timid": [0.75, 0.25], "normal": [
            0.5, 0.5], "greedy": [0.25, 0.75]}
        return choices([0, 1], weights=weight[self.personality])

    # playerMgmt(Player management) class의 Player 정보 리스트를 업데이트
    def __updatePlayerInfo(self):
        playerInfo = {"statusOfGame": self.statusOfGame, "statusOfHand": self.statusOfHand,
                      "personality": self.personality, "cardDeck": self.cardDeck}
        self.playerMgmt.updatePlayerInfo(playerInfo)

# Hand class
# 약지(ring finger)을 사용해서 gesture를 하는경우 => Hit
# 손등(backOfHand)을 사용해서 gesture를 하는경우 => Stay
# 약지와 중지(ring finger + middle finger)를 사용해서 gesture를 하는 경우 => Split
class Hand:
    def __init__(self):
        self.ringFinger = False
        self.middleFinger = False
        self.backOfHand = False

    def useRingFinger(self):
        self.ringFinger = True

    def useMiddleFinger(self):
        self.middleFinger = True

    def useBackOfHand(self):
        self.backOfHand = True

    def resetStatusOfHand(self):
        self.ringFinger = False
        self.middleFinger = False
        self.backOfHand = False

# Card class
# 카드의 종류와 상관없이 2~9까지는 각 각 4장씩 넣고
# 10은 10(4장) J,Q,K 각 각 4장씩 총 16장을 넣어준다.
# Ace의 경우에는 1또는 11로 계산될 수 있기 때문에 'A'로 4장 넣어준다.
class Card:
    cardList = []

    def __init__(self):
        self.prepareCards()

    @classmethod
    def prepareCards(cls):
        # 2~9까지 숫자카드를 각 각 4장씩 넣는다.
        for i in range(2, 9+1):
            for _ in range(4):
                cls.cardList.append(i)
        # 숫자카드 10을 16장 넣어준다.
        for _ in range(16):
            cls.cardList.append(10)
        # Ace 카드를 4장 넣어준다.
        for _ in range(4):
            cls.cardList.append('A')
        # 카드를 섞어준다.
        cls.__shuffleCards()

    @classmethod
    def pickupTheCard(cls):
        return cls.cardList.pop()

    @classmethod
    def __shuffleCards(cls):
        shuffle(cls.cardList)

# CardDeck class
class CardDeck:
    def __init__(self):
        self.bettingChipArea = 0
        self.cardArea = []

    def checkBettingChipArea(self):
        return self.bettingChipArea

    def checkCardStatusOfUser(self):
        return self.cardArea

    def updateBettingChipArea(self, chip):
        self.bettingChipArea += chip

    def updateCardArea(self, card):
        self.cardArea.append(card)

# Game class
# player가 split을 하는 경우, numOfLife를 증가시켜준다.
class Game:
    def __init__(self):
        self.numOfLife = 1
        self.status = ""

    def updateNumOfLife(self, life):
        self.numOfLife += life

    def updateGameStatus(self, _status):
        self.status = _status


# Player객체를 일괄적으로 관리하기 위한 Player management class
class PlayerMgmt:
    playerList = []
    burstPlayerList = []

    # index번째 player의 객체 정보를 참조할 수 있도록 객체를 반환한다.
    @classmethod
    def checkPlayer(cls, index):
        if cls.playerList[index]:
            return cls.playerList[index]
        else:
            print("해당 Player는 존재하지 않습니다.")

    @classmethod
    # Blackjack참가 Player의 객체 정보를 playerList에 업데이트한다.
    def updatePlayerInfo(cls, player):
        cls.playerList.append(player)

    @classmethod
    def updateBurstPlayer(cls, index):
        # list의 index는 0번째부터 시작하기 때문에 -1을 해서 index-1번째 player를 pop한 뒤에
        # burstPlayer list에 업데이트를 해준다.
        if cls.playerList[index-1]:
            burstPlayer = cls.playerList.pop(index-1)
        else:
            print("해당 Player는 존재하지 않습니다.")

    # index번째 Player의 carddeck의 카드를 업데이트한다.
    @classmethod
    def updateCard(cls, index, card):
        cls.playerList[index]['cardDeck'].updateCardArea(card)

```
