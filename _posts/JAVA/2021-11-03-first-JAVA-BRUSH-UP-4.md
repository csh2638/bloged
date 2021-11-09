---
layout: post
title: "[Java] Java Brush Up - 4"
categories: JAVA
tags: [JAVA]
---

## 유효범위

~~~java

public class Practice {
    static int i;
     
    static void a() {
        i = 0;
    }
 
    public static void main(String[] args) {
        for (i = 0; i < 5; i++) {
            a();
            System.out.println(i);
        }
    } 
}

~~~

다음과 같은 예제를 실행시키면 i가 for문의 조건을 만족시키지 못하고 0으로 계속 할당 되기 때문에 0을 계속 출력하게 된다. 이를 해결하는 방법이 2가지가 있다. 첫 번째로는 a() 메소드에서 int i = 0;으로 선언하는 방법이다. a()메소드의 i=0의 의미는 클래스 변수 i에 0을 할당함을 뜻한다. 따라서 변수형을 선언하게 되면 a()메소드 내부에서 사용되는 지역변수화가 되기 때문에 클래스 변수인 i와는 별개인 존재가 된다.
두 번째로 비슷한 원리로 main 메소드에서 변수 i에 변수형을 선언해 주면 된다. 클래스 변수는 클래스에 속한 모든 메소드에서 접근이 가능하지만 메소드 내부에서 사용되는 지역변수보다는 우선순위가 낮다. 따라서 같은 i라는 이름의 변수가 주어졌을때 클래스 변수 i 값인 0보다, main 메소드의 지역변수 i가 적용되기 때문이다. 
다만 주의해야할 부분이 있다. main 메소드에서 i에 변수형을 선언할 때 for문 내에서 for(int i = 0; i<5; i++) 선언할 경우 변수 i는 반복문 안에서만 유효하다.

~~~java

public class practice {
    public static void main(String[] args) {
        for (int i = 0; i < 5; i++) {
            System.out.println(i); // 가능
        }
        // System.out.println(i); // 불가능
    }
}
~~~

이 예제와 같이 반복문 안에 선언된 i는 같은 메소드 내부라도 반복문 안에서만 사용 가능하고, 반복문 밖에서는 사용할 수 없다.

~~~java

class pt {
    int v = 10;

    void m() {
        int v = 20;
        System.out.println(v);
        System.out.println(this.v);
    }
}

public class practice {

    public static void main(String[] args) {
        pt c1 = new pt();
        c1.m();
    }

}

~~~

위 예제는 한 메소드 안에서 지역변수와 클래스 변수 둘 다에 접근하는 방법을 보여준다. i는 클래스 변수로는 10이 할당되어있고, m()메소드에서는 지역변수로 20으로 할당되어 있다. 이 경우 그냥 v를 호출하게 되면 우선순위가 높은 지역변수인 20이 출력되지만, 변수 앞에 this.을 붙여주면 해당하는 메소드가 속한 클래스 변수에 접근할 수 있게 해준다.


## 생성자 초기화

객체를 통해 인스턴스를 생성할때 기본 값을 할당해 초기화 할 수 있다.

~~~java

class Calc {
    int left, right;

    public Calc(int left, int right) { // 생성자
        this.left = left;
        this.right = right;
    }

    public void sum() {
        System.out.println(this.left + this.right);
    }

    public void avg() {
        System.out.println((this.left + this.right) / 2);
    }
}

public class practice {

    public static void main(String[] args) {

        Calc c1 = new Calc(10, 20); // 생성자 = Calc(10,20)
        c1.sum();
        c1.avg();

        Calc c2 = new Calc(20, 40); // // 생성자 = Calc(20,40)
        c2.sum();
        c2.avg();
    }

}

~~~

위 예제와 같이 Calc라는 클래스를 통해 인스턴스 c1과 c2를 생성할때 생성자를 통해 인스턴스 변수의 기본 값들을 할당할 수 있다. 이는 인스턴스를 사용하는데 있어 필요한 변수를 미리 정의해 변수가 할당되지 않았을 경우 발생할 수 있는 에러를 방지하는 효과를 가진다. 
생성자의 이름은 클래스의 이름과 동일하게 하여 사용한다.


## 상속

클래스를 통해 여러 인스턴스를 생성해 코드를 재활용 할 수 있다. 그렇다면 기존 클래스에서는 없던 기능을 인스턴스에 추가하고 싶으면 어떻게 해야 할까? 제일 간단한 방법은 클래스에 해당 기능을 직접 추가하면 된다. 하지만 이 경우 다른 인스턴스에서는 필요가 없는 경우가 발생할 수 있으므로 다른 방법을 찾아야 한다. 여기서 사용될 수 있는게 상속이다.

~~~java

class Calc {
    int left, right;

    public void setOprands(int left, int right) {
        this.left = left;
        this.right = right;
    }

    public void sum() {
        System.out.println(this.left + this.right);
    }

    public void avg() {
        System.out.println((this.left + this.right) / 2);
    }
}

class SubstractionableCalc extends Calc {
    public void substract() {
        System.out.println(this.left - this.right);
    }
}

