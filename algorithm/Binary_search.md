# 이진탐색
이진탐색에 대해 간단하게 알아본다.

## 1. 시간복잡도
나는 머리가 좋은 편은 아니다. 반씩 줄어드는 이진탐색의 시간복잡도가 왜 O(logn)인지 이해가 되지 않았다.

내가 놓치고 있던 것은 시간 복잡도 빅오 표기법이 최악의 경우를 전제로 시간복잡도를 계산한다는 점이다.
예를 들어 선형 탐색의 경우 n번 부터 n-1 n-2 ..... 1 무조건 배열의 마지막까지 탐색을 하는 조건으로 n번 을 탐색한다.
시간 복잡도로 나타내면 O(n)이다.

하지만 이진탐색의 경우 n개의 숫자가 있을 때 몇 번을 탐색해야 하는지 선형탐색처럼 가시적으로 보이지 않는다. 
그래서 시간복잡도를 구하기 위해 가장 중요한 것이 무엇일까? 생각해보았다. 그리고 몇 번의 과정으로 언제까지 탐색을 해야하는가?를 파악했더니 실마리가 조금씩 풀렸다.

우선 이진탐색의 원리부터 파악하자.
1. n개의 정렬된(ex.왼쪽은 작은수 오른쪽은 큰수) 숫자를 반으로 나눠 그 가운데에 위치한 숫자와 내가 찾고자하는 숫자가 일치하는지 확인
2. 찾으려는 숫자가 가운데 숫자보다 큰 경우 가운데 숫자의 오른쪽으로 내려가 오른쪽 숫자를 기준으로 1번을 다시 실행
2-1. 찾으려는 숫자가 가운데 숫자보다 작은 경우 가운데 숫자의 왼쪽으로 내려가 왼쪽 숫자를 기준으로 1번을 다시 실행
3. 원하는 숫자가 나올 때까지 1번 2번 과정을 반복

이제 시간복잡도로 돌아와서 언제까지 몇 번의 과정을 반복해야 할까? 이진탐색트리의 탐색수는 전체 node의 반으로 계속 나눠진다. 그리고 더 이상 반으로 나눌수 없을 때가 바로 최악의 경우가 된다.(가장 깊이 내려갔을 때)

즉 1개의 숫자만이 남게되고 그 숫자가 찾고자하는 숫자일 때가 가능한 많은 과정을 반복한 것이다.
정리해보면 **n개의 데이터를 k번 반으로 나눴을 때 비교해야하는 숫자가 오직 1개가 남는 경우**라고 볼 수 있다.
일반식 n(1/2)^k = 1 으로 나타낼 수 있으며
이 식을 k 기준으로 바꾸면 k = log2n이 된다. 즉 숫자 n이 커질수록 k는 점점 더디게 증가하는 구조이다.
```
증명
n(1/2)^k = 1
n = 2^k
k = log2n
```

## 2. 구현
1. 이진탐색 트리를 구성할 Node와 Tree Class를 만든다.
- 각 Node는 left와 right 속성을 가지고 있으며, 각 속성에는 왼쪽 혹은 오른쪽에 위치할 Node의 주소가 입력된다.
- Tree 는 head(root) 주소를 가지고 있다.
```
class Node:
    def __init__(self):
        self.left = None
        self.right = None

class BST:
    def __init(self, head):
        self.head = head
```
    
2. 메소드
메소드의 경우 기본적으로 이진탐색 규칙을 따른다. (노드의 왼쪽은 작은 값, 오른쪽은 큰 값)
2.1. 삽입
- 현재 비교하고있는 노드와 입력된 값의 대소관계를 파악한다.
- 트리를 내려가 그 자리가 비어있는지 확인한다.
- 비어있다면 삽입, 비어있지 않으면 다시 위 과정을 반복한다.(비교할 노드를 내려온 노드로 변경)

```
...
def insert(self, value):
    current_node = self.head
    while(True):
        if value < current_node.value:
            if current_node.left != None:
                current_node = current_node.left
            else:
                current_node.left = Node(value)
                break
        else:
            if current_node.right != None:
                current_node = current_node.right
            else:
                current_node.right = Node(value)
                break
```

4. 탐색 메소드
삽입은 노드를 내려가 None인지를 판별하였고 다음 노드로 넘어가는 과정을 반복했다면, 비교 노드의 값과 찾는 숫자의 값이 일치하는 지 확인한 다음 대소관계를 비교해 다음 노드로 넘어간다.(다음 노드가 None 일때 까지)
- 비교 노드의 값과 찾는 숫자의 값 일치여부 확인
- 다르다면 대소관계 파악후 다음 노드를 비교노드로 변경
```
def search(self, value):
    current_node = self.head
    
    while(current_node != None):
        if current_node.value == value:
            return True
        elif value < current_node.value:
            current_node = current_node.left
        else:
            current_node = current_node.right
    else:
        return False
```

