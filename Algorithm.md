# 数据结构与算法

## 1 基础算法



## 2 数据结构

### 链表

* 单链表

  >这里主要是用数组模拟单链表，我们一般用结构体模拟单链表
  >
  >链表由节点构成，每个节点保存了值和下一个元素的位置这两个信息。节点的表示形式如下：
  >
  >```C++
  >class Node{
  >public:
  >    int val;
  >    Node* next;
  >};
  >```
  >
  >使用这种方法，在创建一个值为x新节点的时候，语法是：
  >
  >```C++
  >Node* node = new Node();//中间有一个new关键字来为新对象分配空间。new的底层涉及内存分配，调用构造函数，指针转换等多种复杂且费时的操作。一秒大概能new1w次左右。
  >node->val = x
  >```
  
  ```c++
  #include <iostream>
  #include <cstring>
  using namespace std;
  
  const int N = 100010;
  int e[N], ne[N], head, idx;
  
  //用数组模拟链表，所有的点，只要是插入操作都是顺着存的，一个接着一个，用ne[]来表示实际链表的顺序
  
  void init(){
      head = -1, idx = 0;
      // head表示头指针，刚初始化，为-1表示空，后面应当指向第一个结点的指针
      // idx 存储当前已经用到了哪个点的位置(这个idx非常难理解，需要好好理解)
  }
  
  void add_to_head(int x){
      e[idx] = x, ne[idx] = head, head = idx, idx ++;
  }
  
  void add(int k, int x){
      e[idx] = x, ne[idx] = ne[k], ne[k] = idx, idx ++;
  }
  
  void remove(int k){
      ne[k] = ne[ne[k]]; //数组模拟链表，ne[k]表示下标，下标就是指针
  }
  
  int main(){
      int m;
      cin >> m;
      init();
      int k, x;
      while(m --){
          char op;
          cin >> op;
          if (op == 'H'){
              cin >> x;
              add_to_head(x);
          }
          else if (op == 'I'){
              cin >> k >> x;
              add(k - 1, x);
          }
          else{
              cin >> k;
              if (k == 0) head = ne[head];
              else remove(k - 1);
          }
      }
      for (int i = head; i != -1; i = ne[i]) 
          cout << e[i] << ' ' ;
      cout << endl;
      return 0;
  }
  ```
  
* 双链表

  >需要注意的（基本和单链表相似）
  >
  >1. 哨兵结点的使用，0，1位置，不存数，但是存指针
  >2. 输出的时候，从r[0]开始输出

  ```C++
  #include <iostream>
  #include <algorithm>
  #include <cstring>
  using namespace std;
  
  const int N = 100010;
  int e[N], l[N], r[N], idx;
  
  void init(){
      //initalize:0为left,1为right;这里是哨兵,不存储值,但是存贮了指针
      l[1] = 0;
      r[0] = 1;
      idx = 2;
  }
  
  void add(int k, int x){
      // 在k的右侧add一个结点
      e[idx] = x;
      l[idx] = k;
      r[idx] = r[k];
      l[r[k]] = idx;
      r[k] = idx;
      idx ++;
  }
  
  void remove(int k){
      // 删除了第k个数
      r[l[k]] = r[k];
      l[r[k]] = l[k];
  }
  
  int main(){
      int m;
      cin >> m;
      init();
      while(m--){
          string op;
          int k, x;
          cin >> op;
          if(op == "L"){
              cin >> x;
              add(0, x);
          }
          else if(op == "R"){
              cin >> x;
              add(l[1], x);
          }
          else if(op == "D"){
              cin >> k;
              remove(k + 1);
          }
          else if(op == "IR"){
              cin >> k >> x;
              add(k + 1, x);
          }
          else{
              cin >> k >> x;
              add(l[k + 1], x);
          }
      }
      for (int i = r[0]; i != 1; i = r[i]) cout << e[i] << " ";
      cout << endl;
      return 0;
  }
  ```

### 栈

* **数组模拟栈**

  >**要求**：实现一个栈，栈初始为空，支持四种操作：
  >
  >1. `push x` – 向栈顶插入一个数 xx；
  >2. `pop` – 从栈顶弹出一个数；
  >3. `empty` – 判断栈是否为空；
  >4. `query` – 查询栈顶元素。

  >增增减减的操作：x = 0
  >
  >* x ++ → 0， x →1
  >* <font color='red'>++ x → 1</font>，x → 1（++x先增加1，再返回x）

  ```c++
  #include <iostream>
  #include <cstring>
  using namespace std;
  
  const int N = 100010;
  int stk[N], tt, hh; // tt =  0
  
  int main(){
      int m;
      scanf("%d", &m);
      while(m --){
          string op;
          cin >> op;
          int x;
          if (op == "push"){
              scanf("%d", &x);
              stk[++ tt] = x; //stk[0]是不用来存放元素的，栈的元素是从tt=1开始存
          }
          else if (op == "pop") tt --; //这里的pop有点奇怪，不真的输出栈顶元素，而是从逻辑上删除栈顶元素，物理上它还是存在的
          else if (op == "empty") cout << (tt?"NO":"YES") << endl; //等价于 if(tt) Yes else No
          else cout << stk[tt] << endl;
      }
      return 0;
  }
  ```

* 表达式求值

  >中缀表达式：表达树的中序遍历
  >
  >后缀表达式：表达树的后序遍历

* **单调栈**

  >一般来说解决一种问题：给定一个长度为 NN 的整数数列，输出每个数左边第一个比它小的数，如果不存在则输出 −1。

  ```C++
  const int N = 100010;
  int stk[N], tt;
  
  int main(){
      int n; 
      scanf("%d", &n); 
      
      while(n --){
          int x;
          scanf("%d", &x); //读取速度会加快
          while(tt && stk[tt] >= x) tt --;
          if (tt) printf("%d ", stk[tt]);
          else printf("-1 ");
          stk[++ tt] = x;
      }
      return 0;
  }
  ```

### 队列

* 数组模拟队列

  >两种写法
  >
  >1. `tt=-1, q[++tt]=x, hh<=tt表示队列非空`
  >2. `tt=0, q[tt++]=x, hh<tt表示队列非空`

  ```C++
  const int N = 100010;
  int q[N], hh = 0, tt = -1; // hh表示头部，tt表示尾部
  
  int main(){
      // 从尾部插入数据，头部删除数据
      int m; 
      scanf("%d", &m);
      while(m --){
          string op;
          cin >> op;
          int x;
          if (op == "push"){
              scanf("%d", &x);
              q[++ tt] = x;
              
          }
          else if(op == "pop") hh ++;
          else if(op == "empty") cout << (tt >= hh?"NO":"YES") << endl;
          else cout << q[hh] << endl;
      }
      return 0;
  }
  ```

* 单调队列

  >解决：求滑动窗口里面的最大值和最小值
  >
  >这道题过于困难了，需要重写，中间那几个步骤看似简单，写起来复杂

  ```C++
  #include <iostream>
  using namespace std;
  
  const int N = 1000010; //看清楚数据范围
  int q[N], a[N];
  
  int main(){
      int n, k;
      scanf("%d%d",&n,&k);
      for (int i = 0; i < n; i ++) scanf("%d", &a[i]);
      
      //求最小值
      int hh = 0, tt = -1;
      for (int i = 0; i < n; i ++){
          if (tt >= hh && i - k + 1 > q[hh]) ++ hh;
          while (tt >= hh && a[q[tt]] >= a[i]) -- tt;
          q[++ tt] = i;
          if (i >= k - 1) printf("%d ", a[q[hh]]);
      }
      cout <<endl;
      
      //求最大值
      hh = 0, tt = -1;
      for (int i = 0; i < n; i ++){
          if (tt >= hh && i - k + 1 > q[hh]) ++ hh;
          while (tt >= hh && a[q[tt]] <= a[i]) -- tt;
          q[++ tt] = i;
          if (i >= k - 1) printf("%d ", a[q[hh]]);
      }
      cout <<endl;
      return 0;
  }
  ```