public class practice {

    public static void main(String[] args) {

        SubstractionableCalc c1 = new SubstractionableCalc();
        c1.setOprands(10, 20);
        c1.sum();
        c1.avg();
        c1.substract();
    }

}

~~~

위 예제에서 class SubstractionableCalc extends Calc 이 상속을 사용하는 부분이다. SubstractionableCalc라는 클래스에 기존의 Calc 클래스를 상속받고 추가적인 메소드를 작성하였다. 즉 SubstractionableCalc 클래스에는 기존의 Calc 클래스에 작성한 메소드가 추가된 상태이다. 이런 방법으로 클래스에 직접적인 작성 없이 필요한 기능을 추가할 수 있다. 

그렇다면 상속으로 메소드를 추가한 인스턴스에서의 생성자는 어떻게 적용될 지 생각해볼 필요가 있다. 우선 클래스에 생성자가 따로 선언되어 있지 않으면 보이지 않지만 자동으로 기본적인 생성자가 만들어 진다. 따로 생성자를 선언한 경우 이 기본 생성자는 만들어 지지 않는다.

하위 클래스가 호출될때 자동으로 상위 클래스의 기본 생성자를 호출한다. 이 때 상위 클래스에 매개변수가 포함된 생성자가 선언되어 있다고 생각해 보자. 하위 클래스가 호출되어 상위 클래스의 기본 생성자를 호출하려 해도 상위 클래스에서는 이미 임의로 만들어진 생성자가 있기 때문에 기본 생성자가 만들어 지지 않아 에러가 발생한다. 이를 간단하게 해결하기 위해서는 상위 클래스에 기본 생성자를 추가해주면 된다.

~~~java

class Calc {
    int left, right;

    // public Calc() {
    // }

    public Calc(int left, int right) {
        this.left = left;
        this.right = right;
    }

    public void setOprands(int left, int right) {
        this.left = left;
        this.right = right;
    }

    public void sum() {
        System.out.println(this.left + this.right);
    }

    public void avg() {
        System.out.println((this.left + this.right) / 2);
    }
}

class SubstractionableCalc extends Calc {
    public SubstractionableCalc(int left, int right) {
        super(left, right);
    }

    public void substract() {
        System.out.println(this.left - this.right);
    }
}

public class practice {
    public static void main(String[] args) {
        SubstractionableCalc c1 = new SubstractionableCalc(10, 20);
        c1.sum();
        c1.avg();
        c1.substract();
    }
}

~~~

위 예제에서는 하위 클래스에서 상위 클래스의 생성자를 호출하는 방법인 super()를 사용한다. 이 방법을 사용하면 상위 클래스에 기본 생성자를 따로 선언해 주지 않아도 상위 클래스의 생성자를 호출해 초기화할 수 있다.


## overriding

기존의 상속은 상위 클래스의 메소드나 변수 등을 하위 클래스가 extends를 통해 접근할 수 있다. 추가적으로 하위 클래스에서 상위 클래스의 메소드와 동일한 이름을 가진 메소드를 정의하면서 기존 상위 메소드의 동작을 변경할 수 있다. 이렇게 재정의된 메소드는 상위 클래스의 메소드보다 높은 우선순위를 가진다. 이것을 overriding이라고 한다.


~~~java
 
class Calc {
    int left, right;
 
    public void setOprands(int left, int right) {
        this.left = left;
        this.right = right;
    }
 
    public void sum() {
        System.out.println(this.left + this.right);
    }
 
    public void avg() {
        System.out.println((this.left + this.right) / 2);
    }
}
 
class SubstractionableCalc extends Calc {
     
    public void sum() {
        System.out.println("실행 결과는 " +(this.left + this.right)+"입니다.");
    }
     
    public int avg() {
        return (this.left + this.right)/2;
    }
     
    public void substract() {
        System.out.println(this.left - this.right);
    }
}
 
public class practice {
    public static void main(String[] args) {
        SubstractionableCalcu c1 = new SubstractionableCalc();
        c1.setOprands(10, 20);
        c1.sum();
        c1.avg();
        c1.substract();
    }
}

~~~

위 예제에서 Calc클래스로 부터 상속받은 SubstractionableCalc클래스를 살펴보면 overriding의 사용방법을 볼 수 있다. sum()메소드는 상위 클래스에서는 left+right의 값을 리턴했다면 하위 클래스에서는 "실행 결과는 " +(this.left + this.right)+"입니다." 를 출력한다. sum()을 호출하게 되면 우선순위에 따라 하위 클래스의 sum()이 실행된다. 만약 상위 클래스와 하위 클래스에서 메소드가 중복될때 super.메소드()를 하위 클래스에 작성함으로써 해결할 수 있다.

+ overridng의 전제조건

메소드의 이름, 메소드 매개변수 숫자와 데이터 타입 및 순서, 메소드의 리턴 타입이 모두 같아야 한다. 이와 같은 조건들을 통틀어서 메소드의 서명(signature)라고 한다. 이 서명이 다르면 오류가 발생한다.