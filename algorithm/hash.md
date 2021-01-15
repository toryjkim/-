# hash table
최근 코드역량 강화를 위해 
코팅테스트 문제를 풀면서 for문을 통한 탐색이 원하는 결과를 가져다 줄수는 있지만, 2중 3중 for문 처럼 겹겹이 쌓이면 시간과 메모리의 효율성면에서 확실히 좋지 않은 결과를 확인할 수 있었다. 
예를 들어 A와 B 두 개의 배열을 비교할 때, A와 B를 순회하여 탐색하는 방법은 순차탐색의 경우 O(n^2) 의 시간 복잡도를 가진다.
하지만 hash table의 탐색만 놓고보면 O(1)의 시간복잡도로 n번 만 비교하면 되기 때문에 상당히 빠르게 비교할 수 있다.

오늘은 왜 hash table이 이렇게 빠른지 그리고 hash table의 동작 원리와 구현 방법에 대해 집중적으로 알아보려고 한다.

## why is hash table fast?
hash table은 Python의 dictionary, Js의 Map 이라고 생각하면 쉽다. key 값과 그 key에 해당하는 value를 가지고 있는 형태이다.
생각해보자 key값을 알고 있다면 그 값이 존재하는지 확인하기 위해 탐색할 필요가 없고 매칭되는 value를 가져오면 O(1)의 시간이 걸린다.
```
Map {
  key:value
}
```
예를 들어 두 배열 arr1 = ['kim','lee','park'], arr2 = ['lee','park'] 이 있다.
arr1 배열에 저장된 데이터가 arr2의 배열에 존재하는지 확인하기 위해
순차 탐색 알고리즘을 사용한다면 for 문을 통해 arr1을 순회하고 각각의 데이터에서 arr2의 순회를 통해 arr1과 arr2의 모든 데이터를 비교해야한다.
```
const arr1 = ['kim','lee','park'];
const arr2 = ['lee','park'];
for(personOfarr1 of arr1){
  for(personOfarr2 of arr2){
    if(personOfarr1 === personOfarr2)
      console.log(personOfarr1)
  }
}
```
하지만 만약 아래와 같이 key:value 구조로 데이터를 저장하고 key값으로 입력해서 value를 도출 할 수 있다면 
참가자를 순회하는 O(n)의 시간복잡도로 보다 더 빠르게 비교할 수 있게 된다.
```
participants = ['kim','lee','park'], completions = ['lee','park'];

let nonCompletion = ''
function hashing(key){  // 해시함수를 통해 key를 받아 해시값을 리턴한다. 실제로는 암호화 알고리즘이 사용됨.
  if(key === 'kim'){
    return 123;
  }else if(key === 'lee'){
    return 456;
  }else if(key ==='park'){
    return 789;
  }
}

let hashTable = new Map([ // 해시테이블은 해시값을 색인(index)으로하여 key에 해당하는 value를 탐색하는 자료구조이다.
  [456, 'lee'],
  [789, 'park']
])

for(participant of participants){
  const key = hashing(participant);
  const value = hashTable.get(key);
  if(!value){
    nonCompletion = value;
    break;
  }
}
```
## collesion
hash table은 key값을 hashing하여 얻은 hash 값을 index로 사용하여 데이터를 검색하는 구조를 가지고 있다.
즉 hashing 과정에서 사용되는 알고리즘에 따라 결과 값이 정해지기에 입력값의 범위는 무한한대 비해 출력값은 결과적으로 유한한 범위를 가지게 된다. 예를 들어 1234567 과 2345987 두 개의 key가 있다. 이를 10으로 나누는 알고리즘을 사용하여 hashing하면 두 데이터의 index는 모두 7이 되고 이미 테이블의 7번 방에 두 개의 데이터가 들어가야 한다. 하지만 7로 끝나는 수가 얼마나 많겠는가? 셀 수 없을 정도로
많다. 이렇게 한 방에 두 개 이상의 데이터가 들어가게 되면 어떻게 될까? 이를 collision 충돌이 발생했다고 하는데, key 1234567에 해당하는 index 7과 2345987에 해당하는 index 7 모두 해시테이블의 7번 방에서 데이터를 불러와야 하기 때문에 이런 상황을 해결하는 몇 가지 방법이 있다.