5. 삭제 메소드
- 공통 코드
```
def delete(self, value):
    # 1. 삭제하려는 노드가 있는지 탐색해야한다.
    # 2. 삭제할 노드가 있다면 부모노드와의 연결고리를 끊어야 한다.(삭제할 노드와 그 노드의 부모 파악)
    current_node = self.head
    parent_node = current_node
    while(current_node != None):
        if current_node.value == value:
            break
        elif value < current_node.value:
            parent_node = current_node
            current_node = current_node.left
        else:
            parent_node = current_node
            current_node = current_node.right
    else:
        return False
```            
삭제는 3가지 경우로 나뉜다.
- 1. 삭제하려는 노드가 Leaf 인경우(자식이 없음): 부모노드와의 대소 관계를 파악하고, 연결고리를 끊어낸다.
```
if current_node.left == None and current_node.right == None:
    # head의 값을 지울 때도 고려한다.
    if value == self.head.value:
        self.head = None
        return
    if value < parent_node.value:
        parent_node.left = None
        del current_node
    else:
        parent_node.right = None
        del current_node
```
- 2. 삭제하려는 노드가 1개의 자식을 가지는 경우: 
  2.1. 자식이 left/ right 어느쪽에 존재하는지에 따라 크데 두 가지 경우로 나눈다.
  2.2. 삭제하려는 노드가 부모로 부터 어느쪽을 타고왔는지 파악해서 연결고리를 끊어낸다.
```
elif current_node.left != None and current_node.right == None:
    if value == self.head.value:
        self.head = self.head.left
        return
    if value < parent_node.value:
        parent_node.left = current_node.left
    else:
        parent_node.right = current_node.left
elif current_node.left == None and current_node.right != None:    
    if value == self.head.value:
        self.head = self.head.right
        return
    if value < parent_node.value:
        parent_node.left = current_node.right
    else:
        parent_node.right = current_node.right
```
- 3. 삭제하려는 노드가 2개의 자식을 가지는 경우:  
1번은 노드를 삭제해도 아래쪽의 노들이 없기 때문에 이진트리가 유지된다. 2번의 경우도 노드를 삭제하고 하나의 연결된 노드가 대체하면 그만이다.  하지만 3번은 삭제될 노드부터 양방향으로 갈리기 때문에 삭제될 노드의 자리에 어떤값을 대체해야하는지 조금 복잡하다.

쉽게 생격하려면 하나의 노드를 기준으로 왼쪽은 어떤 노드도 전부 작은 값을 가지고 있다. 반대로 오른쪽은 큰 값을 가지고 있다.
이때 만약 삭제노드의 왼쪽에서 어떤 노드를 선택해서 대체한다면 왼쪽에 존재하는 어떤 노드보다 크거나 같은 노드를 대체할 때 트리의 규칙이 유지된다. 왜냐하면 삭제노드를 기준으로 왼쪽의 노드들은 삭제노드의 값보다는 작기 때문에 어떤 값을 선택해도 삭제노드를 기준으로 위쪽으로는 규칙을
유지한다. 그리고 가장 그 중 큰 값을 선택했을 때 아래쪽은 선택된 값보다 작게 된다. 따라서 바이너리 트리 규칙이 유지된다.

대체할 노드중 오른쪽을 선택해서 가장 작은 값으로 대체해 보는 실습
```
elif current_node.left != None and current_node.right != None:
    # 삭제할 노드의 오른쪽에서 가장 작은 값 찾기, 가장 작은 값의 부모 찾기
    lowest_node = current_node.right
    parent_lowest_node = lowest_node

    while(lowest_node.left != None):
        parent_lowest_node = lowest_node
        lowest_node = lowest_node.left
  
    if parent_lowest_node == lowest_node:
        # 작은 값과 작은값의 노드가 같은경우(삭제할 노드의 오른쪽 노드가 가장 작은 값이면)
        # 작은 값(노드)의 왼쪽만 삭제할 노드의 왼쪽과 연결하고 아래에서 부모를 이어주면 끝 
        lowest_node.left = current_node.left
    else:
        # 삭제할 노드의 오른쪽 노드에서 왼쪽을 타고가다 작은 값을 발견한 경우
        # 작은 값의 오른쪽 노드가 있는지 파악해서 작은 값의 부모 노드의 왼쪽과 연결
        # 없다면 작은 값의 부모 노드의 왼쪽은 None
        if lowest_node.right:
            parent_lowest_node.left = lowest_node.right
        else:
            parent_lowest_node.left = None
        # 작은 값의 왼쪽과 오른쪽 모두 삭제할 노드와 연결
        lowest_node.left = current_node.left
        lowest_node.right = current_node.right
    
    # 만약 삭제할 노드가 head면 부모노드가 없기 때문에 부모노드와 연결 시킬 필요가 없고, head 값만 바꾸면 된다.
    # head가 아니면 부모노드와의 대소관계를 파악해 부모노드와 연결한다.
    if current_node == self.head:
         self.head = lowest_node
    elif value < parent_node.value:
        parent_node.left = lowest_node
    else:
        parent_node.right = lowest_node 
```
        
      
## 3. 응용문제
숫자가 아닌 문자를 이진트리를 이용해 정렬하기 + 함수를 매개변수로 받기.

