# 1512.好数对（没做出来）

2020年9月17日

15:48

1512.好数对

给你一个整数数组 nums 。

如果一组数字 (i,j) 满足 nums[i] == nums[j] 且 i < j ，就可以认为这是一组 好数对 。

返回好数对的数目。 

 

示例 1： 

输入：nums = [1,2,3,1,1,3]

输出：4

解释：有 4 组好数对，分别是 (0,3), (0,4), (3,4), (2,5) ，下标从 0 开始

示例 2：

 

输入：nums = [1,1,1,1]

输出：6

解释：数组中的每组数字都是好数对

示例 3：

 

输入：nums = [1,2,3]

输出：0

 

 

提示：

 

1 <= nums.length <= 100

1 <= nums[i] <= 100

 

 

解决方法：转换思路，将循环遍历比较大小改为统计数字在数组中出现的次数

大佬代码：

```c++
class Solution {
public:
    int numIdenticalPairs(vector<int>& nums) {
       int ans = 0;
       vector<int> temp(100);
        for (int num : nums) {
            ans += temp[num - 1]++;
        }
        return ans;
    } 
};
```

结论：将每一个数出现的次数分别存在temp不同的位置中，如果出现次数为两次，则说明有一个好数对，ans结果就+1。

启发：拿到题目后要先思考能否进行简化。

