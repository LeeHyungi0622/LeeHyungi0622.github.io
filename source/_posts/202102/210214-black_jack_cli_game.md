---
title: 210214 Blackjack CLI Based Game
date: 2021-02-14 11:04:00
tags:
  - Self-Development
  - Python-Assignment
categories:
  - Python
---

![](/images/post_images/210214_blackjack_play_img.jpg)

## Blackjack CLI Based Game

이번 과제는 Python을 활용해서 간단한 CLI Based Game을 만드는 것이다.

## 게임 실행결과 화면

<table>
    <tr>
        <td>
            <h4><b>Blackjack으로 게임을 종료하는 경우</b></h4>
        </td>
        <td>
            <h4><b>뽑은 카드들의 수의 합이 21을 초과하여 상대편이 이기는 경우</b></h4>
        </td>
    </tr>
    <tr>
        <td>
            <img src="/images/post_images/210214_1.png" alt="210214_1" />
        </td>
        <td>
            <img src="/images/post_images/210214_2-1.png" alt="210214_2-1" />
            <img src="/images/post_images/210214_2-2.png" alt="210214_2-2" />
            <img src="/images/post_images/210214_2-3.png" alt="210214_2-3" />
        </td>
    </tr>
</table>

  <!-- more -->

## 코드 (Python)

