# Queue(큐)
큐를 js로 구현해보자.

Queue는 데이터가 입력된 순서로 출력되는 선형 자료구조이다. 

## methods
- enqueue: data를 queue안에 저장한다.
- dequeue: 저장된 data중 가장 오래된 data를 추출한다. => queue속 데이터는 삭제됨.


## 구현
```
class Queue {
  constructor() {
    this.data = [];
  }
  
  set enqueue(value) {
    if(!value){
      throw "Enter valid data"
    this.data.push(value);
  }
  
  dequeue() {
    return this.data.shift();
  }
}

let q = new Queue();
q.enqueue = 1;
q.enqueue = 2;
q.dequeue(); // 1
q.dequeue(); // 2
```