나의 답:
```
if s2[0] == s1[0]:
    return 0
elif s2[0] != s1[0]:
    return -1
else:
    return 1
```
분석:
insert 문에서  
> if s2[0] == s1[0]: return 0 # 반드시 s2[0] == s1[0]으로 해주어야 기존 노드의 값과 입력된 값의 앞자리를 비교할 수 있음

이렇게 코드를 작성하는 순간 a로 시작하는 문자는 insert에서 모두 오른쪽으로 정렬된다.

> else: return -1
> 
그리고 나머지는 0만 아니라면 어떤 값을 주어도 사실상 정답이 된다. (정렬의 차이)
search문에 입력된 한자리의 문자가 bst에 저장된 문자의 [0]번 인덱스와 같으면 0을 반환한다.
왜냐하면 아래처럼 search메소드는 comp가 입력되는 순간 comp가 0을 반환하면 True를 반환하고 그 외의 값은 comp가 반환하는 값에 따라
다음 노드로 넘어가게되고 노드가 None이 되는순간 False를 반환하기 때문이다.

```
if comp == 0:
    return True
elif comp < 0:
    current = current.left
else:
    current = current.right
```
하지만 그래도 문제를 낸 사람의 시점에서 정답은 comp가 0 보다 작을 때와 클 때를 구분하고 있다.
따라서 a가 아닌 다른 문자가 입력되면 왼쪽으로 보내는 것이 닶이라고 생각한다. (이하 값 왼쪽, 초과 값 오른쪽)

```
# ex) s1은 value(banana), s2는 apple(비교노드)
# s2 가 apple s1이 banana 라면 s2[0](a) < s2[1](b) 가 성립할 때 왼쪽으로 보낸다.
# 아닌 경우는 오른쪽으로 보낸다.
if s2[0] == s1[0]:
    return 0
elif s2[0] < s1[0]:
    return -1
else:
    return 1
```

**단점:** 

![image](https://user-images.githubusercontent.com/39623897/110408510-01170600-80c9-11eb-9852-b935c65195d0.png)

a의 왼쪽 자식노드는 모두 a이기 때문에 자식노드가 오른쪽 노드를 가질 수 없다.


```
class Node:
    def __init__(self, value):
        self.value = value
        self.right = None
        self.left = None

class BinarySearchTree:
    def __init__(self, comp=None):
        self.root = None
        self.comp = comp
    
    def insert(self, value):
        if self.root is None:
            self.root = Node(value):
            return
        current = self.root
        parent = None
        direction = None
        
        # 정렬 함수의 규칙에 따라 문자열을 비교한 후 left 혹은 right로 노드를 이동한다.
        # 내려갈 때마다 direction을 저장해서 비어있는 노드가 발견되면 direction을 참고해 부모노드와 연결시킨다.
        while current is not None:
            parent = current
            if self.compe is None:
                if value < current.value:
                    direction = 'left'
                    current = current.left
                else:
                    direction = 'right'
                    current = current.right
            else:
                if self.comp(value, current.value) < 0:
                    dirction = 'left'
                    current = current.left
                else:
                    direction = 'right'
                    current = current.right
                    
        if direction == 'left':
            parent.left = Node(value)
        else:
            parent.right = Node(value)
    
    def search(self, value):
        if self.root is None:
            return False
            
        current = self.root
        while current is not None:
            if self.comp is None:
                if value == current.value:
                    return True
                elif value < current.value:
                    current = current.left
                else:
                    current = current.right
            else:
                # comp가 0을 반환하면 True기 때문에 아래 프린트문을 고려하면 current.value[0] == value
                if comp(value, current.value) == 0:
                    return True:
                # 
                elif comp < 0:
                    current = current.left
                else:
                    current = current.right
                  
def comp_func(s1, s2):
    if s2[0] == s1[0]:
        return 0
    elif s2 < s1[0]:
        return -1
    else:
        return 1

bst = BinarySearchTree(comp_func)
items = ['apple', 'banana', 'delta', 'game']
for item in items:
    bst.insert(item)    

queries = ['a', 'b', 'c', 'd', 'e', 'f', 'g']
for query in queries:
    print(query, bst.search(query))
    
    
# a True
# b True
# c False
# d True
# e False
# f False
# g True
```    
    
    
    
    
    
  
  
