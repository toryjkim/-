# 이진탐색
이진탐색에 대해 간단하게 알아본다.
나는 머리가 좋은 편은 아니다. 반씩 나누기를 반복하는 작업인 이진탐색의 시간복잡도가 왜 O(logn)인지 이해가 되지 않았다.

내가 놓치고 있던 것은 시간 복잡도 빅오 표기법이 최악의 경우를 전제로 시간복잡도를 계산한다는 것이다.
예를 들어 선형 탐색의 경우 n번 부터 n-1 n-2 ..... 1 무조건 배열의 마지막까지 탐색을 하는 조건으로 n번 을 탐색한다.
시간 복잡도로 나타내면 O(n)이다.

하지만 이진탐색의 경우 n개의 숫자가 있을 때 몇 번을 탐색해야 하는지 선형탐색처럼 가시적으로 보이지 않는다. 
그래서 시간복잡도를 구하기 위해 가장 중요한 것이 무엇일까? 생각해보고, 몇 번의 과정으로 언제까지 탐색을 해야하는가?를 파악한다면 해답이 조금씩 풀려나갈 것이다.

우선 이진탐색의 원리부터 파악하자.
1. n개의 정렬된 숫자를 반으로 나눠 그 가운데에 위치한 숫자와 내가 찾고자하는 숫자가 일치하는지 확인
2. 찾으려는 숫자가 가운데 숫자보다 큰 경우 가운데 숫자의 오른쪽 부터 마지막 숫자까지를 기준으로 1번을 다시 실행
2-1. 찾으려는 숫자가 가운데 숫자보다 작은 경우 첫번째 숫자부터 가운데 숫자의 왼쪽 숫자까지를 기준으로 1번을 다시 실행
3. 원하는 숫자가 나올 때 까지 1번 2번 과정을 반복

이제 시간복잡도로 돌아와서 언제까지 몇 번의 과정을 반복해야 할까? 이진탐색의 탐색 수는 전체 node의 반으로 계속 나눠진다. 그리고 더 이상 반으로 나눌수 없을 때가 바로 최악의 경우이다.
즉 1개의 숫자만이 남게되고 그 숫자가 찾고자하는 숫자일 때가 가능한 많은 과정을 반복한 것이다.
이를 정리해보면 **n개의 데이터를 k번 반으로 나눴을 때 비교해야하는 숫자가 오직 1개가 남는 경우**라고 볼 수 있다.
일반식 n(1/2)^k = 1 으로 나타낼 수 있으며
이 식을 k 기준으로 바꾸면 k = log2n이 된다. 즉 숫자 n이 커질수록 k는 점점 더디게 증가하는 구조이다.
```
증명
n(1/2)^k = 1
n = 2^k
k = log2n
```

## 구현
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
3.1. 삽입
- 삽입 메소드의 경우 트리를 내려가며 Node가 None인 지점에 삽입한다.

```
...
def insert(self, value):
    current_node = self.head
    while(current_node != None):
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
- 탐색 메소드의 경우 트리를 내려가며 입력받은 값과 일치하는 노드를 찾는다.
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
- 0. 공통 코드
```
def delete(self, value):
    # 1. 삭제하려는 노드가 있는지 탐색해야한다.
    # 2. 삭제할 노드가 있다면 부모노드와의 연결고리를 끊어야 한다.
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
```            
삭제는 3가지 경우로 나뉜다.
- 1. 삭제하려는 노드가 Leaf 인경우(자식이 없음): 부모노드와의 대소 관계를 파악하고, 연결고리를 끊어낸다.
```
if current_node.left == None and current_node.right == None:
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
    if value < parent_node.value:
        parent_node.left = current_node.left
    else:
        parent_node.right = current_node.left
elif current_node.left == None and current_node.right != None:    
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
    lowest_node = current_node.right
    parent_lowest_node = current_node

    while(lowest_node.left != None):
        parent_lowest_node = lowest_node
        lowest_node = lowest_node.left
    
    if parent_lowest_node == lowest_node:
    # 삭제할 노드의 오른쪽노드가 가장 작은 값인 경우(오른쪽 노드의 왼쪽으로 이어지는 노드가 없다)
        lowest_node.left = current_node.left # lowest_node의 오른쪽은 노드가 있고 왼쪽이 없기 때문에
    else:
    # 삭제할 노드의 오른쪽노드에 왼쪽으로 노드 값이 존재한다.
        if lowest_node.right:
        # 가장 작은 노드의 오른쪽에 노드가 이어진 경우
            parent_lowest_node.left = lowest_node.right
        else:
            parent_lowest_node.left = None
        lowest_node.left = current_node.left
        lowest_node.right = current_node.right
    if value < parent_node.value:
        parent_node.left = lowest_node
    else:
        parent_node.right = lowest_node
    
        
      
        
        

    
    
    
    
    
    
    
    
    
    
  
  
