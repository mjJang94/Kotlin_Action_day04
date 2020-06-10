# 봉인된 클래스: 클래스 계층 정의 시 계층 확장 제한

상위 클래스인 Expr에는 숫자를 표현하는 Num과 덧셈 연산을 표현하는 Sum이라는 두 하위 클래스가 있다.   
when 식에서 이 모든 하위 클래스를 처리하면 편리하다. 하지만 when 식에서 Num과 Sum이 아닌 경우를 처리하는 else 분기를 반드시 추가해야 한다.

<pre><code>

interface Expr
class Num(val vlaue: Int) : Expr
class Sum(val left: Expr, val right: Expr) : Expr

fun eval(e: Expr): Int = 
  when(e) {
 
 is Num -> e.value
 is Sum -> eval(e.right) + eval(e.left)
 else ->
    throw IllegalExcep ~~
  
 }

</code></pre>

하지만 항상 디폴트 분기를 추가해주는게 귀찮다. 그리고 디폴트 분기가 있으면 이런 클래스 계층에 새로운 하위 클래스를 추가하더라도 컴파일러가 when이 모든 경우를 처리하는지 제대로 검사할 수 없다.   
코틀린은 이런 문제점을 해결하기 위해 sealed라는 변경자가 준비되어 있다. 이 변경자를 통해 상위 클래스를 상속한 하위 클래스 정의를 제한할 수 있다.

<pre><code>

sealed class Expr {
  class Num(val value: Int) : Expr()
  class Sum(val left: Expr, val right: Expr) : Expr()
}

fun eval(e:Expr) : Int =

  when(e) {                     //------ when식이 모든 하위 클래스를 검사한다.
    is Expr.Num -> e.value
    is Expr.Sum -> eval(e.right) + eval(e.left)
  }

</code></pre>

sealed 클래스는 모두 기본적으로 open이다

# 클래스 초기화: 주 생성자와 초기화 블록

<pre><code>
class Ueser(val nickname: String)
</code></pre>

일반적으로 클래스의 모든 선언은 {} 안에 들어간다. 하지만 이 클래스는 val 선언만 존재한다.    
이렇게 클래스 이름 뒤에 오는 괄호로 둘러싸인 코드를 주 생성자라고 부른다.   
주 생성자는 생성자 파라미터를 지정하고 그 생성자 파라미터에 의해 초기화 되는 프로퍼티를 정의하는 두 가지 목적으로 쓰인다.
<pre><code>
class User constructor(_nickname: String) {
  val nickname: String
  
  init{             //------초기화 블록
    nickname = _nickname
  }
}
</code></pre>

언더바 _ 는 프로퍼티와 생성자 파라미터를 구분해준다. 자바의 this와 비슷한 개념인데 모호성을 없애주는 장점이 있다.   

클래스에 기반 클래스가 있다며ㄴ 주 생성자에서 기반 클래스의 생성자를 호출해야 할 필요가 있따. 기반 클래스를 초기화 하려면 기반 클래스 이름 뒤에 괄호를 치고 생성자 인자를 넘긴다.

<pre><code>
open class User(val nickname: String) {~~~~}
class TwitterUer(nickname: String) : User(nickname) {~~~~}
</code></pre>

어떤 클래스를 외부에서 인스턴스화하지 못하게 막고싶으면 모든 생성자를 private으로 만드는 방법도 있다.

<pre><code>
class Secretive private constructor() {}
</code></pre>

Secretive 클래스 안에는 주 생성자밖에 없고 그 주 생성자는 비공개이므로 외부에서는 Secretive를 인스턴스화할 수 없다. 나중에 동반 객체에 대해 설명하면서 동반 객체 안에서 이런 비공개 생성자를 호출하면 좋은 점을 알려준다고 한다.


다음시간 '부 생성자'