### KMP

* KMP字符串匹配

  >题目：给定一个模式串 S，以及一个模板串P，所有字符串中只包含大小写英文字母以及阿拉伯数字。模板串P在模式串S中多次作为子串出现。求出模板串P在模式串S中所有出现的位置的起始下标。
  >
  >需要注意的点：
  >
  >1. next[i]表示前i段的"部分匹配值"：前缀和后缀的最长共有元素的长度。
  >2. 核心思想：在每次失配时，不是把p串往后移一位，而是把p串往后移动至下一次可以和前面部分匹配的位置，这样就可以跳过大多数的失配步骤。而每次p串移动的步数就是通过查找next[ ]数组确定的。

  ```c++
  const int N = 100010, M = 1000010;
  int ne[N];
  char p[N], s[M];
  
  void build_next(int n, char p[]){ 
      //用递归的方法求next数组,len表示已经匹配的前缀长度
      for (int i = 1, len = 0; i < n;){
          if (p[i] == p[len]){
              len ++;
              ne[i] = len;
              i ++;
          }
          else{
              if (len == 0){
                  ne[i] = 0;
                  i ++;
              }
              else len = ne[len - 1];
          }
      }
  }
  
  int main(){
      int n, m;
      cin >> n >> p >> m >> s;
      //这个读入很别致，for (int i = 1; i < n + 1; i ++) cout << p[i] << ' ';
      build_next(n, p); //ne:最大前缀=后缀的值
      
      for (int i = 0, j = 0; i < m;){ // i只循环一次，不退后
          if (p[j] == s[i]){
              i ++;
              j ++;
          } //匹配，指针后移
          else if (j > 0) j = ne[j - 1]; //遇到不匹配的，但是前面已经匹配了一些了，next跳转
          else i ++; //第一个就不匹配
          if (j == n) printf("%d ", i - n);
      }
  }
  ```

### Trie树

>1. 又叫做前缀树，作用是高校存储和查找字符串集合的数据结构
>
>2. `son[1][0]=2`表示1结点的一个值为a的子结点为结点2;如果`son[1][0] = 0`，则意味着没有值为a子结点。
>
>   >`son[1][0]`表示`e[1] = 0`, `ne[1] = 2`
>
>3. `idx`：保证了不同的idx值对应不同的结点，这样就可以利用idx把结构体内两个属性联系在一起了。因此，**<font color='red'>idx可以理解为结点</font>**
>
>   >题目中每一步的idx理解为指向当前结点的指针
>
>4. `char op[2]`; %s读取字符串会有一个’\0’作为字符串的结束 op[1]就是‘\0’
>
>   >提示：用字符串读字母

* Trie树的建立

  ```c++
  const int N = 100010;
  int son[N][26], cnt[N], idx;
  char str[N];
  
  void insert(char *str){
      int p = 0;
      for (int i = 0; str[i]; i ++){
          int u = str[i] - 'a'; //字母转化为数字
          if(!son[p][u]) son[p][u] = ++ idx; //idx+1,存贮下一个结点的p位置
          p = son[p][u]; //结点已经存在，向下一个结点走
      }
      cnt[p] ++; //最后一步p已经是指向最后一个结点
  }
  
  int query(char *str){
      int p = 0;
      for (int i = 0; str[i]; i ++){
          int u = str[i] - 'a';
          if (!son[p][u]) return 0;
          p = son[p][u];
      }
      return cnt[p];
  }
  
  int main(){
      int m;
      scanf("%d", &m);
      char op[2]; //%s读取字符串会有一个’\0’作为字符串的结束 op[1]就是‘\0’
      while(m--){
          scanf("%s%s", op, str);
          if (op[0] == 'I') insert(str);
          else printf("%d\n", query(str));
      }
      return 0;
  }
  ```

* 变：

### 并查集

>功能：
>
>1. 将两个集合合并
>2. 询问两个元素是否在一个集合中
>
>基本原理：每一个集合用一棵树表示。树根的编号就是整个集合的编号，每一个结点存储它的父结点`p[x]`
>
>问题：
>
>1. 如何判断树根：`if (p[x] == x)`
>
>2. 如何求x的集合编号：`while (p[x] != x) x = p[x]`
>
>   >优化：**路径压缩**
>
>3. 如何合并两个集合：px是x的集合编号，py是y的集合编号，`p[px] = py`

```c++
const int N = 100010;
int parent[N]; //the parent of each node

void init(int n){
    for (int i = 0; i < n; i ++){
        parent[i] = i;
    }
}

int find(int x){
    //Query ancestor node
    //while (parent[x] != x) x = parent[x];
    if(parent[x] != x) parent[x] = find(parent[x]); //这个递归！！！实现了路径压缩
    return parent[x]; //只有祖宗结点的p[x] = x, 所以被返回表示这个集合的类
}

void query(int x, int y){
    if (find(x) == find(y)) puts("Yes");
    else puts("No");
}

int main(){
    int n, m, a, b;
    scanf("%d %d",&n,&m);
    init(n); //initialize
    char op[2];
    while(m --){
        scanf("%s%d%d", op, &a, &b);
        if (op[0] == 'M') parent[find(a)] = find(b);
        else query(a, b);
    }
    return 0;
}
```

>变形，有了需要维护的额外信息：
>
>* `size`:每一个集合中元素的个数，只有根结点的`size`是有意义的
>* ？

### 堆

>堆是一颗完全二叉树，除了最后一层，其他所有结点都是满的，最下面一层结点从左到右排列
>
>功能(核心在于`up`，`down`操作)：
>
>1. 插入一个树`heap[++ size] = x; up(size)`
>2. 求集合中的最小值`heap[1]`
>3. 删除最小值`heap[1] = heap[size --]; down(1)`
>4. 删除任意一个元素`heap[k] = heap[size --]; down(k); up(k)`
>5. 修改任意一个元素`heap[k] = x; down(k); up(k)`（后面两种STL中的堆是实现不了的）
>
>存储：用一个一维数组存储
>
>>本质是如何用一个数组存下一颗树
>>
>>规则：最左端是根节点，`x`的左儿子是`2x`，x的右儿子是`2x+1`

```c++
const int N = 100010;
int heap[N], mysize; //建的是小根堆，根节点小于左右儿子

void down(int x){
    int temp = x; // temp始终记录左右根三个中最小的
    if (2 * x <= mysize && heap[temp] >= heap[2 * x]) temp = 2 * x;
    if (2 * x + 1 <= mysize && heap[temp] >= heap[2 * x + 1]) temp = 2 * x + 1;
    if (temp != x){
        swap(heap[temp], heap[x]);
        down(temp);
    }
}

void up(int x){
    while(x / 2 && heap[x / 2] > heap[x]){
        swap(heap[x / 2], heap[x]);
        x /= 2;
    } 
}

int main(){
    int n, m;
    cin >> n >> m;
    mysize = n;
    for (int i = 1; i < n + 1; i ++) scanf("%d", &heap[i]); //直接读入
    for (int i = n / 2; i; i --) down(i); //建立heap,从n/2开始,i表示heap下标
    
    while(m --){
        cout << heap[1] << ' ';
        heap[1] = heap[mysize --];
        down(1);
    }
    return 0;
}
```

### 哈希表

