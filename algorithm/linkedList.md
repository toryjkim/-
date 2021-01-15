# 연결리스트
 연결리스트를 공부하며 ... 삽질했던 내용들을 기록한다.
```
// 연결리스트의 기본 구조로 head만 지정해주면 head를 통해 다음 노드로 이동할 수 있다.
// 즉 코드상으로 LinkedList 클래스는 단지 head만 저장하고있으면 충분하다. 
class LinkedList{
    constructor(key, value){
      this.head = new Node(key, value);
    }
}
// 연결리스트를 이루는 요소인 노드이며 key, value, next를 속성으로 가지고 있으며, next에는 다음 Node를 저장한다.
class Node{
    constructor(key, value){
      this.key = key;
      this.value = value;
      this.next = null;
    }
}
```

여기까지는 매우 쉽다. 하지만 ....

탐색, 추가, 삭제 기능을 구현해 보자!
## 1. 기능구현
### 1.1. 탐색기능(난이도 쉬움)
```
search(key){
  let node = this.head;
  // 연결리스트의 head부터 null이면 탐색할 필요가 없으니 while문은 바로 건너뛰고 return false;
  // while의 node들을 검사하여 key 값과 일치하는 node가 있으면 그 node를 반환한다.
  // 없다면 node에 다음 노드를 저장한다.
  while(node !== null){
    if (node.key === key) {
      return node
    } else {
      node = node.next;
    }
  }
  return false;
}
```
### 1.2. 추가(난이도 중..?)
기본적으로 노드를 마지막 노드 위에 추가하되.. 만약 key값이 이미 존재하면 기존 노드를 수정해주고 싶었다(수정기능이 필요했기 때문에...)
그런데 보통 수정을 할 때는 그 값이 존재하는지 여부를 확인하고 데이터를 수정한다. 연결리스트의 경우 데이터를 탐색하는 시간이 길기 때문에
수정할 때마다 데이터를 탐색해야 한다면, 연결리스트는 수정이 잦은 곳에도 어울리지 않겠다 라는 생각이 들었다. 오직 삽입과 삭제에서의 왕...
실질적으로 하드웨어의 발전과 동적으로 크기를 조절하는 배열을 사용하기 시작한 이후로는 거의 사용이 안되는 듯 하다.
아무튼
- head => head가 null인 경우
- mid => 수정해야 할 경우
- last => 새로운 key 값이 들어온 경우

이 세가지를 모두 고려해서 한 메서드에 구현하려다 보니 개인적으로 구현하면서 조금 버거움을 느꼈다.

#### 1.2.1 첫번째 고민 ... node가 null이 아닐 때 까지 vs node.next 가 null이 아닐 때 까지.
node가 null이 아닐 때 까지 while문이 실행되면 node가 null인 while문을 빠져나와 무엇인가를 처리해야 할 때 문제가 발생한다.
```
append(key, value){
  let node = this.head;
  let newNode = new Node(key, value);
  // node의 head 자체에서 null이라면 head에 새로운 node를 추가해야 한다. 
  if (node === null) {
    this.head = newNode;
  } else {
    while(node !== null){
      if(node.key === key){
        node.value = value;
        return
      }
      node = node.next;
    }
    // while문에서 key와 일치하는 node가 없으면 마지막 node 다음에 새로운 노드를 추가해야함.
    node.next = newNode // 에러 발생
  }
}
```
while문을 모두 통과하면 node 는 null이 돼버리고, 마지막 노드에서 꼭 새로운 노드를 추가해줘야 하는 추가 함수의 경우... WTH...

따라서 node의 next가 null이 아닐때까지 while문을 실행해야 한다. 하지만... 코드가 깔끔하지 않다...
```
append(key, value){
  let node = this.head;
  let newNode = new Node(key, value);
  // node의 head 자체에서 null이라면 head에 새로운 node를 추가해야 한다. 
  if (node === null) {
    this.head = newNode;
  } else {
    while(node.next !== null){ // 위의 코드에서 node !== null에서 node.next !== null 로 수정됨
      if(node.key === key){
        node.value = value;
        return
      }
      node = node.next;
    }
    if(node.key === key){
      node.value = value;
      return
    }
    node.next = newNode;
  }
}
```
자 이렇게 코드를 짜면 node가 마지막일 때 즉 node.next가 null 값을 가질 때 while을 빠져나가므로 key값을 비교할 수 없다.
그래서 마지막에 다시한번 if문으로 비교해야한다.
아래는 중복코드를 줄이기 위한 발악으로 탄생한 결과이나... 썩 만족스럽지는 않다.
```
append(key, value){
  let newNode = new Node(key, value);
  let node = this.head;
  if( node === null ) {
      this.head = newNode;
  } else {
    while(true){
        if(node.key === key){
            node.value = value;
            return
        } else {
            if(node.next === null){
                break
            }
            node = node.next;
        }
    }
    node.next = newNode;
  }
}
```
### 1.3. 삭제 닌이도(중하...)
삭제메소드 구현시 고려해야할 점

1. head값 삭제
2. 그 이후 노드 삭제

이렇게 두 가지로 생각해 볼 수 있다. 중복을 고려한 append에 비해서는..? 쉽다.
```
let node = this.head;
if (node === null) {
  return false
} else if (node.key === key) {
  // head를 삭제해야 하는 경우
  this.head = this.head.next  
} else {
  while(node.next !== null) {
    if (node.next.key === key) {
      node.next = node.next.next;
      break;
    } else {
      node = node.next;
    }
  }
}
```
삭제의 경우 key에 해당하는 node의 이전 node와 해당하는 node의 다음 node를 연결해주면 된다. 다음과 같이 말이다. 
```
1 -> 2 -> 3

     2
1 ------->3
```
여기서 한가지 의문이 생길 수 있다. 삭제메서드에서도 node.next !== null 일 때 까지 while문이 실행되는데 마지막 노드는 실행이 안되고 결국 삭제도 못하지 않나?? => X 

위의 코드 중 node.next.key === key 를 봐 보자. while문 내에서는 node.next가 삭제될 대상이다. 
node가 마지막 전 노드 일 때, node.next를 통해 마지막 노드의 key 값을 확인할 것이다.
즉 마지막 전 노드에서 이미 마지막 노드를 확인하여 key값이 같으면 마지막 전노드의 next로 null을 덮어 씌울 것이기 때문에 마지막 노드는 삭제 될 것이다.

## 느낀점
연결리스트를 어디에 써먹을 수 있을까... 곰곰이 생각해보는 중이다.
