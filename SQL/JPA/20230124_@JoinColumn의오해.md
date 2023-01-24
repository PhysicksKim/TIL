# 오해 : @JoinColumn(name = "MEMBER_ID") 할 때, name 속성은 join 해줄 Member쪽 column 이름을 알려주는거다?  

틀렸다  
@JoinColumn(name = "MEMBER_ID") 에서 name 값은 그냥 현재 Entity에서 column 이름을 지정해주는거다.  
Member로 부터 FK 매핑해주는거는, JPA가 알아서 Member에 있는 PK를 갖고와서 FK로 쓴다.  

> 출처 - [자바 ORM 표준 JPA 프로그래밍 - 기본편 질문답변](https://www.inflearn.com/questions/113969/joincolumn-%EC%9D%98-name-referencedcolumnname-%EC%97%90-%EB%8C%80%ED%95%B4-%EC%A7%88%EB%AC%B8-%EB%93%9C%EB%A6%BD%EB%8B%88%EB%8B%A4)  
> 여기서 Member와 team이 연관관계가 있는데요. 여기서 @JoinColumn의 name은 FK 이름입니다.
  
# 예시  
  
![image](https://user-images.githubusercontent.com/101965836/214232625-97afc2e3-8a10-477f-9059-2c41f6d786cc.png)  
  
이렇게 Member와 Team이 있다고 할 때  

```java
@Entity
public class Member {

  ...

  @JoinColumn(name = "TEAM_ID")
  private Team team;
  
}
```
```java
@Entity
public class Team {
  
  @Id 
  @Column(name = "TEAM_ID")
  private Long id;

}
```
이렇게 Member 쪽에서는 @JoinColumn 을 써서 name을 지정하고, Team 쪽에서는 @Column을 써서 name을 지정했다  
여기서 오해가 생기는 데  

### ❌ 오해 : "아~ @JoinColumn 의 name 속성을 통해서, Team 쪽에서 어떤 값을 FK로 잡을지 아는구나?"  
   
### ✅ FK 잡는거는 JPA가 알아서 그쪽 PK 갖고와서 잡아준다. @JoinColumn 의 name은 그냥 그 테이블에서 FK Column 에서 이름일 뿐이다.  
  
예를 들어  
```java
@JoinColumn(name = "MY_TEAM_ID")
private Team team;
```
이렇게 해도 정상적으로 FK가 Team쪽 PK인 TEAM_ID 와 연결된다.  
  
<br><br>

---
  
# 결론 : @JoinColumn의 (name = "") 값도, 그냥 @Column(name = "") 처럼 그 테이블에서 이름일 뿐이다. PK FK 이어주는거는 name과 무관하게 JPA가 알아서 한다.  
  
더 정확히 말하면  
referencedColumnName 라는 속성을 지정해주면 된다.  
근데 referencedColumnName 속성을 생략하면, JPA가 알아서 반대쪽 테이블의 PK 값으로 FK를 지정해준다.  
따라서 referencedColumnName를 일부러 지정해주지 않아도 된다.  

- 추가 첨언으로, 김영한님 답변에서  
> "referencedColumnName을 PK가 아닌 다른 컬럼에 직접 지정할 수도 있지만 정규화 관점에서 권장하지는 않습니다."  

라고 한다.  
[정규화 참고 - PK가 여러 키 값으로 구성된 복합키인 경우 2차 정규화의 대상이다](https://dog-foot-story.tistory.com/61)  
  