* 哈希表的写法

  * 拉链法

  ```c++
  #include <iostream>
  #include <cstring>
  using namespace std;
  const int N = 10003; //这个数的取法有讲究
  int heap_map[N], e[N], ne[N], idx;
  /*
  h[]散列表保存头指针的下标，为-1表示还未拉出链表
  e[]保存值，ne[]保存前一个值的下标，idx是链表的索引
  */
  
  void insert(int x){
      int k = (x % N + N) % N;
      e[idx] = x;
      ne[idx] = heap_map[k];
      heap_map[k] = idx ++;
  }
  
  int find(int x){
      int k = (x % N + N) % N;
      for (int i = heap_map[k]; i != -1; i = ne[i]){
          if (e[i] == x) return 1;
      }
      return 0;
  }
  
  int main(){
      int n;
      scanf("%d", &n);
      memset(heap_map, -1, sizeof heap_map); //初始化,memset初始化时按照字节初始化
      while(n --){
          char op[2];
          int x;
          scanf("%s%d", op, &x);
          if (op[0] == 'I') insert(x);
          else find(x)? puts("Yes"): puts("No");
      }
      return 0;
  }
  ```

  * 开放寻址法

  ```C++
  void insert(int x){
      int k = (x % N + N) % N;
      //冲突：当前位置不空，并且不等于x
      while(h[k] != null && h[k] != x){
          k ++;
          if (k == N) k = 0;
      }
      return k;
  }
  ```

* 字符串的哈希方法

  >哈希函数的设计：对形如x~1~x~2~x~3~…x~n-1~x~n~的字符串，其**哈希值 = 字符串的ASCII码 × p的次方**
  >$$
  >(x_1p^{n-1} + x_2p^{n-2} + ...x_{n-1}p + x_n)\;mod\;Q
  >$$
  >其实字符串哈希最核心的一点就是，==将字符串看成一个P进制的数，哈希的过程就是将P进制的数转换成十进制的数==
  >
  >两点规定：
  >
  >1. 不能有映射为0的数，这里选择ASCII码
  >
  >2. P = 131 或 13331 and Q = 264
  >
  >   >可以避免99.99%的冲突
  >
  >前缀和公式：$h[i+1]=h[i]×P+s[i], i∈[0,n−1],i∈[0,n−1]$ h为前缀和数组，s为字符串数组
  >
  >区间和公式：$h[l,r]=h[r]−h[l−1]×P^{r−l+1}$
  >
  >>区间和公式的理解: ABCDE与ABC的前三个字符值是一样，只差两位，乘上P^2^把 ABC 变为 ABC00，再用 ABCDE - ABC00 得到DE的哈希值（有点末位补0的意思）
  >
  >`unsigned long long`，没必要取模了，因为这个变量类型就是64位的，如果超过的话自带mod 264功能
  >
  >>==这一点其实没搞懂==

  ```c++
  #include <iostream>
  using namespace std;
  
  typedef unsigned long long ULL;
  const int N = 100010, P = 131;
  char str[N];
  ULL hash_map[N], p[N]; //hash_map存储前缀的哈希值
  
  int get(int l, int r){
      return hash_map[r] - hash_map[l - 1] * p[r - l + 1];
  }
  
  int main(){
      int n, m;
      scanf("%d%d%s", &n, &m, str + 1);
      int l1, r1, l2, r2;
      //初始化
      p[0] = 1;
      for (int i = 1; i < n + 1; i ++){
          hash_map[i] = hash_map[i - 1] * P + str[i]; //str[i]是该字符的ASCII值
          p[i] = p[i - 1] * P;
      }
      while(m--){
          scanf("%d%d%d%d", &l1, &r1, &l2, &r2);
          (get(l1, r1) == get(l2, r2))? puts("Yes"):puts("No");
      }
      return 0;
  }
  ```


## 3 搜索和图论

|      | 数据结构 | 空间    |                |
| ---- | -------- | ------- | -------------- |
| DFS  | stack    | O(h)    | 不具最短路性质 |
| BFS  | queue    | O(2^h^) | 最短路         |

### DFS 深度优先搜索

>回溯
>
>剪枝

* 排列数字

  >递归的逻辑需要搞明白，`dfs(u)`执行到`dfs(u+1)`把剩下没有执行的压到栈里面，然后return的时候需要把栈里面所有的东西都执行完
  >
  >`state[i] = false`是回溯步骤，恢复案发现场
  >
  >`dfs`之前的是传入状态，`dfs`之后是回溯的时候执行的步骤

  ```C++
  const int N = 10;
  int n;
  int path[N], state[N];
  
  void dfs(int p){
      if (p > n){
          for (int i = 1; i <= n; i ++) cout << path[i] << ' ';
          puts("");
          return;
      }
      
      for (int i = 1; i <= n; i ++){
          if (!state[i]){
              path[p] = i;
              state[i] = true;
              dfs(p + 1);
              state[i] = false;
          }
      }
  }
  ```

* n-皇后问题

  >1. `g[i][i]`是char型，输出`g[i]`相当于char*，也就是输出字符串(相当于输出一行)
  >
  >   >和指针相关
  >
  >2. 对角线`dg[u+i]`，反对角线`udg[n−u+i]`中的下标`u+i`和`n−u+i`表示的是截距

  ```C++
  const int N = 10, M =20;
  char g[N][N];
  bool col[N], dg[M], udg[M];
  
  void dfs(int n, int u){
      if (u == n){
          for (int i = 0; i < n; i ++) puts(g[i]);
          //g[i][i]是char型，输出g[i]相当于char*，也就是输出字符串
          puts("");
          return;
      }
      
      int x = u;
      for (int i = 0; i < n; i ++){
          //剪枝
          if (!col[i] && !dg[x + i] && !udg[x - i + n]){
              col[i] = dg[x + i] = udg[x - i + n] = true;
              g[u][i] = 'Q';
              dfs(n, u + 1);
              g[u][i] = '.';
              col[i] = dg[x + i] = udg[x - i + n] = false;
          }
      }
  }
  ```

### BFS 广度优先搜索

* 走迷宫

  >1. `typedef pair<int, int> PII`
  >2. 手动模拟队列的注意点：队头队尾，判断队列不空的条件
  >3. 往上下左右走的简单写法`int dx[4] = {-1, 0, 1, 0}, dy[4] = {0, 1, 0, -1};`

  ```C++
  typedef pair<int, int> PII;
  const int N = 110;
  int g[N][N], d[N][N];
  int n, m;
  PII q[N * N]; //表示queue
  
  int bfs(){
      int tt = 0, hh = 0; //hh表示头部，tt表示尾部, 从尾部插入数据，头部删除数据
      memset(d, -1, sizeof d);
      q[0] = {0, 0};
      d[0][0] = 0;
      int dx[4] = {-1, 0, 1, 0}, dy[4] = {0, 1, 0, -1};
      while(hh <= tt){
          auto t = q[hh ++];
          for (int i = 0; i < 4; i ++){
              int x = t.first + dx[i], y = t.second + dy[i];
              if (x >= 0 && x < n && y >= 0 && y < m && g[x][y] == 0 && d[x][y] == -1){
                  d[x][y] = d[t.first][t.second] + 1;
                  q[++ tt] = {x, y};
              }
          }
      }
      return d[n - 1][m - 1];
  }
  ```

### 树与图的存储和遍历

* 存储：邻接表，最常用

  >数组建立邻接表
  >
  >>节点的编号是树中节点的值，范围是从1~n，用`e[i]`数组存储
  >>
  >>`ne[i]`的值是下标，是下标为`i`的节点的next节点的下标
  >
  >```C++
  >for(int i=1;i<=n;i++){
  >    	cout<<i<<":";
  >    	for(int j=h[i];j!=-1;j=ne[j]){
  >        		cout<<"->"<<e[j]; 
  >    	}
  >    	cout<<endl;
  >}
  >```
  
  ```c++
  int h[N], e[N * 2], ne[N * 2], idx;
  
  void add(int a, int b) {
      e[idx] = b, ne[idx] = h[a], h[a] = idx++;
  }
  ```
  