### 1. Chaning
Buckets(데이터가 저장되는 공간) 한 index에 해당하는 Bucket을 연결리스트로 구성하여 충돌이 발생하면 하나씩 연결하는 방식.
### 2. Open Addressing
충돌이 일어나면 다른 버킷에 데이터를 저장한다.

하지만 충돌해결 방법에 앞서 최대한 충돌이 발생하지 않고 데이터들이 한쪽에 치우치지 않는 견고한 hashing 알고리즘을 사용하는 것도 중요하다. 또한 어떤 알고리즘을 쓰더라도 테이블의 크기가 작다면 그 만큼 충돌 확률이 높아지고 크다면 메모리 공간을 끝도없이 차지할 것이기에 테이블 크기를 적절하게 설정하는 것도 고려해야할 사항이다.
충돌에 대해 조금 더 깊이 이해하기 위해 적재율이라는 개념을 살펴보자. 적재율이란 해시 테이블의 크기 대비, 키의 개수를 말한다.
즉, 키의 개수를 K 해시 테이블의 크기를 T 이라고 했을 때 적재율은 K/N 이다. 따라서 적재율이 1을 초과한 해시 테이블의 경우는 반드시 충돌이 발생한다고 볼 수 있다.
충돌이 발생하지 않는 이상적인 해시 테이블의 경우 삽입, 삭제, 탐색 연산은 모두 O(1)로 수행되지만 충돌이 발생할 경우 그 버킷 안에서 다시 한번 탐색해야하기 때문에 최악의 경우 O(K)만큼 걸리게 된다. 따라서 해시 테이블의 중점사항은 앞서 말한것 처럼 충돌을 최소화하고 이를 위해 고르게 index를 만들어 내는 알고리즘을 선택하는 것이라 볼 수 있다.

간단하게 chaining을 통한 중복된 인덱스 값들을 연결리스트로 구현하여 저장하는 방법으로 해시 테이블을 구현해 보았다.
## let's make a hash table.
1. 데이터를 담을 공간인 (this.data)buckets과 bucket의 크기를 지정할 size를 

```
constructor(size){
  this.data = []; // buckets
  if (size) { // 해시 테이블의 크기
    this.size = size;
  } else {
    this.size = 100;
  }
  
  
```
hash table과 저장, 삭제, 검색 메소드를 구현해보자.
```
class HashTable{
  constructor(size){
    this.data = [];
    if (size) {
      this.size = size;;
    } else {
      this.size = 100;
    }
  }

  hashing(key){
    let hash = 0;
    for(let char of key){
      hash += char.charCodeAt();
    }
    return hash
  }

  convertToIndex(hash){
    return hash % this.size
  }

  get(key){
    const hash = this.hashing(key);
    const index = this.convertToIndex(hash);
    const list = this.data[index];
    if (list === undefined){
      // if that particular table index is undefined, linked-list is not exist,
      // therefore there is no key assgined.
      return false;
    } else if (list.search(key) === false){
      // the value doesn't exist in the linked-list
      return false;
    } else{
      return list.search(key).value;
    }
  }
  put(key, value){
    const hash = this.hashing(key);
    const index = this.convertToIndex(hash);
    const list = this.data[index];
    if (list === undefined) {
      this.data[index] = new LinkedList(key, value);
    } else {
      this.data[index].append(key, value);
    }
  }
  delete(key){
    const hash = this.hashing(key);
    const index = this.convertToIndex(hash);
    const list = this.data[index];
    if (list) {
      list.delete(key);
    }
    if (list && !list.head) {
      delete this.data[index];

    }
  }
}
```