![img](file:////tmp/wps-yzk/ksohtml/wpsq9Hi6B.jpg) 

![img](file:////tmp/wps-yzk/ksohtml/wpsTwzfht.jpg) 

 

 

 

# 665.非递减数列（没做出来）

Sunday, October 4, 2020

9:51 PM

 

给你一个长度为 n 的整数数组，请你判断在 最多 改变 1 个元素的情况下，该数组能否变成一个非递减数列。

 

我们是这样定义一个非递减数列的： 对于数组中所有的 i (0 <= i <= n-2)，总满足 nums[i] <= nums[i + 1]。

 

 

 

示例 1:

 

输入: nums = [4,2,3]

输出: true

解释: 你可以通过把第一个4变成1来使得它成为一个非递减数列。

示例 2:

 

输入: nums = [4,2,1]

输出: false

解释: 你不能在只改变一个元素的情况下将其变为非递减数列。

 

 

说明：

 

1 <= n <= 10 ^ 4

\- 10 ^ 5 <= nums[i] <= 10 ^ 5

 

 

思路：找向下拐点问题，找到向下拐点之后立刻拉回，按照拉回幅度最大的点进行拉回，如若拐点左边的点大于右边的点，则将拐点拉至和拐点右侧点一致，如果拐点右边的点大于左边的点，则将右边的点拉至拐点位置，保证完全消除拐点。

![img](file:////tmp/wps-yzk/ksohtml/wpsUdajsk.jpg) 

 

继续寻找拐点，若拐点数大于2，则说明无法达成题目要求，返回false。

```c++
class Solution {
public:
    bool checkPossibility(vector<int>& nums) {
        int greaterCount=0;
        for(int i=1;i<nums.size();i++){
            if(nums[i-1]>nums[i]){
                greaterCount++;
                if(greaterCount>=2)
                    return false;
                if(i-2>=0&&nums[i-2]>nums[i])
                    nums[i]=nums[i-1];
                else
                    nums[i-1]=nums[i];
            }
        }
        return true;
    }
};
```

 

启发：要用动态的思维来思考一些问题，不能静态的看待，不然永远发现不了真相。

 

 

# LCP 18. 早餐组合（没做出来）

Monday, October 5, 2020

8:17 PM

小扣在秋日市集选择了一家早餐摊位，一维整型数组 staple 中记录了每种主食的价格，一维整型数组 drinks 中记录了每种饮料的价格。小扣的计划选择一份主食和一款饮料，且花费不超过 x 元。请返回小扣共有多少种购买方案。

 

注意：答案需要以 1e9 + 7 (1000000007) 为底取模，如：计算初始结果为：1000000008，请返回 1

 

示例 1：

 

输入：staple = [10,20,5], drinks = [5,5,2], x = 15

 

输出：6

 

解释：小扣有 6 种购买方案，所选主食与所选饮料在数组中对应的下标分别是：

第 1 种方案：staple[0] + drinks[0] = 10 + 5 = 15；

第 2 种方案：staple[0] + drinks[1] = 10 + 5 = 15；

第 3 种方案：staple[0] + drinks[2] = 10 + 2 = 12；

第 4 种方案：staple[2] + drinks[0] = 5 + 5 = 10；

第 5 种方案：staple[2] + drinks[1] = 5 + 5 = 10；

第 6 种方案：staple[2] + drinks[2] = 5 + 2 = 7。

 

示例 2：

 

输入：staple = [2,1,1], drinks = [8,9,5,1], x = 9

 

输出：8

 

解释：小扣有 8 种购买方案，所选主食与所选饮料在数组中对应的下标分别是：

第 1 种方案：staple[0] + drinks[2] = 2 + 5 = 7；

第 2 种方案：staple[0] + drinks[3] = 2 + 1 = 3；

第 3 种方案：staple[1] + drinks[0] = 1 + 8 = 9；

第 4 种方案：staple[1] + drinks[2] = 1 + 5 = 6；

第 5 种方案：staple[1] + drinks[3] = 1 + 1 = 2；

第 6 种方案：staple[2] + drinks[0] = 1 + 8 = 9；

第 7 种方案：staple[2] + drinks[2] = 1 + 5 = 6；

第 8 种方案：staple[2] + drinks[3] = 1 + 1 = 2；

 

提示：

 

1 <= staple.length <= 10^5

1 <= drinks.length <= 10^5

1 <= staple[i],drinks[i] <= 10^5

1 <= x <= 2*10^5

 

 

思路：由于数目过大，如果用两层for循环，时间复杂度为O(mn)，会超时，先将两个数组同时从小到大排序，然后顺序遍历主食，逆序遍历饮品。因为主食的价格会逐渐增大，所以之前遍历过的饮品肯定是不适合的，则每次遍历饮品的变量都不需要回溯，节省了时间。

```c++
class Solution {
public:
    int breakfastNumber(vector<int>& staple, vector<int>& drinks, int x) {
        int ans=0;
        int mod = 1e9+7;
        int j=0;
        sort(staple.begin(),staple.end());
        sort(drinks.begin(),drinks.end());
        j=drinks.size()-1;
        for(int i=0;i<staple.size();i++){
            while(j>=0&&staple[i]+drinks[j]>x)
            j--;
            if(j==-1) break;
            ans+=j+1;
            ans%=mod;
            
        }
        return ans;
    }
};
```

 

 

 

# LCP 22. 黑白方格画（没做出来）

Monday, October 5, 2020

8:53 PM

 

小扣注意到秋日市集上有一个创作黑白方格画的摊位。摊主给每个顾客提供一个固定在墙上的白色画板，画板不能转动。画板上有 n * n 的网格。绘画规则为，小扣可以选择任意多行以及任意多列的格子涂成黑色，所选行数、列数均可为 0。

 

小扣希望最终的成品上需要有 k 个黑色格子，请返回小扣共有多少种涂色方案。

 

注意：两个方案中任意一个相同位置的格子颜色不同，就视为不同的方案。

 

示例 1：

 

输入：n = 2, k = 2

 

输出：4

 

解释：一共有四种不同的方案：

第一种方案：涂第一列；

第二种方案：涂第二列；

第三种方案：涂第一行；

第四种方案：涂第二行。 

示例 2： 

输入：n = 2, k = 1

输出：0 

解释：不可行，因为第一次涂色至少会涂两个黑格。

示例 3： 

输入：n = 2, k = 4 

输出：1

解释：共有 2*2=4 个格子，仅有一种涂色方案。

限制：

 

1 <= n <= 6

0 <= k <= n * n

 

```c++
class Solution {
public:
    int paintingPlan(int n, int k) {
        int x, y, ans = 0;
        if(n*n == k) return 1;
        for(x = 0; x <= n; ++x) 
        {
            for(y = 0; y <= n; ++y)
            {
                if((x+y)*n-x*y == k)
                {
                    ans += C(n,x)*C(n,y);
                }
            }
        }
        return ans;
    }
    int C(int n, int x)
    {
        int up = 1, t = x, down = 1;
        while(t--)
        {
            up *= n--;
            down *= x--;
        }
        return up/down;
    }
};
```

 

 

# 605.种花问题

Wednesday, October 7, 2020

8:10 PM

假设你有一个很长的花坛，一部分地块种植了花，另一部分却没有。可是，花卉不能种植在相邻的地块上，它们会争夺水源，两者都会死去。

 

给定一个花坛（表示为一个数组包含0和1，其中0表示没种植花，1表示种植了花），和一个数 n 。能否在不打破种植规则的情况下种入 n 朵花？能则返回True，不能则返回False。

 

示例 1:

 

输入: flowerbed = [1,0,0,0,1], n = 1

输出: True

示例 2:

 

输入: flowerbed = [1,0,0,0,1], n = 2

输出: False

注意:

 

数组内已种好的花不会违反种植规则。

输入的数组长度范围为 [1, 20000]。

n 是非负整数，且不会超过输入数组的大小。

 

思路：在数组前后各插入一个0，防止溢出，然后循环判断flowerbed[i]以及前后是否都为零即可。

 

 

```c++
 1 class Solution {

  2 public:

  3     bool canPlaceFlowers(vector<int>& flowerbed, int n) {

  4         if(n==0){

  5             return true;

  6         }

  7         flowerbed.insert(flowerbed.begin(),0);

  8         flowerbed.insert(flowerbed.end(),0);

  9         for(int i=1;i<flowerbed.size()-1;i++){

  10             if(flowerbed[i]==0){

  11                 if(flowerbed[i]-flowerbed[i-1]-flowerbed[i+1]==0){

  12                     n--;

  13                     flowerbed[i]=1;

  14                     if(n==0){

  15                          return true;

  16                     }

  17                 }

  18 

  19             }

  20         }

  21 

  22        return false;

  23     }

  24 };


```

 

 

 

# 75. 颜色分类（没做出来）

Wednesday, October 7, 2020

9:48 PM

给定一个包含红色、白色和蓝色，一共 n 个元素的数组，原地对它们进行排序，使得相同颜色的元素相邻，并按照红色、白色、蓝色顺序排列。

 

此题中，我们使用整数 0、 1 和 2 分别表示红色、白色和蓝色。

 

注意:

不能使用代码库中的排序函数来解决这道题。

 

示例:

 

输入: [2,0,2,1,1,0]

输出: [0,0,1,1,2,2]

进阶：

 

一个直观的解决方案是使用计数排序的两趟扫描算法。

首先，迭代计算出0、1 和 2 元素的个数，然后按照0、1、2的排序，重写当前数组。

你能想出一个仅使用常数空间的一趟扫描算法吗？

 

 

思路：此题有三种解法：

1.扫描一遍数组，记录0,1,2的个数，然后重写数组。

2.扫描一遍数组，将0放至数组最前端，再扫描一遍数组，将1放至中间。

3.扫描一遍数组，若遇到0则放在前面，若遇到2则放在后面，使用两个指针记录放至位置，当遇到2且交换后不为1时，需要将循环指针回退1，因为0和2都需要再次移动。

 

  

```c++
1 class Solution {

  2 public:

  3     void sortColors(vector<int>& nums) {

  4         int zeroFlag=0,twoFlag=nums.size()-1;

  5         if(nums.size()<2) return;

  6         for(int i=0;i<=twoFlag;i++){   

  7              if(nums[i]==0){

  8                  swap(nums[i],nums[zeroFlag]);

  9                  zeroFlag++;

  10              }  

  11               if(nums[i]==2){

  12                   swap(nums[i],nums[twoFlag]);

  13                   twoFlag--;

  14                   if(nums[i]!=1){

  15                       --i;

  16                   }

  17               }

  18         }

  19         

  20     }

  21 };


```

 

 

 

# 142. 环形链表 II（没做出来）

Saturday, October 10, 2020

8:59 PM

给定一个链表，返回链表开始入环的第一个节点。 如果链表无环，则返回 null。

 

为了表示给定链表中的环，我们使用整数 pos 来表示链表尾连接到链表中的位置（索引从 0 开始）。 如果 pos 是 -1，则在该链表中没有环。注意，pos 仅仅是用于标识环的情况，并不会作为参数传递到函数中。

 

说明：不允许修改给定的链表。

 

进阶：

 

你是否可以不用额外空间解决此题？

 

 

示例 1：

 

 

 

输入：head = [3,2,0,-4], pos = 1

输出：返回索引为 1 的链表节点

解释：链表中有一个环，其尾部连接到第二个节点。

示例 2：

 

 

 

输入：head = [1,2], pos = 0

输出：返回索引为 0 的链表节点

解释：链表中有一个环，其尾部连接到第一个节点。

示例 3：

 

 

 

输入：head = [1], pos = -1

输出：返回 null

解释：链表中没有环。

 

 

提示：

 

链表中节点的数目范围在范围 [0, 104] 内

-105 <= Node.val <= 105

pos 的值为 -1 或者链表中的一个有效索引

 

 

 

 

解法：

1.哈希表

思路与算法

一个非常直观的思路是：我们遍历链表中的每个节点，并将它记录下来；一旦遇到了此前遍历过的节点，就可以判定链表中存在环。借助哈希表可以很方便地实现。

  

```c++
1 class Solution {

  2 public:

  3     ListNode *detectCycle(ListNode *head) {

  4         unordered_set<ListNode *> visited;

  5         while (head != nullptr) {

  6             if (visited.count(head)) {

  7                 return head;

  8             }

  9             visited.insert(head);

  10             head = head->next;

  11         }

  12         return nullptr;

  13     }

  14 };

  15 
```

 

2.快慢指针

思路与算法

 

我们使用两个指针，fast 与 slow。它们起始都位于链表的头部。随后，slow 指针每次向后移动一个位置，而 fast 指针向后移动两个位置。如果链表中存在环，则 fast 指针最终将再次与 slow 指针在环中相遇。

 

如下图所示，设链表中环外部分的长度为 a。slow 指针进入环后，又走了 b 的距离与fast 相遇。此时，fast 指针已经走完了环的 n 圈，因此它走过的总距离为 a+n(b+c)+b=a+(n+1)b+nca+n(b+c)+b=a+(n+1)b+nc。

 

![img](file:////tmp/wps-yzk/ksohtml/wpsvCcdEb.jpg) 

根据题意，任意时刻，fast 指针走过的距离都为 slow 指针的 22 倍。因此，我们有

a+(n+1)b+nc=2(a+b)⟹a=c+(n−1)(b+c)

 

有了 a=c+(n-1)(b+c)a=c+(n−1)(b+c) 的等量关系，我们会发现：从相遇点到入环点的距离加上 n−1 圈的环长，恰好等于从链表头部到入环点的距离。

 

因此，当发现 slow 与 fast 相遇时，我们再额外使用一个指针 ptr。起始，它指向链表头部；随后，它和 slow 每次向后移动一个位置。最终，它们会在入环点相遇。

  

```c++
1 class Solution {

  2 public:

  3     ListNode *detectCycle(ListNode *head) {

  4         ListNode *slow = head, *fast = head;

  5         while (fast != nullptr) {

  6             slow = slow->next;

  7             if (fast->next == nullptr) {

  8                 return nullptr;

  9             }

  10             fast = fast->next->next;

  11             if (fast == slow) {

  12                 ListNode *ptr = head;

  13                 while (ptr != slow) {

  14                     ptr = ptr->next;

  15                     slow = slow->next;

  16                 }

  17                 return ptr;

  18             }

  19         }

  20         return nullptr;

  21     }

  22 };


```

 

 

 

# 204. 计数质数（超时）

Sunday, October 11, 2020

10:24 PM

统计所有小于非负整数 n 的质数的数量。

 

 

 

示例 1：

 

输入：n = 10

输出：4

解释：小于 10 的质数一共有 4 个, 它们是 2, 3, 5, 7 。

示例 2：

 

输入：n = 0

输出：0

示例 3：

 

输入：n = 1

输出：0

 

 

提示：

 

0 <= n <= 5 * 106

 

思路：使用厄拉多塞筛法

 

  

```c++
1 class Solution {

  2 public:

  3 int countPrimes(int n) {

  4     int count = 0;

  5     //初始默认所有数为质数

  6     vector<bool> signs(n, true);

  7     for (int i = 2; i < n; i++) {

  8         if (signs[i]) {

  9             count++;

  10             for (int j = i + i; j < n; j += i) {

  11                 //排除不是质数的数

  12                 signs[j] = false;

  13             }

  14         }

  15     }

  16     return count;

  17 }

  18 };
```

 

 

 

 

# 116. 填充每个节点的下一个右侧节点指针

Thursday, October 15, 2020

7:17 PM

给定一个完美二叉树，其所有叶子节点都在同一层，每个父节点都有两个子节点。二叉树定义如下：

 

struct Node {

 int val;

 Node *left;

 Node *right;

 Node *next;

}

填充它的每个 next 指针，让这个指针指向其下一个右侧节点。如果找不到下一个右侧节点，则将 next 指针设置为 NULL。

 

初始状态下，所有 next 指针都被设置为 NULL。

 

 

 

示例：

 

 

 

输  入：{"$id":"1","left":{"$id":"2","left":{"$id":"3","left":null,"next":null,"right":null,"val":4},"next":null,"right":{"$id":"4","left":null,"next":null,"right":null,"val":5},"val":2},"next":null,"right":{"$id":"5","left":{"$id":"6","left":null,"next":null,"right":null,"val":6},"next":null,"right":{"$id":"7","left":null,"next":null,"right":null,"val":7},"val":3},"val":1}

 

输出：{"$id":"1","left":{"$id":"2","left":{"$id":"3","left":null,"next":{"$id":"4","left":null,"next":{"$id":"5","left":null,"next":{"$id":"6","left":null,"next":null,"right":null,"val":7},"right":null,"val":6},"right":null,"val":5},"right":null,"val":4},"next":{"$id":"7","left":{"$ref":"5"},"next":null,"right":{"$ref":"6"},"val":3},"right":{"$ref":"4"},"val":2},"next":null,"right":{"$ref":"7"},"val":1}

 

解释：给定二叉树如图 A 所示，你的函数应该填充它的每个 next 指针，以指向其下一个右侧节点，如图 B 所示。

 

 

提示：

 

你只能使用常量级额外空间。

使用递归解题也符合要求，本题中递归程序占用的栈空间不算做额外的空间复杂度。

 

 

思路：

![img](file:////tmp/wps-yzk/ksohtml/wpsKSw9Q2.jpg) 

 

```c++
 1 /*

  2 // Definition for a Node.

  3 class Node {

  4 public:

  5   int val;

  6   Node* left;

  7   Node* right;

  8   Node* next;

  9 

  10   Node() : val(0), left(NULL), right(NULL), next(NULL) {}

  11 

  12   Node(int _val) : val(_val), left(NULL), right(NULL), next(NULL) {}

  13 

  14   Node(int _val, Node* _left, Node* _right, Node* _next)

  15     : val(_val), left(_left), right(_right), next(_next) {}

  16 };

  17 */

  18 

  19 class Solution {

  20 public:

  21     Node* connect(Node* root) {

  22         int levelNum=0;

  23         Node* temp;

  24         int right;

  25         queue<Node*> q;

  26         if(root!=NULL){

  27             q.push(root);

  28         }

  29         while(!q.empty()){

  30             levelNum=q.size();

  31             for(int i=0;i<levelNum;++i){

  32                 if(q.front()->left!=NULL){

  33                     q.push(q.front()->left);

  34                 }

  35                 if(q.front()->right!=NULL){

  36                     q.push(q.front()->right);

  37                 }

  38                 if(q.front()->right!=NULL&&q.front()->left!=NULL)

  39                 q.front()->left->next=q.front()->right;

  40                 if(q.front()->right) 

  41                     if(q.front()->next)

  42                         q.front()->right->next=q.front()->next->left;

  43                 q.pop();

  44             }

  45 

  46         }

  47         return root;

  48     }

  49 };


```

 

 

 

# 941. 有效的山脉数组

Thursday, October 15, 2020

7:53 PM

给定一个整数数组 A，如果它是有效的山脉数组就返回 true，否则返回 false。

 

让我们回顾一下，如果 A 满足下述条件，那么它是一个山脉数组：

 

A.length >= 3

在 0 < i < A.length - 1 条件下，存在 i 使得：

A[0] < A[1] < ... A[i-1] < A[i]

A[i] > A[i+1] > ... > A[A.length - 1]

 

示例 1：

 

输入：[2,1]

输出：false

示例 2：

 

输入：[3,5,5]

输出：false

示例 3：

 

输入：[0,3,2,1]

输出：true

 

 

提示：

 

0 <= A.length <= 10000

0 <= A[i] <= 10000 

 

 

```c++
  1 class Solution {

  2 public:

  3     bool validMountainArray(vector<int>& A) {

  4         int i,j;

  5         if(A.size()<=2) return false;

  6         for(i=0;i<A.size()-2;i++){

  7             if(A[i+1]<=A[i]) break;

  8         }

  9         for(j=A.size()-1;j>0;j--){

  10             if(A[j-1]<=A[j]) break;

  11         }

  12     if(i==0||j==A.size()-1) return false;

  13     if(i==j) return true;  

  14     else return false;

  15     }

  16 };


```

 

 

 

# 977. 有序数组的平方

Friday, October 16, 2020

8:33 PM

给定一个按非递减顺序排序的整数数组 A，返回每个数字的平方组成的新数组，要求也按非递减顺序排序。

 

 

 

示例 1：

 

输入：[-4,-1,0,3,10]

输出：[0,1,9,16,100]

示例 2：

 

输入：[-7,-3,2,3,11]

输出：[4,9,9,49,121]

 

 

提示：

 

1 <= A.length <= 10000

-10000 <= A[i] <= 10000

A 已按非递减顺序排序。

 

解法一：将数组中的数字依次平方，然后使用sort进行排序

  

```c++
1 

  2 class Solution {

  3 public:

  4     vector<int> sortedSquares(vector<int>& A) {

  5         for(auto &a:A){

  6             a*=a;

  7         }

  8         sort(A.begin(),A.end());

  9         return A;

  10 

  11     }

  12 };
```

解法二：使用双指针i，j，分别从头尾进行遍历，并且使用标志变量pos进行逆序遍历数组，如果下标为i的元素平方大于下标为j的元素平方，则ans[pos]=A[i]*A[i]，反之亦然。

```c++
  1 class Solution {

  2 public:

  3     vector<int> sortedSquares(vector<int>& A) {

  4         vector<int> ans(A.size());

  5         for(int i=0,j=A.size()-1,pos=A.size()-1;i<=j;){

  6             if(A[i]*A[i]>A[j]*A[j]){

  7                 ans[pos]=A[i]*A[i];

  8                 i++;

  9             }

  10             else {

  11                 ans[pos]=A[j]*A[j];

  12                 j--;

  13             }

  14             pos--;

  15         }

  16         return ans;

  17     }

  18 };
```

 

 

# 434.字符串中的单词数

Friday, October 16, 2020

9:13 PM

 

统计字符串中的单词个数，这里的单词指的是连续的不是空格的字符。

 

请注意，你可以假定字符串里不包括任何不可打印的字符。

 

示例:

 

输入: "Hello, my name is John"

输出: 5

解释: 这里的单词是指连续的不是空格的字符，所以 "Hello," 算作 1 个单词。

 

解法一：使用stringstream

 

```c++
 1 class Solution {

  2 public:

  3     int countSegments(string s) {

  4         int count=0;

  5         stringstream ss;

  6         ss<<s;

  7         while(ss>>s) count++;

  8         return count;

  9     }

  10 };
```

 

解法二：遇到空格时，如果前一个字符不为空格 则加一，如果最后一个字符不为空格，则加一。

 

```c++
 1 class Solution {

  2 public:

  3     int countSegments(string s) {

  4         int count=0;

  5         if(s.size()==0) return 0;

  6         for(int i=1;i<s.size();++i){

  7             if(s[i]==' '&&s[i-1]!=' '){

  8                 count++;

  9             }

  10         }

  11         if(s[s.size()-1]!=' ')

  12             count++;

  13         return count;

  14     }

  15 };


```

 