* 树的dfs模板

  ```C++
  // 需要标记数组st[N],  遍历节点的每个相邻的便
  void dfs(int u) {
      st[u] = true; // 标记一下，记录为已经被搜索过了，下面进行搜索过程
      for (int i = h[u]; i != -1; i = ne[i]) {
          int j = e[i];
          if (!st[j]) {
              dfs(j);
          }
      }
  }
  ```

* 树的bfs模板

  >二者相似之处
  >
  >1. 需要一个h[N]存储头指针
  >2. `e[N]`，`ne[N]`
  >
  >压栈，压的是没有被访问过的节点

  ```c++
  int bfs(){
      int hh = 0, tt = 0;
      d[1] = 0;
      q[0] = 1;
      while(hh <= tt){
          int j = q[hh ++];
          for(int i = h[j]; i != -1; i = ne[i]){
              int k = e[i]; 
              if (d[k] == -1){
                  d[k] = d[j] + 1; //d[k]存储k节点离起点的距离,并标记为访问过
                  q[++ tt] = k;
              }
          }
      }
      return d[n];
  }
  ```

### 拓扑排序

### 单源最短路

>从某个点（固定起点）到其他点的最小距离
>
>最短路问题的难点在建图：如何抽象出点和边

#### 边权都为正

>n是点的个数，m是边的个数

* 朴素Dijkstra-O(n^2^)

  >1. 稠密图，用邻接矩阵来存储
  >
  >2. 思路：
  >   1. 找dist最小的点
  >   2. 将该点加入集合,用该点来更新集合外的点的dist(松弛操作)

  ```c++
  #include <iostream>
  #include <cstring>
  using namespace std;
  const int N = 510, M = 100010;
  int g[N][N];
  int st[N]; //是否被搜索过
  int dist[N]; //记录每个点到1号点的距离
  
  int dijkstra(int n){
      memset(dist, 0x3f, sizeof dist); //初始化为最大值
      dist[1] = 0;
      
      for(int i = 0; i < n; i ++){
          int current_spot = -1;
          for (int j = 1; j < n + 1; j ++){
              if(!st[j] && (current_spot == -1 || dist[current_spot] > dist[j]))
               current_spot = j; //找到所有的点中没有划入集合的dist最小的点
          }
          
          st[current_spot] = true;
          for (int i = 1; i < n + 1; i ++){
              if(!st[i]){
                  dist[i] = min(dist[i], dist[current_spot] + g[current_spot][i]);
              }
          }
      }
      if (dist[n] == 0x3f3f3f3f) return -1;
      else return dist[n];
  }
  
  int main(){
      int n, m;
      cin >> n >> m;
      memset(g, 0x3f, sizeof g); //初始化为无穷
      //自环肯定不会走
      while(m --){
          int x, y, z; //从点x到点y的有向边
          scanf("%d%d%d", &x, &y, &z);
          g[x][y] = min(g[x][y], z);
      }
      cout << dijkstra(n) << endl;
  }
  ```

* 堆优化版Dijkstra-O(mlogn)

  > 1. 稀疏图，用邻接表来装，复习一下邻接表的输出
  > 2. stl小根堆，==**问题：小根堆PII按照大小排列，按照哪个大小排列**==
  
  ```c++
  #include <iostream>
  #include <cstring>
  #include <queue>
  using namespace std;
  typedef pair<int, int> PII;
  const int N = 1000010;
  int h[N], e[N], ne[N], idx; //存储图
  int w[N]; //边的权重
  int dist[N]; //距离
  bool st[N]; //是否被访问过
  
  void add(int x, int y, int z){
      e[idx] = y; w[idx] = z; //x->y:w
      ne[idx] = h[x]; h[x] = idx ++;
  }
  
  int dijkstra(int n){
      memset(dist, 0x3f, sizeof dist);
      dist[1] = 0;
      priority_queue<PII, vector<PII>, greater<PII>> heap;
      heap.push({0, 1}); //{distance, num}
      while(heap.size()){//heap is not empty
          auto curr_node = heap.top();
          heap.pop();
          int num = curr_node.second, distance = curr_node.first;
          if (st[num]) continue;
          st[num] = true;
          for (int i = h[num]; i != -1; i = ne[i]){
              int j = e[i]; //node_num
              if (dist[j] > dist[num] + w[i]){//what w[i]?
                  dist[j] = dist[num] + w[i];
                  heap.push({dist[j],j});
              } 
          }
      }
      if (dist[n] == 0x3f3f3f3f) return -1;
      else return dist[n];
  }
  
  int main(){
      memset(h, -1, sizeof h);
      int n, m;
      cin >> n >> m;
      while(m --){
          int x, y, z; scanf("%d%d%d", &x, &y, &z);
          add(x, y, z);
      }
      cout << dijkstra(n) << endl;
  }
  ```

#### 存在负权边

* **Bellman-Ford-O(nm)**

  >* Bellman—Ford算法的核心：对每一条边都进行松弛操作
  >
  >  每次松弛操作实际上是对相邻节点的访问(相当于广度优先搜索)，第n次松弛操作保证了所有深度为n的路径最短。
  >
  >  >可以想象这个图是1->2->3->4....->n这样一条直线。第一次迭代只有与原点相连的点才能被更新dist
  >
  >* 像bfs一层层的更新：从起点出发,每次迭代就是访问相邻节点.每次迭代都遍历所有边

  ```C++
  struct Edge{
      int a;
      int b;
      int w;
  }e[M];
  
  int bellman_ford(){
      memset(dist, 0x3f, sizeof dist);
      dist[1] = 0;
      for (int i = 0; i < k; i ++){ //k次更新，为什么是k次更新对应的就是k条边
          memcpy(back, dist, sizeof dist);
          for (int j = 0; j < m; j ++){
              int a = e[j].a, b = e[j].b, w = e[j].w;
              dist[b] = min(dist[b], back[a] + w);
          }
      }
      return dist[n];
  }
  ```

* SPFA-一般O(m)，最坏O(nm)

  >1. Bellman_ford算法会遍历所有的边，但是有很多的边遍历了其实没有什么意义，只用遍历那些到源点距离变小的点所连接的边即可，只有当一个点的前驱结点更新了，该节点才会得到更新
  >
  >   >用队列实现
  >
  >2. SPFA算法看上去和Dijstra算法长得有一些像但是其中的意义还是相差甚远的：
  >
  >   >Dijkstra算法中的state数组保存的是当前确定了到源点距离最小的点，且一旦确定了最小那么就不可逆了；SPFA算法中的state数组仅仅只是表示目前在队列中的点
  >
  >3. Bellman_ford算法可以存在负权回路，因为其循环的次数是有限制
  >     SPFA算法不可以，用了队列来存储，只要发生了更新就会不断的入队
  >
  >4. 求负环一般使用SPFA算法：用一个cnt数组记录每个点到源点的边数，一个点被更新一次就+1，一旦有点的边数达到了n那就证明存在了负环
  >
  >   >==**更新：**==
  >   >
  >   >```C++
  >   >dist[x] = dist[t] + w[i];
  >   >cnt[x] = cnt[t] + 1; //1-t已经走了cnt[t]步了，到x就再加上1
  >   >```

  ```C++
  int spfa(){
      queue<PII> queue;
      memset(dist, 0x3f, sizeof dist);
      dist[1] = 0;
      queue.push({0, 1}); //{distance, num}
      state[1] = true; //true means enter the queue
      while(queue.size()){
          auto curr_node = queue.front();
          queue.pop();
          int num = curr_node.second;
          state[num] = false;
          for (int i = h[num]; i != -1; i = ne[i]){
              int j = e[i];
              if (dist[j] > dist[num] + w[i]){
                  dist[j] = dist[num] + w[i];
                  if (!state[j]){
                      state[j] = true;
                      queue.push({dist[j], j});
                  }
              }
          }
      }
      return dist[n];
  }
  ```

