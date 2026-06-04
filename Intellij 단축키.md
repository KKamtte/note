
## 줄바꿈

작성 후 ; 를 적고 줄바꿈을 할 때 자동으로 마침표를 적어주고 줄바꿈

```Java
Member findMember = memberService.findOne(saveId).get()

해당 라인 아무 위치에서 command + shift + enter

Member findMember = memberService.findOne(saveId).get();
```
## static import

import 할 때 사용되는 메소드의 길이를 줄여줌

```Java
Assertions.assertThat(member.getName()).isEqualTo(findMember.getName());

Assertions 에 option + enter
Add on-demand static import for 'org.assertj.core.api.Assertions'

assertThat(member.getName()).isEqualTo(findMember.getName());
```

## 반환 값 바로 생성

반환 값이 있는 경우 앞에 클래스와 변수를 바로 생성

```Java
assertThrows(IllegalStateException.class, () -> memberService.join(member2));

맨 끝에 가서 option + command + v

IllegalStateException illegalStateException = assertThrows(IllegalStateException.class, () -> memberService.join(member2));
```

## 값을 implements 하기

특정 값을 implements 하였을 때 Override 하는 기능

```
public class JdbcMemberRepository implements MemberRepository {}

MemberRepository 가서 control + enter 후 implement method

override 된 값들이 나오게됨
```

## Refactor

특정 값을 리팩토링하여 단순화 할 때

```
private RowMapper<Member> memberRowMapper() {  
    return new RowMapper<Member>() {  
        @Override  
        public Member mapRow(ResultSet rs, int rowNum) throws SQLException {  
            Member member = new Member();  
            member.setId(rs.getLong("id"));  
            member.setName(rs.getString("name"));  
            return member;  
        }  
    };  
}

return new RowMapper 는 lambda 식 처리 가능
RowMapper 에서 option + enter 후 Replace with lambda

private RowMapper<Member> memberRowMapper() {  
    return (rs, rowNum) -> {  
        Member member = new Member();  
        member.setId(rs.getLong("id"));  
        member.setName(rs.getString("name"));  
        return member;  
    };  
}
```

특정 부분을 합칠 때

```
Dog dog = new Dog();  
Cat cat = new Cat();  
Caw caw = new Caw();  
  
Animal[] animalArr = {dog, cat, caw};

dog, cat, caw 를 배열 안에서 선언할 수 있음
option + command + N

Animal[] animalArr = {new Dog(), new Cat(), new Caw()};
```

메서드를 추출하여 만들 수 있음

```
for (Animal animal : animalArr) {  
    System.out.println("동물 소리 테스트 시작");  
    animal.sound();  
    System.out.println("동물 소리 테스트 종료");  
}

for 작업 안의 부분을 메서드로 추출하기
범위 선택 후 option + command + M

for (Animal animal : animalArr) {  
    animalSound(animal);  
}

private static void animalSound(Animal animal) {  
    System.out.println("동물 소리 테스트 시작");  
    animal.sound();  
    System.out.println("동물 소리 테스트 종료");  
}
```


변수로 꺼내 바꿔 사용

```
private Book createBook() {  
    Book book = new Book();  
    book.setName("JPA");  
    book.setPrice(10000);  
    book.setStockQuantity(10);  
}

바꿀 부분을 선택하고 option + command + P

private Book createBook(String name, int price, int quantity) {  
    Book book = new Book();  
    book.setName(name);  
    book.setPrice(price);  
    book.setStockQuantity(quantity);   
}
```

## 바로가기


```
command + O // 특정 파일, 클래스 등을 바로 찾아갈 때 사용
command + E // 최근에 사용된 파일을 바로 찾아갈 때 사용
```

## 배열 순회

for loop 로 전체 순회 배열은 for-each 문을 활용할 수 있음

```
int[] numbers = {1, 2, 3, 4, 5};  
  
// 기존 for loopfor (int i = 0; i < numbers.length; i++) {  
    System.out.print(numbers[i] + " ");  
}  
  
System.out.println();  

// iter + enter (for-each 문 생성 단축어)

// for-each loop  
for (int number: numbers) {  
    System.out.print(number + " ");  
}
```

## 변수 값 한번에 바꾸기

특정 변수를 선택한 뒤 하위 모든 변수의 명을 바꾸는 방식

```
Student student1 = new Student();  
student1.name = "학생1";  
student1.age = 15;  
student1.grade = 90;

student1 선택 후 shift + F6

Student student2 = new Student();  
student2.name = "학생1";  
student2.age = 15;  
student2.grade = 90;
```


## 메서드 사용 추적

특정 메서드가 어디서 사용되는지 확인

```
MemberConstruct(String name, int age, int grade) {   
    this.name = name;  
    this.age = age;  
    this.grade = grade;  
}

MemberConstruct 에서 command + B 를 누르면 어디서 사용되는지 확인 가능 및 역 추적으로 어디서 생성되었는지 확인 가능
```

## 오버라이드

상속받은 자식 클래스에서 부모 클래스의 메서드를 오버라이드 할 경우

```
public void print() {  
    System.out.println("이름:" + name + ", 가격:" + price);  
}

해당 메서드를 자식 클래스에서 오버라이드할때 control + O 를 통해 오버라이드 바로 생성

@Override
public void print() {
    super.print();
}
```