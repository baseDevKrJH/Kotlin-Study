# 12. 클래스 정의하기

## 클래스 정의하기

{% code title="Player.kt" %}
```kotlin
class Player
```
{% endcode %}

## 인스턴스 생성하기

Player 클래스의 생성자를 호출하여 인스턴스를 생성해야함.

```kotlin
val player = Player()
```

## 클래스 함수

* 클래스에는 행동과 데이터를 정의한다.
* 클래스 내부에 정의된 함수를 클래스 함수라 한다.

{% code title="Player.kt" %}
```kotlin
class Player {
    fun castFireball(numFireballs: Int = 2) =
            println("한 덩어리의 파이어볼이 나타난다. (x$numFireballs)")
}
```
{% endcode %}

## 가시성과 캡슐화

* 클래스 함수나 속성에 가시성 제한자를 지정하지 않으면 기본적으로 public
* private 클래스 함수는 자신이 정의된 클래스 내부에서만 사용
* 정보은닉, 캡슐화
* 자바의 패키지 가시성은 코틀린에 없음

| 제한자 | 설명 |
| :--- | :--- |
| public | 함수나 속성이 클래스 외부에서 사용, 가시성 제한자가 없으면 기본적으로 pubic |
| private | 함수나 속성이 정의된 클래스 내부에서만 사용 |
| protected | 함수나 속성이 정의된 클래스 내부 또는 이 클래스의 서브 클래스에서만 사용 |
| internal | 함수나 속성이 정의된 클래스가 포함된 모듈에서 사용 |

## 클래스 속성

* 클래스 속성은 클래스의 데이터 즉, 상태나 특성을 나타냄.

{% code title="Player.kt" %}
```kotlin
class Player {
    val name = "madrigal"

    fun castFireball(numFireballs: Int = 2) =
            println("한 덩어리의 파이어볼이 나타난다. (x$numFireballs)")
}
```
{% endcode %}

{% code title="Game.kt" %}
```kotlin
fun main(args: Array<String>) {
    //val name="마드리갈"
    var healthPoints = 89
    val isBlessed = true
    val isImmortal = false

    val player = Player()
    player.castFireball()

    val auraColor = auraColor(isBlessed, healthPoints, isImmortal)
    val healthStatus = formatHealthStatus(healthPoints, isBlessed)

    printPlayerStatus(auraColor, isBlessed, player.name, healthStatus)
}
```
{% endcode %}

### 속성의 게터와 세터

* 우리가 정의한 각 속성에 대해 필드와 게터가 자동생성되며, 경우에 따라 세터도 자동생성\(세터:속성이 var일 떄만 자동생성\)

{% code title="Player.kt" %}
```kotlin
class Player {
    //val name = "madrigal"
    var name = "madrigal"
        get() = field.capitalize()
        set(value) {
            field = value.trim()
        } 

    fun castFireball(numFireballs: Int = 2) =
            println("한 덩어리의 파이어볼이 나타난다. (x$numFireballs)")
}
```
{% endcode %}

### 속성의 가시성

{% code title="Player.kt" %}
```kotlin
class Player {
    //val name = "madrigal"
    var name = "madrigal"
        get() = field.capitalize()
        private set(value) {
            field = value.trim()
        } 

    fun castFireball(numFireballs: Int = 2) =
            println("한 덩어리의 파이어볼이 나타난다. (x$numFireballs)")
}
```
{% endcode %}

### 산출 속성

## NyetHack 코드 리팩터링하기

{% code title="Player.kt" %}
```kotlin
class Player {
    //val name = "madrigal"
    var name = "madrigal"
        get() = field.capitalize()
        set(value) {
            field = value.trim()
        }

    var healthPoints = 89
    val isBlessed = true
    private val isImmortal = false

    fun castFireball(numFireballs: Int = 2) =
            println("한 덩어리의 파이어볼이 나타난다. (x$numFireballs)")

    fun auraColor(/*isBlessed: Boolean, healthPoints: Int, isImmortal: Boolean)*/): String {
        val auraVisible = isBlessed && healthPoints > 50 || isImmortal
        val auraColor = if (auraVisible) "GREEN" else "NONE"
        return auraColor
    }

    fun formatHealthStatus(/*healthPoints: Int, isBlessed: Boolean*/): String {
        val healthStatus = when (healthPoints) {
            100 -> "최상의 상태임!"
            in 90..99 -> "약간의찰과상만 있음."
            in 75..89 -> if (isBlessed) {
                "경미한 상처가 있지만 빨리 치유되고 있음!"
            } else {
                "경미한 상처가 있음."
            }
            in 15..74 -> "많이 다친 것 같음"
            else -> "최악의 상태임!"
        }
        return healthStatus
    }
}
```
{% endcode %}

{% code title="Game.kt" %}
```kotlin
fun main(args: Array<String>) {
    val player = Player()
    player.castFireball()
    
    printPlayerStatus(player)
}

private fun printPlayerStatus(player: Player) {
    println("(Aura: ${player.auraColor()}) " +
            "(Blessed: ${if (player.isBlessed) "YES" else "NO"})")
    println("${player.name} ${player.formatHealthStatus()}")
}
```
{% endcode %}

{% hint style="info" %}
printPlayerStatus\(player: Player\)

printPlayerStatus\(auraColor: String, isBlessed: Boolean, name: String, healthStatus: String\)
{% endhint %}

## 패키지 사용하기

* 프로젝트의 규모가 커지고 복잡해질 때 프로젝트를 구성하고 관리하기 좋다
* 같은 이름을 갖는 요소들의 이름 충돌을 막을수 있다

{% hint style="info" %}
import mypkg.io.extractValue

import mypkg.util.extractValue as extractValueUtil

val value1 = extractValue\(\)

val value2 = extractValueUtil\(\)
{% endhint %}

## 궁금증 해소하기

### - var과 val 속성의 내부 구현 

* var와 val의 가장 큰 차이점은 세터

### - 경합 상태 방지하기

* 경합상태 : 특정 코드의 데이터를 프로그램의 다른 코드에서 동시에 변경할 때 발생\(ex:여러개의 스레드 실행시\)

### - 패키지 가시성

* 자바는 기본적으로 패키지 가시성 사용 \(가시성 제한자가 없는 매서드, 필드, 클래스는 같은 패키지에 있는 클래스에서만 사용 가능\)
* 코틀린은 패키지 가시성이 없음 \( 같은 패키지에 있는 클래스, 함수, 속성 등은 기본적으로 상호사용할수 있음\)
* 코틀린에서는 internal 가시성 지원 \( 같은 모듈에 있는 클래스, 함수, 속성끼리 상호 사용 할 수 있음 \) - internal이 지정된 클래스와 이 클래스의 함수,속성은 바이트코드 파일에서 public으로 바뀜

##  