### 多源(起点)汇(终点)最短路

>起点重点不确定

>解题思路：动态规划==没看明白==
>
>```
>假设节点序号是从1到n。
>假设f[0][i][j]是一个n*n的矩阵，第i行第j列代表从i到j的权值，如果i到j有边，那么其值就为ci,j（边ij的权值）。
>如果没有边，那么其值就为无穷大。
>
>f[k][i][j]代表（k的取值范围是从1到n），在考虑了从1到k的节点作为中间经过的节点时，从i到j的最短路径的长度。
>
>比如，f[1][i][j]就代表了，在考虑了1节点作为中间经过的节点时，从i到j的最短路径的长度。
>分析可知，f[1][i][j]的值无非就是两种情况，而现在需要分析的路径也无非两种情况，i=>j，i=>1=>j：
>【1】f[0][i][j]：i=>j这种路径的长度，小于，i=>1=>j这种路径的长度
>【2】f[0][i][1]+f[0][1][j]：i=>1=>j这种路径的长度，小于，i=>j这种路径的长度
>形式化说明如下：
>f[k][i][j]可以从两种情况转移而来：
>【1】从f[k−1][i][j]转移而来，表示i到j的最短路径不经过k这个节点
>【2】从f[k−1][i][k]+f[k−1][k][j]转移而来，表示i到j的最短路径经过k这个节点
>
>总结就是：f[k][i][j]=min(f[k−1][i][j],f[k−1][i][k]+f[k−1][k][j])
>从总结上来看，发现f[k]只可能与f[k−1]有关。
>```

* Floyd-O(n^3^)

  ```c++
  void floyd(){
      for (int k = 1; k <= n; k ++ )
          for (int i = 1; i <= n; i ++)
              for (int j = 1; j <= n; j ++)
                  d[i][j] = min(d[i][j], d[i][k] + d[k][j]);
  }
  ```

## 4 数学知识

### 质数

1. 质数的判断-试除法

   >注意其中的那一步优化，不推荐`sqrt`(慢)和`i * i <= n`（会溢出）

   ```C++
   bool is_prime(int n){
       if (n < 2) return false;
       for (int i = 2; i <= n / i; i ++){ //该步骤的细节
           if (n % i == 0) return false;
       }
       return true;
   }
   ```

2. 分解质因数

   >根据算术基本定理，不考虑排列顺序的情况下，每个正整数都能够以唯一的方式表示成它的质因数的乘积。
   >$$
   >n=p_1^{a_1} * p_2^{a_2} *p_3^{a_3}.....p_n^{a_n}
   >$$
   >时间复杂度：O($\sqrt[]{n}$)

   ```C++
   int divide(int n){
       for (int i = 2; i <= n / i; i ++){
           if (n % i == 0){
               int count = 0;
               while(n % i == 0){
                   n /= i;
                   count ++;
               }
               printf("%d %d\n", i, count);
           }
       }
       if (n > 1) printf("%d %d\n", n, 1);
   }
   ```