```python
from random import shuffle
import re
import sys

# player와 bank의 게임진행 상태
player_play = True
bank_play = True
# 뽑은 카드의 숫자 합이 21을 초과하는 경우의 palyer와 bank
player_lose = False
bank_lose = False
turn = 0

# card_deck에 카드를 준비
def prepareCards():
    for i in range(2, 11+1):
        for _ in range(4):
            card_deck.append('A' if i == 11 else i)

# card deck을 3회 shuffle
def shuffleCardsTreeTimes():
    return [shuffle(card_deck) for _ in range(3)]

# card deck에 남아있는 카드가 10장 미만일 경우에 card deck을 reset하고 다시 shuffle한다.
def resetCards():
    # 카드를 다시 준비한다.
    prepareCards()
    # 카드를 다시 3회 섞는다.
    shuffleCards()

# 사용자로부터 Y(y) or N(n)를 올바르게 입력받았는지 정규표현검사
def regexCheck(value):
    answer_check = re.compile(r'^(?:Y|N)$', re.I)
    return answer_check.match(value)

# 사용자로부터 한 번 더 shuffle할 것인지 물어보는 처리
def askMoreShuffle():
    # 사용자로부터 한 번 더 shuffle을 할 것인지 묻기
    while True:
        shuffle_more = input(
            '✨✨✨✨✨✨✨Shuffle을 한 번 더 하길 원하십니까? (Y(y)/N(n))✨✨✨✨✨✨✨')
        # 사용자로부터 입력받을 Y/N를 정규표현식으로 검사
        answer = regexCheck(shuffle_more)
        if answer:
            if answer.group() == 'Y' or answer.group() == 'y':
                print("✨✨✨✨✨✨✨한 번 더 card deck의 shuffle을 진행합니다.✨✨✨✨✨✨✨")
                shuffle(card_deck)
                break
            else:
                print("✨✨✨✨✨✨✨추가적인 card deck의 shuffle을 진행하지 않습니다.✨✨✨✨✨✨✨")
                break
        else:
            print("🥲🥲🥲🥲🥲🥲🥲 입력을 확인후 다시 입력해주세요.🥲🥲🥲🥲🥲🥲🥲")

# 뽑은 카드들의 숫자 합을 반환한다.
def getSumOfCards(cards):
    sum_of_cards = 0
    for idx, card in enumerate(cards):
        if card == 'A':
            # 우선 Ace 카드를 11로 치환해서 A 카드를 제외한 카드들의 숫자 합에 더해준다.
            sum_of_cards += 11
            # 카드의 합이 21을 초과하는 경우,
            if sum_of_cards > 21:
                # Ace카드를 1로 치환해서 더해준다.
                sum_of_cards -= 10
        else:
            sum_of_cards += card
    return sum_of_cards

# 뽑은 카드들의 숫자 합을 기준으로 결과를 판별한다.
def checkSumOfCards(player, sum_of_cards):
    if sum_of_cards > 21:
        print(
            "😵‍💫😵‍💫😵‍💫😵‍💫😵‍💫{0}가 받은 카드들의 숫자 합이 21을 초과했습니다. {0}의 턴을 종료합니다.😵‍💫😵‍💫😵‍💫😵‍💫😵‍💫".format(player))
        if player == '뱅크':
            global player_lose
            bank_lose = True
            if player_lose:
                print("😮‍💨😮‍💨😮‍💨😮‍💨😮‍💨 사용자와 뱅크는 서로 비겼습니다. 😮‍💨😮‍💨😮‍💨😮‍💨😮‍💨")
                sys.exit(0)
            else:
                print("🎉🎉🎉🎉🎉🎉🎉 사용자가 이겼습니다. 🎉🎉🎉🎉🎉🎉🎉")
                sys.exit(0)
        else:
            player_lose = True
    elif sum_of_cards == 21:
        print(
            "👍👍👍👍👍 {0}가 받은 카드들의 숫자 합이 21입니다. Blackjack으로 {0}가 승리하였습니다. 👍👍👍👍👍".format(player))
        sys.exit(0)
    else:
        print("{0}가 받은 카드들의 숫자 합은 {1}입니다.".format(player, sum_of_cards))


# 딜러로부터 카드를 받는 처리
def receiveCard(player, cards):
    print("\n")
    print("🌱🌱🌱🌱🌱🌱🌱 << {0}의 {1}번째 카드 분배입니다. >>🌱🌱🌱🌱🌱🌱🌱".format(
        player, turn+2))
    print("그 다음으로 {} 측 게임을 진행합니다. ".format(player))
    # 카드를 점검합니다. 카드 덱에 남아있는 카드가 10장 미만인 경우,
    # 카드덱의 상태를 재정비합니다.
    if len(card_deck) < 10:
        resetCards()
    card = card_deck.pop()
    print('😃😃😃😃😃😃😃{0:>3}는 {1:>2} 카드를 받았습니다.😃😃😃😃😃😃😃'.format(player, card))
    cards.append(card)
    print("💡💡💡💡💡💡{}의 Card deck을 확인 => {}💡💡💡💡💡💡".format(player, cards))


# 카드를 뽑고, 카드의 합을 구하고, 카드의 합이 21을 초과하는지 검사하는 공통화 처리
def commonCardGameProcess(player, cards, sum_of_cards):
    # card deck으로부터 카드를 뽑습니다.
    receiveCard(player, cards)
    # 여지까지 뽑은 카드의 합을 구합니다.
    sum_of_cards = getSumOfCards(cards)
    # 여기서 cards의 숫자의 합이 21을 초과하는지 검사한다.
    checkSumOfCards(player, sum_of_cards)


# player와 bank의 게임
def cardPlay(player, sum_of_cards, cards):
    global turn, player_play, bank_play
    print("\n")
    print("🌱🌱🌱🌱🌱🌱🌱< {0}의 {1}번째 카드 분배입니다. >>🌱🌱🌱🌱🌱🌱🌱".format(
        player, turn+2))
    print("🔥🔥🔥🔥🔥🔥🔥 다음은 {}의 순서입니다. 🔥🔥🔥🔥🔥🔥🔥".format(player))
    if player == "뱅크" and sum_of_cards < 16:
        print("Dealer hit rule에 의해 카드의 합이 16을 넘지 않으므로, 카드를 뽑습니다.")
        # 카드를 뽑고, 뽑은 카드의 합을 구하고, 그 합이 21을 초과하지 않는지 검사
        commonCardGameProcess(player, cards, sum_of_cards)
        # 사용자, 뱅크 모두 순회를 끝냈다면 turn 값을 1증가시켜준다.
        turn += 1
    else:
        more_card = input(
            "✊✊✊✊✊✊ 카드를 더 뽑으시겠습니까? (Y(y)/N(n)) ✊✊✊✊✊✊")
        result = regexCheck(more_card)
        if result.group() == 'Y' or result.group() == 'y':
            # 카드를 뽑고, 뽑은 카드의 합을 구하고, 그 합이 21을 초과하지 않는지 검사
            commonCardGameProcess(player, cards, sum_of_cards)
            # 사용자, 뱅크 모두 순회를 끝냈다면 turn 값을 1증가시켜준다.
            if player == "뱅크":
                turn += 1
        else:
            if player == "사용자":
                player_play = False
            else:
                bank_play = False
            result_dict[player] = sum_of_cards
            if player == "사용자":
                player_play = False
            if player == "뱅크":
                bank_play = False


# 게임의 순번에 따라 게임을 진행
# arg: player -> 사용자, 뱅크
# arg: cards  -> 사용자(cards_for_bank), 뱅크(cards_for_bank)
def rotateTurnOfGames(player, cards):
    global turn
    global player_play
    global bank_play

    # print('{0}의 카드 수의 총 합은 {1}입니다.'.format(player, sum_of_cards))
    sum_of_cards = getSumOfCards(cards)

    if turn > 0:
        if (player == "사용자" and (player_lose != True and player_play != False)):
            cardPlay(player, sum_of_cards, cards)
        elif (player == "뱅크" and (bank_lose != True and bank_play != False)):
            cardPlay(player, sum_of_cards, cards)

    else:
       # 카드를 뽑고, 뽑은 카드의 합을 구하고, 그 합이 21을 초과하지 않는지 검사
        commonCardGameProcess(player, cards, sum_of_cards)
        # 사용자, 뱅크 모두 순회를 끝냈다면 turn 값을 1증가시켜준다.
        if player == "뱅크":
            turn += 1


# 게임 실행
card_deck = []
print("👨👨👨👨👨👨👨👨👨 카드를 준비합니다. 👨👨👨👨👨👨👨👨👨")
prepareCards()
# card_deck을 3회 섞는다.
print("✨✨✨✨✨✨✨✨ 준비된 카드를 3회 섞습니다. ✨✨✨✨✨✨✨✨")
shuffleCardsTreeTimes()

# 카드를 한 번 더 섞을 것인지 사용자로부터 확인한다.
askMoreShuffle()

print("✨✨✨✨✨✨✨✨ 카드섞기를 완료하였습니다. ✨✨✨✨✨✨✨✨")
# card shuffle이 완성된 후에는 tuple로 카드의 순서를 고정한다.
card_deck = tuple(card_deck)
print("\n")
print("💪💪💪💪💪💪💪💪 < Blackjack Game Start >> 💪💪💪💪💪💪💪💪")
print("게임이 시작됩니다. 사용자와 뱅크는 각 각 한 장의 카드를 받습니다.")
# 뱅크와 user의 card를 보관할 리스트를 정의한다.
cards_for_bank = list()
cards_for_user = list()

# 게임이 시작되면 bank와 user는 카드를 한 장씩 받게 된다.
# 카드는 한쪽방향으로 꺼내기때문에 pop()으로 꺼내도록 한다.
card_deck = list(card_deck)
card_for_user = card_deck.pop()
card_for_bank = card_deck.pop()

cards_for_user.append(card_for_user)
cards_for_bank.append(card_for_bank)

# 받은 카드는 출력을 하도록 한다.
print('{0:>3}는 {1:>2} 카드를 받았습니다.'.format("사용자", card_for_user))
print('{0:>4}는 {1:>2} 카드를 받았습니다.'.format("뱅크", card_for_bank))

sum_of_user_cards = 0
sum_of_bank_cards = 0

# cards_for_bank, cards_for_user
player = {'사용자': cards_for_user, '뱅크': cards_for_bank}

turn = 0

result_dict = {}

while player_play or bank_play:
    for k, v in player.items():
        result = rotateTurnOfGames(k, v)
        # print(result)

print("\n")

print("✨✨✨✨✨✨✨✨✨✨✨✨✨✨< Blackjack Game End >>✨✨✨✨✨✨✨✨✨✨✨✨✨✨")
print("✨✨✨✨✨✨ 사용자가 뽑은 카드 수의 합은 {사용자}, 뱅크가 뽑은 카드 수의 합은 {뱅크}입니다. ✨✨✨✨✨✨".format(
    **result_dict))

game_result = [(k, v) for k, v in result_dict.items()]

if result_dict['사용자'] == result_dict['뱅크']:
    print("🎉🎉🎉🎉🎉🎉🎉 사용자와 뱅크는 비겼습니다. 🎉🎉🎉🎉🎉🎉🎉")
else:
    sorted_result = sorted(game_result, key=lambda x: x[1], reverse=True)
    print("\n")
    print("🎉🎉🎉🎉🎉🎉🎉🎉🎉🎉🎉🎉🎉게임의 승리자는 {}입니다.🎉🎉🎉🎉🎉🎉🎉🎉🎉🎉🎉🎉🎉".format(
        sorted_result[0][0]))

```