3. 筛质数

   * 直接筛（比较呆，基本不用）

   * 埃氏筛（**思想重要~什么思想**）

     >核心：从2开始筛去素数本身的倍数，向后第一个找到的一定是素数
     >
     >>证明：设第一个找到的是$p$，$2\sim p-1$都没能把$p$筛掉。**$p$无法被小于自身的所有素数整除，推出$p$是素数（L就是第n+1个素数）**。（如果$p$可以被小于自己的某个合数$r$整除，那么$r$可以表示为两个素数的乘积，p也一定能被素数整除
     >
     >复杂度：$O(nloglogn)$
   
     ```C++
     int is_primes(int n){
         bool isnp[n] = {false};
         int count = 0;
         for (int i = 2; i <= n; i ++){
             if (!isnp[i]){
                 count ++;
                 for (int j = i; j <= n; j += i)
                     isnp[j] = true;
             }
         }
         return count;
     }
     ```
   
   * 线性筛
   
     >核心：让每一个合数被其最小质因数筛到
     >
     >>1. 维护一个质数表
     >>
     >>2. 从头到尾遍历，第一个数是2，未被划掉，把它放进质数表，然后我们用2去乘质数表里的每个数，划掉它们
     >>3. 划掉的数也要遍历，只是不加入质数表。4：先划掉8，但我们不划掉12
     >
     >>**证明1**：实际上，对于$x$ ，我们遍历到质数表中的$p$，且发现$p|x$时，就应当停止遍历质数表。因为：设$x = pr(r≥p)$（$p$本身是$x$的最小质因数），那么对于任意$p'>p$，有
     >>$$
     >>p'x = pp'r=p(p'r)
     >>$$
     >>，说明$p'x$的最小质因数不是$p'$，我们不应该在此划掉它。
     >
     >>**证明2**：why每一个合数都会被筛过
     >
     >复杂度：$O(n)$
   
     ```C++
     int is_primes(int n){
         bool isnp[n] = {false};
         int count = 0;
         int primes[n] = {0};
         for (int i = 2; i <= n; i ++){
             if (!isnp[i]) primes[count ++] = i;
             for (int j = 0; primes[j] <= n / i; j ++){//primes[j] * i <= n
                 isnp[primes[j] * i] = true;
                 if (i % primes[j] == 0) break; //虽然两重循环，但是这里很容易break出去
             }
         }
         return count;
     }
     ```

### 约数

1. 试除法求约数

2. 约数个数

   >$$
   >n=p_1^{a_1} * p_2^{a_2} *p_3^{a_3}.....p_n^{a_n}
   >$$
   >
   >$$
   >d = p_1^{b_1} * p_2^{b_2} *p_3^{b_3}.....p_n^{b_n}
   >$$
   >
   >$$
   >0 ≤ b_i ≤ a_i
   >$$
   >
   >所以：**约数个数的公式$(a_1+1)(a_2+1)...(a_n+1)$**

   >代码上的细节：
   >
   >1. `map`存储，**这里采用数组方式插入数据**，例如`primes[1] = 0`
   >
   >2. 每乘一次就取余：`(a * b) % p = (a % p * b % p) % p `
   >
   >   >y总最后没有取余也能过，我为了保险起见还是取余了

   ```c++
   #include <iostream>
   #include<unordered_map>
   const int mod = 1e9 + 7;
   using namespace std;
   typedef long long LL;
   
   int main(){
       int num;
       cin >> num; 
       unordered_map<int, int> primes;
       while(num --){
           int x;
           scanf("%d", &x);
           for (int i = 2; i <= x/i; i++){
               while(x % i == 0){
                   primes[i] ++;
                   x /= i;
               }
           }
           if (x > 1) primes[x] ++;
       }
       LL ans = 1;
       for (auto prime: primes) 
           ans = ans * (prime.second + 1) % mod;
       ans %= mod; //这一步！！
       cout << ans << endl;
   }
   ```

3. 约数之和

   >公式：
   >$$
   >(p_1^0 + p_1^1 + ... p_1^{a_1})(p_2^0 + p_2^1 + ... p_2^{a_2})...(p_k^0 + p_k^1 + ... p_k^{a_k})
   >$$
   
   >等比数列求和：迭代
   ```c++
   LL ans = 1;
   for (auto prime: primes){
       int temp = 1;
       LL p = prime.first, a = prime.second;
       while(a --) temp = (temp * p + 1) % mod;
       ans = (ans * temp) % mod;
   }
   ans %= mod; //这一步！！
   ```

* 求最大公约数

  >辗转相除法
  >
  >**核心**：gcd(a,b) = gcd(b,a%b)
  >
  >证明：
  >    设a%b = a - kb 其中k = a/b(向下取整)
  >    若d是(a,b)的公约数：则知 d|a 且 d|b 则易知 d|a-kb 故d也是(b,a%b) 的公约数
  >    若d是(b,a%b)的公约数：则知 d|b 且 d|a-kb 则 d|a-kb+kb = d|a 故而d|b 故而 d也是(a,b)的公约数
  >    因此(a,b)的公约数集合和(b,a%b)的公约数集合相同，所以他们的最大公约数也相同

  ```C++
  int gcd_1(int x, int y){
      if (x % y == 0) return y;
      else{
          cout << x << ' ' << y << endl;
          return gcd_1(y, x % y);
      } 
  }
  
  int gcd_2(int x, int y){
      return y ? gcd_2(y, x % y): x;
  }
  ```

### 欧拉函数

> 欧拉函数：对于正整数$n$，欧拉函数是小于或等于$n$的正整数中与$n$互质的数的数目，记作$φ(n)$.

>证明：
>
>1. 欧拉函数是乘性函数，当$m,n$互质，$\phi(mn) = \phi(m) * \phi(n)$
>
>2. 根据唯一分解定理知$n=p_1^{a_1} * p_2^{a_2} *p_3^{a_3}.....p_n^{a_n}$。因此$φ(n)=φ(p_1^{a_1})∗…∗φ(p_x^{a_x})$
>
>   > 对于任意一项 $φ(p_s^{a_s})=p_s^{a_s}−p_s^{(a_s-1)}$
>   > 证明：在$1\sim p^k$中只有$p$的倍数与$p^k$不互质
>   >
>   > 所以 $φ(p_s^{a_s})= p_s^{a_s} - p_s^{a_s - 1} = p_s^{a_s}∗(1 − \frac{1}{p_s})$
>
>3. 所以
> $$
>   φ(n) =n∗\prod_{i = 1}^{n}(1−\frac{1}{p_i})
> $$

```c++
int phi(int x){
    int res = x;
    for (int i = 2; i <= x / i; i ++){
        if (x % i == 0){
            res = res / i * (i - 1) ; //这个写法，防止小数
            while(x % i == 0) x /= i;
        }
    }
    if (x > 1) res = res / x * (x - 1)  ;
    return res;
}
```

>质数`i`的欧拉函数即为`phi[i] = i - 1`：`1 ~ i−1`均与`i`互质，共`i−1`个
>
>==`phi[primes[j] * i]`分为两种情况：==
>
>1. ` i % primes[j] == 0`时：`primes[j]`是`i`的最小质因子，也是`primes[j] * i`的最小质因子，因此`1 - 1 / primes[j]`这一项在`phi[i]`中计算过了，只需将基数NN修正为`primes[j]`倍，最终结果为`phi[i] * primes[j]`。
>2.  `i % primes[j] != 0`：`primes[j]`不是`i`的质因子，只是`primes[j] * i`的最小质因子，因此不仅需要将基数`N`修正为`primes[j]`倍，还需要补上`1 - 1 / primes[j]`这一项，因此最终结果`phi[i] * (primes[j] - 1)`

```c++
void get_euler(int n){
    phi[1] = 1;
    for (int i = 2; i <= n; i ++){
        if (!isnp[i]){
            primes[count ++] = i;
            phi[i] = i - 1;
        } 
        for (int j = 0; primes[j] <= n / i; j ++){//primes[j] * i <= n
            isnp[primes[j] * i] = true;
            if (i % primes[j] == 0) {
                phi[primes[j] * i] = primes[j] * phi[i];
                break; //虽然两重循环，但是这里很容易break出去
            }
            phi[primes[j] * i] = phi[i] * (primes[j] - 1);
        }
    }
}
```

### 快速幂

>基本思路:
>
>1. 预处理出$a^{2^0},a^{2^1},a^{2^2},…,a^{2^{logb}}$这b个数
>
>2. 将$a^b$用$a^{2^0},a^{2^1},a^{2^2},…,a^{2^{logb}}$这b个数来组合，即组合成$a^b=a^{2^{x_1}}×a^{2^{x_2}}×…×a^{2^{x_t}}=a^{2^{x_1}+2^{x_2}+…+2^{x_t}}$，即用二进制表示
>
>   > 为什么$b$可用$a^{2^0},a^{2^1},a^{2^2},…,a^{2^{logb}}$这b个数来表示：二进制可以表示所有数
>
>注意：`b&1`就是判断b的二进制表示中第0位上的数是否为1，若为1，`b&1=true`；反之`b&1=false`

* 快速幂求法

  ```C++
  long long qmi(long long a, int b, int p){
      long long result = 1;
      while(b){
          if (b & 1) result = result * a % p; //判断二进制0号位是不是1
          b >>= 1;
          a = (long long)a * a % p; //这个mod p
      }
      return result % p;
  }
  ```

* 逆元

  >单位元：在一个集合中，对于某种运算$*$（代表通用运算的表示符号），如果对于任何的集合元素$a$和元素$e$运算，得到还是集合元素$a$本身，则称$e$为这个运算下的单位元。
  >
  >逆元：在一个集合中，对于某种运算$*$，如果任意两个元素的运算结果等于单位元，则称这两个元素互为逆元。
  >
  >模乘的单位元：$1(mod n)$
  >
  >模乘的逆元：$aa^{-1}\equiv 1(mod n)$

  >求解
  >
  >1. 模数是质数：费马小定理
  > $$
  >   a^{p-1} \equiv 1(mod\, p)
  > $$
  >
  > $$
  >   a * a^{p-2} \equiv 1(mod \, p)
  > $$
  >
  > $$
  >   x = a^{p-2}(mod\, p)
  > $$
  >
  >   >当$a$为$p$的倍数时，$ax \equiv 0(mod \, p)$，所以一定不存在
  >
  >2. 模数不是质数：扩展欧几里得定理

### 扩展欧几里得算法

>用于求解方程$ax+by=gcd(a,b)$的解
>
>1. 当$b=0$时，$ax+by=a$，故而$x=1,y=0$当
>
>2.  $b≠0$时，
> $$
>   gcd(a,b)=gcd(b,a\%b)
> $$
>   而
> $$
>   bx′+(a\%b)y′=gcd(b,a\%b)
> $$
>
> $$
>   bx′+(a−⌊\frac{a}{b}⌋∗b)y′=gcd(b,a\%b)
> $$
>
> $$
>   ay′+b(x′−⌊\frac{a}{b}⌋y′)=gcd(b,a\%b)=gcd(a,b)
> $$
>
>
>   故而
> $$
>   x=y′, y=x′−⌊\frac{a}{b}⌋∗y′
> $$
>
>   因此可以采取递归算法 先求出下一层的x′和y′再利用上述公式回代即可

```C++
int exgcd(int a, int b, int& x, int& y){//为什么是&,实际上就是址传递,在回溯的过程中改变
    if (b == 0){
        x = 1, y = 0;
        return a;
    }
    //cout << "递归:" << a << ' ' << b << ' ' << x << ' ' << y << endl;
    int gcd = exgcd(b, a % b, x, y);
    //cout << "回溯:" << a << ' ' << b << ' ' << x << ' ' << y << endl;
    int t = x;
    x = y, y = t - a / b * y;
    return gcd; 
}
```

### 中国剩余定理

### 高斯消元

>思想不难，但是算起来非常难算
>
>枚举每一列c，
>
>1. 找到当前列绝对值最大的一行
>2. 用初等行变换(2) 把这一行换到最上面（未确定阶梯型的行，并不是第一行）
>3. 用初等行变换(1) 将该行的第一个数变成 1（其余所有的数字依次跟着变化）
>4. 用初等行变换(3) 将下面所有行的当且列的值变成 0

```c++
#include<iostream>
#include <algorithm>
#include<cmath>
using namespace std;
const int N = 110;
const double eps = 1e-6;
int n;
double a[N][N];

void out(){ //debug代码
    for (int i = 0; i < n; i ++){
        for (int j = 0; j < n + 1; j ++) printf("%.2lf ", a[i][j]);
        cout << endl;
    }
    puts("");
}

int gauss(){
    int curr_row = 0, curr_col = 0;
    for (; curr_col < n; curr_col ++){
        int max_row = curr_row; //找到当前这一列，绝对值最大的一个数字所在的行号
        for (int i = curr_row; i < n; i ++){
            if (fabs(a[i][curr_col]) > fabs(a[max_row][curr_row]))
                max_row = i;
        }
        
        if (fabs(a[max_row][curr_col]) < eps) continue;//这一步
        
        for(int i = curr_col; i < n + 1; i ++) swap(a[curr_row][i], a[max_row][i]);
        for(int i = n; i >= curr_col; i --) a[curr_row][i] /= a[curr_row][curr_col];
        //把下面所有行的第curr_col都消为0
        for(int i = curr_row + 1; i < n; i ++)
            if (fabs(a[i][curr_col]) > eps){
                for (int j = n; j >= curr_col; j --)
                    a[i][j] -= a[curr_row][j] * a[i][curr_col];
            }
        curr_row ++; //本行工作做完，转下一行
    }
    
    if(curr_row < n){
        for (int i = curr_row; i < n; i ++)
            if (fabs(a[i][n]) > eps) return 2;
        return 1;
    }
    //阶梯型回代求解,比较难理解，需要好好思考一下
    for (int i = n - 1; i >= 0; i --)
        for (int j = i + 1; j < n; j ++)
            a[i][n] -= a[i][j] * a[j][n];
    return 0;
}

int main(){
    cin >> n;
    for (int i = 0; i < n; i ++){
        for (int j = 0; j < n + 1; j ++)
            cin >> a[i][j];
    }
    int result = gauss();
    if (result == 0){
        for (int i = 0; i < n; i ++){
            if(fabs(a[i][n]) < eps) puts("0.00"); //防止输出-0.00
            else printf("%.2lf\n", a[i][n]);
        }
    }
    else if (result == 1) puts("Infinite group solutions");
    else puts("No solution");
}
```

### 组合数

* 递推公式法，**适用于a,b都不算大，n很大**

  >`1 ≤ n ≤ 10000`
  >`1 ≤ b ≤ a ≤ 2000`

  ```c++
  int main(){
      int n;
      cin >> n;
      for (int i = 0; i <= 2000; i ++){
          for (int j = 0; j <= i; j ++){
              if (!j) c[i][j] = 1; //一个都不选，只有一种可能
              else c[i][j] = (c[i - 1][j] + c[i - 1][j - 1]) % mod; //递推公式
          }
      }
      while(n --){
          int a, b;
          scanf("%d%d", &a, &b);
          printf("%ld\n", c[a][b]);
      }
  }
  ```

* 快速幂

  >1. `fact[i] = (LL)fact[i - 1] * i % mod`: 关于LL 强制转换, 右边的fact[i - 1]会被转换成长整型,这个只是临时的
  >
  >   右边是长整型, 左边接收的是 int, 因为取模所以数值上不会超过int
  >
  >   隐式转化: long long转化成int时会自动取最低的32位
  >
  >2. 公式：
  > $$
  >   C_b^a=\frac{a!}{b!(a−b)!}=a!∗b!^{−1}∗(a−b)!^{−1}
  > $$

  ```C++
  const int N = 1e5 + 2, mod = 1e9 + 7;
  int fact[N], infact[N];
  
  int main(){
      fact[0] = infact[0] = 1;
      for (int i = 1; i < N; i ++){
          fact[i] = (long long)fact[i - 1] * i % mod;
          infact[i] = (long long)infact[i - 1] * qmi(i, mod - 2, mod) % mod;
      }
      
      int n;
      cin >> n;
      while (n --){
          int a, b;
          scanf("%d%d", &a, &b);
          printf("%ld\n", (long long)fact[a] * infact[b] % mod * infact[a - b] % mod);
      }
      return 0;
  }
  ```

* 卢卡斯定理：用于处理组合数$C_a^b$，$a, b$特别大，用阶乘处理会越界

  >证明过程：https://www.acwing.com/solution/content/26553/

  ```C++
  int qmi(int a, int b, int p){
      int result_qmi = 1;
      while(b){
          if (b & 1) result_qmi = (LL)result_qmi * a % p;
          //debug技巧：莫名其妙出现负的，说明越界了，加LL
          b >>= 1;
          a = (LL)a * a % p;
      }
      return result_qmi;
  }
  
  int C(int a, int b, int p){
      if (b > a) return 0; //边界条件
      int result = 1;
      for (int i = 1, j = a; i <= b; ++ i, -- j){
          result = (LL)result * j % p;
          result = (LL)result * qmi(i, p - 2, p) % p;
      }
      return result;
  }
  
  LL lucas(LL a, LL b, int p){
      if (a < p && b < p) return C(a, b, p);
      else return (LL)C(a % p, b % p, p) * lucas(a / p, b / p, p) % p;
  }
  ```

* 卡特兰数

  >给定$n$个 $0$ 和$n$个$1$，它们将按照某种顺序排成长度为$2n$的序列，求它们能排列成的所有序列中，能够满足任意前缀序列中$0$的个数都不少于$1$的个数的序列有多少个。答案对$10^9+7$取模
  >
  >解法：将$01$序列置于坐标系中，起点定于原点。若$0$表示向右走，$1$表示向上走，那么任何前缀中$0$的个数不少于$1$的个数就转化为，路径上的任意一点，横坐标大于等于纵坐标。题目所求即为这样的合法路径数量。
  >$$
  >C_{2n}^{n} - C_{2n}^{n - 1} = \frac{C_{2n}^{n}}{n + 1}
  >$$

### 容斥原理

>记$S_i$为$1\sim n$ 中能整除$p_i$的集合,那么根据容斥原理, 所有数的个数为各个集合的并集，计算公式如下
>$$
>⋃_{i=1}^mSi = S_1+…S_m−(S_1⋂S_2+…+S_{m−1}⋂S_m)+(S_1⋂S_2⋂S_3+…+S_{m−2}⋂S_{m−1}⋂S_m)+…+(−1)^{m−1}(⋂_{i=1}^mS)
>$$

>如何用代码表示每个集合的状态？这里使用的**二进制**，以$m = 4$为例，所以需要4个二进制位来表示每一个集合选中与不选的状态，$1101$表示选中集合$S_1,S_2,S_4$，故这个集合中元素的个数为$\frac{n}{p_1∗p_2∗p_4}$。因为集合个数是3个，根据公式，前面的系数为$(−1)^{3−1}=1$

```C++
int main(){
    cin >> n >> m;
    for (int i = 0; i < m; i ++) cin >> primes[i];
    int res = 0;
    for (int i = 1; i < 1 << m; i ++){ //<< 左移运算,向左进行移位操作,高位丢弃,低位补0
    //用二进制表示每一个状态
        int t = 1; //选中集合对应质数的乘积
        int s = 0; //选中的集合数量

        for (int j = 0; j < m; j ++){ //枚举每一个状态
            if (i >> j & 1){
                if ((long long)t * primes[j] > n){
                    t = -1;
                    break; //跳出这个状态
                }
                s ++;
                t *= primes[j];
            }
        }
        if (t == -1) continue; 
        if (s & 1) res += n / t; // s & 1用于判断是奇数还是偶数
        else res -= n / t;
    }
    cout << res << endl;
    return 0;
}
```

### 博弈论

* Nim游戏

  >先手必胜状态：先手操作完，可以走到某一个必败状态
  >先手必败状态：先手操作完，走不到任何一个必败状态
  >先手必败状态：a1 ^ a2 ^ a3 ^ ... ^an = 0
  >先手必胜状态：a1 ^ a2 ^ a3 ^ ... ^an ≠ 0

* 集合类Nim游戏：`sg函数`

  ```C++
  #include <iostream>
  #include <algorithm>
  #include <cstring>
  #include <set>
  using namespace std;
  
  const int N = 110, M = 10010;
  int n_s, n, m;
  int f[M], s[N]; //s存储数字集合, sgf存储每对石子值的sg函数值
  
  int sg(int x){
      if (f[x] != -1) return f[x]; //记忆化搜索,防止重新搜很多遍
      //因为集合是确定的,所以只要x出现在哪里,sg的值都是确定的
      set<int> S; //对于每一个数S都是一个新的集合,定义在函数内部的
      for (int i = 0; i < n_s; i ++){
          if (x >= s[i]) S.insert(sg(x - s[i]));
      }
      for (int i = 0; ; i ++) //看看i是不是在S中
          if (!S.count(i)) return f[x] = i;//return f[x] = i; return值就是i;
  }
  
  using namespace std;
  int main(){
      cin >> n_s;
      for (int i = 0; i < n_s; i ++) cin >> s[i];
      memset(f, -1, sizeof f);
      cin >> n;
      int res = 0;
      for (int i = 0; i < n; i ++){
          int x;
          cin >> x;
          res ^= sg(x);
      }
      res ? puts("Yes"): puts("No");
      return 0;
  }
  ```


## 5 动态规划

### 背包问题

#### 0-1背包问题

```C++
#include <iostream>
using namespace std;

const int N = 1005;
int volunm[N], value[N];
int f_1[N][N]; 
int f_2[N];
//状态f[i][j]定义:前i个物品,背包容量j下的最优解(最大价值)
//为什么是0-1背包问题,最后一个物品选or不选两种选择
/*状态转移方程:
1. 最后一个物品装不下: f[i][j] = f[i - 1][j]
2. 最后一个物品能装下: f[i][j] = max(f[i - 1][j], f[i - 1][j - volunm[i]] + value[i])
*/

int main(){
    int number, volunmTotal;
    cin >> number >> volunmTotal;
    for (int i = 1; i <= number; i ++) cin >> volunm[i] >> value[i];
    //二维循环情况
    for (int i = 1; i <= number; i ++){
        for (int j = 1; j <= volunmTotal; j ++){
            if (volunm[i] > j) f_1[i][j] = f_1[i - 1][j];
            else f_1[i][j] = max(f_1[i - 1][j], f_1[i - 1][j - volunm[i]] + value[i]);
        }
    }
    cout << f_1[number][volunmTotal] << endl;

    //一维循环优化
    for (int i = 1; i <= number; i ++){
        for (int j = volunmTotal; j >= volunm[i]; j --){
            f_2[j] = max(f_2[j], f_2[j - volunm[i]] + value[i]);
        }
    }
    cout << f_2[volunmTotal] << endl;
    return 0;
}
```

#### 完全背包问题

```C++
#include <iostream>
using namespace std;
const int N = 1005;
int volunm[N], value[N];
// int f[N][N];
// int f[N];

int main(){
    int number, volunmTotal;
    cin >> number >> volunmTotal;
    for (int i = 1; i <= number; i ++) cin >> volunm[i] >> value[i];
    // TLE代码
    for (int i = 1; i <= number; i ++)
        for (int j = 1; j <= volunmTotal; j ++)
            for (int k = 0; k * volunm[i] <= j; k ++)
                f[i][j] = max(f[i][j], f[i - 1][j - k * volunm[i]] + k * value[i]);
    
    cout << f[number][volunmTotal] << endl;
    
    // 递推关系式：f[i][j] = max(f[i][j - v] + w , f[i - 1][j]) 
    for (int i = 1; i <= number; i ++)
        for (int j = 1; j <= volunmTotal; j ++){
            if (volunm[i] > j) f[i][j] = f[i - 1][j]; //为什么这一步
            else f[i][j] = max(f[i - 1][j], f[i][j - volunm[i]] + value[i]);
        }
    cout << f[number][volunmTotal] << endl;
    
    // 再优化的写法
    for (int i = 1; i <= number; i ++)
        for (int j = 1; j <= volunmTotal; j ++)
            if (volunm[i] <= j) f[j] = max(f[j], f[j - volunm[i]] + value[i]);
    cout << f[volunmTotal] << endl;
    return 0;
}
```

#### 多重背包问题

* 暴力写法

  ```c++
  #include <iostream>
  using namespace std;
  const int N = 1005;
  int volumn[N], value[N], num[N];
  int f[N][N];
  
  int main(){
      int number, volumnTotal;
      cin >> number >> volumnTotal;
      for (int i = 1; i <= number; i ++) cin >> volumn[i] >> value[i] >> num[i];
      
      for (int i = 1; i <= number; i ++)
          for (int j = 1; j <= volumnTotal; j ++)
              for (int k = 0; k <= num[i] && k * volumn[i] <= j; k ++)
                  f[i][j] = max(f[i][j], f[i - 1][j - k * volumn[i]] + k * value[i]);
      
      cout << f[number][volumnTotal] << endl;
      return 0;
  }
  ```

* 二进制优化

  ```C++
  #include <iostream>
  using namespace std;
  const int N = 12010, M = 2010; //数据范围的确定
  int volumnPack[N], valuePack[N]; //逐一枚举最大是N * logS
  int f[M];
  
  int main(){
      int num, volumnTotal;
      cin >> num >> volumnTotal;
      int count = 0;
      while (num --){
          int volumn, value, number;
          cin >> volumn >> value >> number;
          int k = 1; 
          //对每一个物品，有n个，打包成n'组，每组里面物品的个数分别是2^0, 2^1...个
          while (k <= number){
              count ++;
              volumnPack[count] = volumn * k;
              valuePack[count] = value * k;
              number -= k;
              k *= 2; //倍增思想
          }
          if (number){
              count ++;
              volumnPack[count] = volumn * number;
              valuePack[count] = value * number;
          }
      }
  
      // dp的开始，这里转化为01背包问题
      for(int i = 1; i <= count; i ++)
          for (int j = volumnTotal; j >= volumnPack[i]; j --)
              f[j] = max(f[j], f[j - volumnPack[i]] + valuePack[i]);
              
      cout << f[volumnTotal] << endl;
  }
  ```


#### 分组背包问题

```C++
/*
f[i][j]:集合,表示从前i组里面选,体积不超过j的选法
属性:最大值
集合的划分: 第i组选或者不选,选第i组,选哪个物品(要求:体积不超, 价值取max)
*/

#include <iostream>
using namespace std;
const int N = 110;
int group[N]; //组号
int volumn[N][N], value[N][N];
int f[N][N];

int main(){
    int groupNumber, volumnTotal;
    cin >> groupNumber >> volumnTotal;
    
    for(int i = 1; i <= groupNumber; i ++){
        cin >> group[i];
        for (int j = 1; j <= group[i]; j ++)
            cin >> volumn[i][j] >> value[i][j];
    }
    
    for (int i = 1; i <= groupNumber; i ++)
        for (int j = 1; j <= volumnTotal; j ++){
            f[i][j] = f[i - 1][j];
            for (int k = 1; k <= group[i]; k ++){
                if (volumn[i][k] <= j)
                    f[i][j] = max(f[i][j], f[i - 1][j - volumn[i][k]] + value[i][k]);
            }
        }
    cout << f[groupNumber][volumnTotal] << endl;
    return 0;
}
```

