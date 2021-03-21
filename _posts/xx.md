[toc]

# (复习)[无重复字符的最长子串](https://leetcode-cn.com/problems/longest-substring-without-repeating-characters/)

> 给定一个字符串，请你找出其中不含有重复字符的 **最长子串** 的长度

输入串，输出的是长度；

**思路:unordered_set记录,**

**return：left-i+1**

```c++
class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        unordered_set<char> look;
        int left = 0;
        int res = 0;
        for(int i = 0;i < s.size();i++){
            if(look.find(s[i]) != look.end()){
                look.erase(left);
                left++;
            }
            look.insert(s[i]);
            res = max(res,i - left +1);
        }
        return res;
    }
};
```

> 给定一个数组arr，返回arr的最长无的重复子串的长度(无重复指的是所有数字都不相同)

**思路：unordered_map,pos,res**

```c++
#include <unordered_map>
class Solution {
public:
    /**
     * 
     * @param arr int整型vector the array
     * @return int整型
     */
    int maxLength(vector<int>& arr) {
        // write code here
        unordered_map<int, int> map;
        int pos = 0;
        int res = 0;
        for(int a : arr)
            map[a] = -1;
        for(int i = 0;i < arr.size();i++){
            if(map[arr[i]] != -1)
                pos = max(pos,map[arr[i]] +1);
            res = max(res,i-pos+1);
            map[arr[i]] = i;
        }
        return res;
    }
};
```

# (复习)[字符串解码](https://leetcode-cn.com/problems/decode-string/)

> 给定一个经过编码的字符串，返回它解码后的字符串。
>
> 输入：s = "3[a]2[bc]"
> 输出："aaabcbc"

**双栈，nums记录数据，strs记录符号**

**模拟：每次获取数字，加入num：num*10 +s[i]-'0'**

**获取字母，存入res，暂时保存**

**获取[，说明数字，字母获取完整，将num，res，入栈**

**获取]，数据准备完成，规划res次数**

```c++
class Solution {
public:
    string decodeString(string s) {
        string res = "";
        stack<int> nums;
        stack<string> strs;
        int num = 0;
        int len = s.size();
        for(int i = 0;i < len;i++)
        {
            if(s[i] >= '0' && s[i] <= '9')
                num = num*10 + s[i] - '0';
            else if((s[i] >= 'a' && s[i] <= 'z') || (s[i] >= 'A' && s[i] <= 'Z'))
                res += s[i];
            else if(s[i] == '[')
            {
                nums.push(num);
                num = 0;
                strs.push(res);
                res = "";
            }
            else
            {
                int times = nums.top();
                nums.pop();
                for(int i = 0;i < times;i++)
                    strs.top() += res;
                res = strs.top();
                strs.pop();
            }
        }
        return res;
    }
};
```

# (常考)[数组中的第K个最大元素](https://leetcode-cn.com/problems/kth-largest-element-in-an-array/)

> 在未排序的数组中找到第 **k** 个最大的元素。请注意，你需要找的是数组排序后的第 k 个最大的元素，而不是第 k 个不同的元素。

**堆排序**

```c++
class Solution {
public:
    void buildmaxheap(vector<int> &nums,int i,int heapsize){
        int l = 2*i+1;
        int r = 2*i+2;
        int largest = i;
        if(l < heapsize && nums[l] > nums[largest])
            largest = l;
        if(r < heapsize && nums[r] > nums[largest])
            largest = r;
        if(largest != i){
            swap(nums[i],nums[largest]);
            buildmaxheap(nums,largest,heapsize);
        }
    }
    void buildheap(vector<int> &nums,int heapsize){
        for(int i = heapsize/2;i >= 0;i--)
            buildmaxheap(nums,i,heapsize);
    }
    int findKthLargest(vector<int>& nums, int k) {
        int heapsize = nums.size();
        buildheap(nums,heapsize);
        for(int i = nums.size()-1;i >= nums.size()-k+1;i--){
            swap(nums[0],nums[i]);
            heapsize--;
            buildmaxheap(nums,0,heapsize);
        }
        return nums[0];
    }
};
```

# 最小的K个数(牛客)

> 输入n个整数，找出其中最小的K个数。例如输入4,5,1,6,2,7,3,8这8个数字，则最小的4个数字是1,2,3,4

**选择排序**

```c++
class Solution {
public:
    vector<int> GetLeastNumbers_Solution(vector<int> input, int k) {
        vector<int> ret;
        if(input.empty()||k==0||k>input.size())
            return ret;
        int temp,num;
        for(int i=0;i<k;i++)
        {
            temp = INT_MAX;
            num=0;
            for(int j=0;j<input.size();j++)
            {
                if(temp>input[j])
                {
                    temp=input[j];
                    num=j;
                }
            }
            ret.push_back(temp);
            input.erase(input.begin()+num);
        }
        return ret;
    }
};
```

# [三数之和](https://leetcode-cn.com/problems/3sum/)

>给你一个包含 n 个整数的数组 nums，判断 nums 中是否存在三个元素 a，b，c ，使得 a + b + c = 0 ？请你找出所有和为 0 且不重复的三元组

**排序+三指针,一定要先排序**

```c++
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        vector<vector<int>> res;
        sort(nums.begin(),nums.end());
        for(int k = 0;k < nums.size();k++){
            if(nums[k] > 0)
                break;
            if(k > 0 && nums[k] == nums[k-1])
                continue;
            int i = k+1;
            int j = nums.size()-1;
            while(i < j){
                int sum = nums[k] + nums[i] + nums[j];
                if(sum > 0)
                    while(i < j && nums[j] == nums[--j]);
                else if(sum < 0)
                    while(i < j && nums[i] == nums[++i]);
                else{
                    res.push_back({nums[k],nums[i],nums[j]});
                    while(i < j && nums[j] == nums[--j]);
                    while(i < j && nums[i] == nums[++i]);
                }
            }
        }
        return res;
    }
};
```

# (复习，全文默写)[LRU 缓存机制](https://leetcode-cn.com/problems/lru-cache/)

> 运用你所掌握的数据结构，设计和实现一个 [LRU (最近最少使用) 缓存机制](https://baike.baidu.com/item/LRU) 。

**双向链表+unordered_map**

```c++
#include <unordered_map>
#include <list>
struct Node{
    int key;
    int value;
    Node(int _key,int _value):key(_key),value(_value){}
};
class Solution {
private:
    list<Node> li;
    unordered_map<int, list<Node>::iterator> map;
    int cap;
public:
    /**
     * lru design
     * @param operators int整型vector<vector<>> the ops
     * @param k int整型 the k
     * @return int整型vector
     */
    int remove(list<Node>::iterator &ite){
        int key = ite->key;
        int value = ite->value;
        li.erase(ite);
        map.erase(key);
        return value;
    }
    void add(int key,int value){
        li.push_front(Node(key,value));
        map[key] = li.begin();
        if(li.size() > cap){
            auto last = li.end();
            last--;
            remove(last);
        }
    }
    void set(int x,int y){
        auto ite = map.find(x);
        if(ite != map.end())
            remove(ite->second);
        add(x, y);
    }
    int get(int x){
        int val = 0;
        auto ite = map.find(x);
        if(ite != map.end()){
            val = remove(ite->second);
            add(x, val);
            return val;
        }
        return -1;
    }
    vector<int> LRU(vector<vector<int> >& operators, int k) {
        // write code here
        cap = k;
        vector<int> res;
        for(auto &input : operators){
            if(input[0] == 1)
                set(input[1], input[2]);
            else
                res.push_back(get(input[1]));
        }
        return res;
    }
};
```

# [LFU 缓存](https://leetcode-cn.com/problems/lfu-cache/)

> 请你为 [最不经常使用（LFU）](https://baike.baidu.com/item/缓存算法)缓存算法设计并实现数据结构



# [买卖股票的最佳时机](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock/)

>给定一个数组 prices ，它的第 i 个元素 prices[i] 表示一支给定股票第 i 天的价格。你只能选择 某一天 买入这只股票，并选择在 未来的某一个不同的日子 卖出该股票。设计一个算法来计算你所能获取的最大利润。返回你可以从这笔交易中获取的最大利润。如果你不能获取任何利润，返回 0 。

**一支股票问题：记录maxpro和minprice，遍历prices更新**

```c++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
      int maxpro = INT_MIN;
      int minprice = INT_MAX;
      for(int price:prices){
          maxpro = max(maxpro,price-minprice);
          minprice = min(minprice,price);
      }  
      return maxpro;
    }
};
```

# [买卖股票的最佳时机 II](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-ii/)

>给定一个数组，它的第 *i* 个元素是一支给定股票第 *i* 天的价格。设计一个算法来计算你所能获取的最大利润。你可以尽可能地完成更多的交易（多次买卖一支股票）

**多支股票问题：**

**思路：上涨就卖，直接定义temp = prices[i] - prices[i-1],大于0直接保存**

```c++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int res = 0;
        for(int i = 1;i < prices.size();i++){
            int temp = prices[i] - prices[i-1];
            if(temp > 0)
                res += temp;
        }
        return res;
    }
};
```

# [相交链表](https://leetcode-cn.com/problems/intersection-of-two-linked-lists/)

> 编写一个程序，找到两个单链表相交的起始节点。

**双指针，为空时转化为另外链表头**

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        ListNode *A = headA;
        ListNode *B = headB;
        while(A!=B){
            A = A == nullptr ? headB:A->next;
            B = B == nullptr ? headA:B->next;
        }
        return A;
    }
};
```

# [环形链表](https://leetcode-cn.com/problems/linked-list-cycle/)

> 给定一个链表，判断链表中是否有环。

**快慢指针**

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    bool hasCycle(ListNode *head) {
        ListNode *fast = head;
        ListNode *slow = head;
        while(fast != nullptr && fast->next != nullptr){
            fast = fast->next->next;
            slow = slow->next;
            if(slow == fast){
                fast = head;
                while(fast != slow){
                    fast = fast->next;
                    slow = slow->next;
                }
                return true;
            }
        }
        return false;
    }
};
```

# [环形链表 II](https://leetcode-cn.com/problems/linked-list-cycle-ii/)

>给定一个链表，返回链表开始入环的第一个节点。 如果链表无环，则返回 null。为了表示给定链表中的环，我们使用整数 pos 来表示链表尾连接到链表中的位置（索引从 0 开始）。 如果 pos 是 -1，则在该链表中没有环。注意，pos 仅仅是用于标识环的情况，并不会作为参数传递到函数中

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode *detectCycle(ListNode *head) {
        ListNode *fast = head;
        ListNode *slow = head;
        while(fast != nullptr && fast->next != nullptr){
            fast = fast->next->next;
            slow = slow->next;
            if(fast == slow){
                fast = head;
                while(fast != slow){
                    fast = fast->next;
                    slow = slow->next;
                }
                return fast;
            }
        }
        return nullptr;
    }
};
```

# (复习)[删除链表的倒数第 N 个结点](https://leetcode-cn.com/problems/remove-nth-node-from-end-of-list/)

> 给你一个链表，删除链表的倒数第 `n` 个结点，并且返回链表的头结点。

**快慢指针**

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        if(head->next == nullptr)
            return nullptr;
        ListNode *fast = head;
        ListNode *slow = head;
        for(int i =0 ;i < n;i++)
            fast = fast->next;
        if(fast == nullptr)
            head = slow->next;
        else
        {
            while(fast->next != nullptr)
            {
                fast = fast->next;
                slow = slow->next;
            }
            slow->next = slow->next->next;
        }
        return head;
    }
};
```

# [删除排序链表中的重复元素](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list/)

> 给定一个排序链表，删除所有重复的元素，使得每个`元素只出现一次`。

**只出现一次：直接遍历**

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* deleteDuplicates(ListNode* head) {
        ListNode *temp = head;
        while(temp != nullptr && temp->next != nullptr)
        {
            if(temp->val == temp->next->val)
            {
                temp->next = temp->next->next;
            }
            temp = temp->next;
        }
        return  head;
    }
};
```

# 删除链表重复出现的节点

> 给出一个升序排序的链表，删除链表中的所有重复出现的元素，只保留原链表中只出现一次的元素。

> 给出的链表为1 \to 2\to 3\to 3\to 4\to 4\to51→2→3→3→4→4→5, 返回1\to 2\to51→2→5

**有重复出现的，直接全去除：递归**

```c++
class Solution {
public:
    /**
     * 
     * @param head ListNode类 
     * @return ListNode类
     */
    ListNode* deleteDuplicates(ListNode* head) {
        // write code here
        if(head == nullptr || head->next == nullptr)
            return head;
        if(head->val != head->next->val){
            head->next = deleteDuplicates(head->next);
            return head;
        }
        else{
            int temp = head->val;
            while(head->val == temp){
                head = head->next;
                if(head == nullptr)
                    return nullptr;
            }
            return deleteDuplicates(head);
        }
    }
};
```

# (复习)[排序链表](https://leetcode-cn.com/problems/sort-list/)

> 给你链表的头结点 `head` ，请将其按 **升序** 排列并返回 **排序后的链表** 。

**快慢指针找mid，mergesort调用merge**

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode *findmid(ListNode *head)
    {
        ListNode *slow = head;
        ListNode *fast = head;
        ListNode *prev;
        while(fast != nullptr && fast->next != nullptr)
        {
            prev = slow;
            slow = slow->next;
            fast = fast->next->next;
        }
        prev->next = nullptr;
        return slow;
    }
    ListNode *mergetowlist(ListNode *l1,ListNode *l2)
    {
        if(l1 == nullptr)
            return l2;
        else if(l2 == nullptr)
            return l1;
        if(l1->val < l2->val)
        {
            l1->next = mergetowlist(l1->next,l2);
            return l1;
        }
        else
        {
            l2->next = mergetowlist(l1,l2->next);
            return l2;
        }
    }
    ListNode *mergesort(ListNode *head)
    {
        if(head->next  == nullptr)
            return head;
        ListNode *mid = findmid(head);
        ListNode *l1 = mergesort(head);
        ListNode *l2 = mergesort(mid);
        return mergetowlist(l1,l2);
    }
    ListNode* sortList(ListNode* head) {
        return head == nullptr ? nullptr : mergesort(head);
    }
};
```

# [反转链表](https://leetcode-cn.com/problems/reverse-linked-list/)

> 反转一个单链表。

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        if(head == nullptr || head->next == nullptr)
            return head;
        ListNode *dump = reverseList(head->next);
        head->next->next = head;
        head->next =nullptr;
        return dump;
    }
};
```

# (复习)[反转链表 II](https://leetcode-cn.com/problems/reverse-linked-list-ii/)

> 反转从位置 *m* 到 *n* 的链表。请使用一趟扫描完成反转

```c++
/**
 * struct ListNode {
 *	int val;
 *	struct ListNode *next;
 * };
 */

class Solution {
public:
    /**
     * 
     * @param head ListNode类 
     * @param m int整型 
     * @param n int整型 
     * @return ListNode类
     */
    ListNode* reverseBetween(ListNode* head, int m, int n) {
        // write code here
        ListNode  *dummy = new ListNode(-1);
        dummy->next = head;
        ListNode *pre = dummy;
        for(int i = 1;i < m;i++)
            pre = pre->next;
        head = pre->next;
        for(int i = m;i < n;i++){
            ListNode *next = head->next;
            head->next = next->next;
            next->next = pre->next;
            pre->next = next;
        }
        return dummy->next;
    }
};
```

# (复习)[重排链表](https://leetcode-cn.com/problems/reorder-list/)

>给定一个单链表 *L*：*L*0→*L*1→…→*L**n*-1→*L*n ，
> 将其重新排列后变为： *L*0→*L**n*→*L*1→*L**n*-1→*L*2→*L**n*-2→…

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode *reverse(ListNode *head)
    {
        if(head == nullptr || head->next == nullptr)
            return head;
        ListNode *dump = reverse(head->next);
        head->next->next = head;
        head->next =  nullptr;
        return dump;
    }
    void reorderList(ListNode* head) {
        if(!head ||  !head->next)
            return;
        //找中点
        ListNode *slow = head;
        ListNode *fast = head->next->next;
        while(fast && fast->next)
        {
            slow = slow->next;
            fast = fast->next->next;
        }
        fast = slow->next;
        slow->next = nullptr;
        slow = head;
        //翻转右边
        fast = reverse(fast);
        //重排
        while(slow->next && fast)
        {
            ListNode *temp = slow->next;
            slow->next = fast;
            slow = slow->next;
            fast =fast->next;
            slow->next = temp;
            slow = slow->next;
        }
        if(fast)
            slow->next = fast;
    }
};
```

# [ K 个一组翻转链表](https://leetcode-cn.com/problems/reverse-nodes-in-k-group/)

>给你一个链表，每 k 个节点一组进行翻转，请你返回翻转后的链表。k 是一个正整数，它的值小于或等于链表的长度。如果节点总数不是 k 的整数倍，那么请将最后剩余的节点保持原有顺序

**思路：单链表翻转--->> k**

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode *reverse(ListNode *head,ListNode *tail){
        ListNode *prev = head;
        ListNode *cur;
        while(head != tail){
            cur = head->next;
            head->next = prev;
            prev = head;
            head = cur;
        }
        return prev;
    }
    ListNode* reverseKGroup(ListNode* head, int k) {
        ListNode *tail = head;
        ListNode *dump;
        for(int i = 0;i < k;i++){
            if(tail == nullptr)
                return head;
            tail = tail->next;
        }
        dump = reverse(head,tail);
        head->next = reverseKGroup(tail,k);
        return dump;
    }
};
```

# [两两交换链表中的节点](https://leetcode-cn.com/problems/swap-nodes-in-pairs/)

>给定一个链表，两两交换其中相邻的节点，并返回交换后的链表。
>
>**你不能只是单纯的改变节点内部的值**，而是需要实际的进行节点交换

**上题：k == 2**

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode *reverse(ListNode *head,ListNode *tail)
    {
        ListNode *prev = head;
        ListNode *curr;
        while(head != tail)
        {
            curr = head->next;
            head->next = prev;
            prev = head;
            head = curr;
        }
        return prev;
    }
    ListNode* swapPairs(ListNode* head) {
        ListNode *tail = head;
        ListNode *dump;
        for(int i = 0;i < 2;i++)
        {
            if(tail == nullptr)
                return head;
            tail = tail->next;
        }
        dump = reverse(head,tail);
        head->next = swapPairs(tail);
        return dump;
    }
};
```

# 排序奇升偶降链表                

-  按奇偶位置拆分链表，得1->3->5->7->NULL和8->6->4->2->NULL
- 反转偶链表，得1->3->5->7->NULL和2->4->6->8->NULL
- 合并两个有序链表，得1->2->3->4->5->6->7->8->NULL

# (复习)[翻转字符串里的单词](https://leetcode-cn.com/problems/reverse-words-in-a-string/)

> 给定一个字符串，逐个翻转字符串中的每个单词。

```
输入："the sky is blue"
输出："blue is sky the"
```

```c++
class Solution {
public:
    string reverseWords(string s) {
        int left = 0;
        int right = s.size()-1;
        while(left < right && s[left] == ' ') left++;
        while(left < right && s[right] == ' ') right--;
        deque<string> dq;
        string word;
        while(left <= right){
            char c = s[left];
            if(word.size() != 0 && c == ' '){
                dq.push_front(move(word));
                word = "";
            }
            else if(c != ' ')
                word += c;
            left++;
        }
        dq.push_front(move(word));
        string res;
        while(!dq.empty()){
            res += dq.front();
            dq.pop_front();
            if(!dq.empty())
                res += ' ';
        }
        return res;
    }
};
```

# (复习)[合并K个排序链表](https://leetcode-cn.com/problems/merge-k-sorted-lists)

>给你一个链表数组，每个链表都已经按升序排列。请你将所有链表合并到一个升序链表中，返回合并后的链表。

**问题分解**

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode *merge(ListNode *l1,ListNode *l2){
        if(l1 == nullptr) return l2;
        else if(l2 == nullptr) return l1;
        if(l1->val < l2->val)
        {
            l1->next = merge(l1->next,l2);
            return l1;
        }
        else
        {
            l2->next = merge(l1,l2->next);
            return l2;
        }
    }
    ListNode *merge(vector<ListNode *> &lists,int l,int r){
        if(l == r)
            return lists[l];
        int mid = (l + r)/2;
        ListNode *l1 = merge(lists,l,mid);
        ListNode *l2 = merge(lists,mid + 1,r);
        return merge(l1,l2);
    }
    ListNode* mergeKLists(vector<ListNode*>& lists) {
        if(lists.size() == 0)
            return nullptr;
        return merge(lists,0,lists.size()-1);
    }
};
```

# [链表中倒数第k个节点](https://leetcode-cn.com/problems/lian-biao-zhong-dao-shu-di-kge-jie-dian-lcof/)

> 输入一个链表，输出该链表中倒数第k个节点。为了符合大多数人的习惯，本题从1开始计数，即链表的尾节点是倒数第1个节点。

**快慢指针**

```c++
/**
 * struct ListNode {
 *	int val;
 *	struct ListNode *next;
 *	ListNode(int x) : val(x), next(nullptr) {}
 * };
 */
class Solution {
public:
    /**
     * 代码中的类名、方法名、参数名已经指定，请勿修改，直接返回方法规定的值即可
     *
     * 
     * @param pHead ListNode类 
     * @param k int整型 
     * @return ListNode类
     */
    ListNode* FindKthToTail(ListNode* pHead, int k) {
        // write code here
        ListNode *fast = pHead;
        ListNode *slow = pHead;
        for(int i = 0;i < k;i++){
            if(fast == nullptr)
                return fast;
            fast = fast->next;
        }
        while(fast != nullptr){
            fast = fast->next;
            slow = slow->next;
        }
        return slow;
    }
};
```

# [两数之和](https://leetcode-cn.com/problems/two-sum/)

> 给定一个整数数组 `nums` 和一个整数目标值 `target`，请你在该数组中找出 **和为目标值** 的那 **两个** 整数，并返回它们的数组下标。

**unordered_map**

```c++
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        unordered_map<int,int> mp;
        for(int i = 0;i < nums.size();i++){
            auto it = mp.find(target - nums[i]);
            if(it != mp.end())
                return {i,it->second};
            mp[nums[i]] = i;
        }
        return {};
    }
};
```

# (复习)[和为K的子数组](https://leetcode-cn.com/problems/subarray-sum-equals-k/)

> 给定一个整数数组和一个整数 **k，**你需要找到该数组中和为 **k** 的连续的子数组的个数。

```c++
class Solution {
public:
    int subarraySum(vector<int>& nums, int k) {
        unordered_map<int,int> mp;
        int pre = 0;
        int res = 0;
        mp[0] = 1;
        for(int num:nums){
            pre += num;
            if(mp.find(pre - k) != mp.end())
                res += mp[pre - k];
            mp[pre]++;
        }
        return res;
    }
};
```

# [字符串相加](https://leetcode-cn.com/problems/add-strings/)

> 给定两个字符串形式的非负整数 `num1` 和`num2` ，计算它们的和。

**模拟**

```c++
class Solution {
public:
    string addStrings(string num1, string num2) {
        int m = num1.size()-1;
        int n = num2.size()-1;
        string res;
        int carry = 0;
        while(m >= 0 || n >= 0 || carry != 0){
            int x = m >= 0 ? num1[m--]-'0' : 0;
            int y = n >= 0 ? num2[n--]-'0' : 0;
            int sum = x + y + carry;
            carry  = sum / 10;
            sum %= 10;
            res += (sum + '0');
        }
        reverse(res.begin(),res.end());
        return res;
    }
};
```

# [大数加法](https://www.nowcoder.com/practice/11ae12e8c6fe48f883cad618c2e81475?tpId=196&tqId=37176&rp=1&ru=%2Factivity%2Foj&qru=%2Fta%2Fjob-code-total%2Fquestion-ranking&tab=answerKey)

> 以字符串的形式读入两个数字，编写一个函数计算它们的和，以字符串形式返回。

```c++
class Solution {
public:
    /**
     * 代码中的类名、方法名、参数名已经指定，请勿修改，直接返回方法规定的值即可
     * 计算两个数之和
     * @param s string字符串 表示第一个整数
     * @param t string字符串 表示第二个整数
     * @return string字符串
     */
    string solve(string s, string t) {
        // write code here
        int m = s.size()-1;
        int n = t.size()-1;
        string res;
        int carry = 0;
        while(m >=0 || n >= 0 ||carry != 0){
            int x = m >= 0 ? s[m--] - '0' : 0;
            int y = n >= 0 ? t[n--] - '0' : 0;
            int sum = x + y + carry;
            carry = sum / 10;
            sum %= 10;
            res += sum + '0';
        }
        reverse(res.begin(), res.end());
        return res;
    }
};
```

# [两数相加](https://leetcode-cn.com/problems/add-two-numbers/)

>给你两个 非空 的链表，表示两个非负的整数。它们每位数字都是按照 逆序 的方式存储的，并且每个节点只能存储 一位 数字。请你将两个数相加，并以相同形式返回一个表示和的链表。你可以假设除了数字 0 之外，这两个数都不会以 0 开头。

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        ListNode *head = nullptr;
        ListNode *tail = nullptr;
        int carry = 0;
        while(l1 != nullptr || l2 != nullptr){
            int x = l1 == nullptr ? 0 : l1->val;
            int y = l2 == nullptr ? 0 : l2->val;
            int sum = x + y + carry;
            if(head == nullptr)
                head = tail = new ListNode(sum % 10);
            else{
                tail->next = new ListNode(sum % 10);
                tail = tail->next;
            }
            carry =sum/10;
            if(l1)
                l1 = l1->next;
            if(l2)
                l2 = l2->next;
        }
        if(carry > 0)
            tail->next = new ListNode(carry);
        return head;
    }
};
```

# (复习)[36进制加法](https://mp.weixin.qq.com/s/bgD1Q5lc92mX7RNS1L65qA)

```c++
#include <iostream>
#include <algorithm>
using namespace std;
char getChar(int n)
{
    if (n <= 9)
        return n + '0';
    else
        return n - 10 + 'a';
}
int getInt(char ch)
{
    if ('0' <= ch && ch <= '9')
        return ch - '0';
    else
        return ch - 'a' + 10;
}
string add36Strings(string num1, string num2)
{
    int carry = 0;
    int i = num1.size() - 1, j = num2.size() - 1;
    int x, y;
    string res;
    while (i >= 0 || j >= 0 || carry)
    {
        x = i >= 0 ? getInt(num1[i]) : 0;
        y = j >= 0 ? getInt(num2[j]) : 0;
        int temp = x + y + carry;
        res += getChar(temp % 36);
        carry = temp / 36;
        i--, j--;
    }
    reverse(res.begin(), res.end());
    return res;
}
int main()
{
    string a = "1b", b = "2x", c;
    c = add36Strings(a, b);
    cout << c << endl;
}
```

# (复习)[基本计算器](https://leetcode-cn.com/problems/basic-calculator/)

> 给你一个字符串表达式 `s` ，请你实现一个基本计算器来计算并返回它的值。

```c++
class Solution {
public:
    int calculate(string s) {
        stack<int>st;
        int temp=0;//记录多位数
        int result=0;//记录压入栈的数字
        int symbol = 1;//标志正负数
        for (int i = 0; i < s.size(); i++)
        {
            if (s[i] >= '0'&&s[i] <= '9')
                temp = 10 * temp + (s[i] - '0');
            else if (s[i] == '+')
            {
                result += symbol*temp;
                symbol = 1;//重置
                temp = 0;//重置
            }
            else if (s[i] == '-')
            {
                result += symbol*temp;
                symbol = -1;//负数
                temp = 0;
            }
            else if (s[i] == '(')//将迄今为止计算的结果和符号添加到栈上
            {
                st.push(result);
                st.push(symbol);
                symbol = 1;
                result = 0;
            }
            else if (s[i] == ')')
            {
                result += symbol*temp;//计算括号内的结果
                result *= st.top(); st.pop();//获取入栈前的正负号
                result += st.top(); st.pop();//加上入栈前的结果
                temp = 0;//重置，因为)之前是数字，temp还是不变
            }
        }
        //循环退出的时候，肯定最后一个是数字（合法表达式），要把result加上该数字，而且别忘了倒数第二位的正负号
        return result + (symbol*temp);
    }
};
```

# [ 最大子序和](https://leetcode-cn.com/problems/maximum-subarray/)

> 给定一个整数数组 `nums` ，找到一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。

**res = nums[0],pre = 0 ...**

```c++
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        int res = nums[0];
        int pre = 0;
        for(int num:nums){
            pre = max(num,pre + num);
            res = max(pre,res);
        }
        return res;
    }
};
```

# [最大累加和](https://www.nowcoder.com/practice/554aa508dd5d4fefbf0f86e5fe953abd?tpId=117&tqId=37797&companyId=665&rp=1&ru=%2Fcompany%2Fhome%2Fcode%2F665&qru=%2Fta%2Fjob-code-high%2Fquestion-ranking&tab=answerKey)

```c++
class Solution {
public:
    /**
     * max sum of the subarray
     * @param arr int整型vector the array
     * @return int整型
     */
    int maxsumofSubarray(vector<int>& arr) {
        // write code here
        int pre = 0;
        int res = arr[0];
        for(int a:arr){
            pre = max(a,a+pre);
            res = max(res,pre);
        }
        return res;
    }
};
```

# [长度最小的子数组](https://leetcode-cn.com/problems/minimum-size-subarray-sum/)

>给定一个含有 n 个正整数的数组和一个正整数 target 。找出该数组中满足其和 ≥ target 的长度最小的 连续子数组 [numsl, numsl+1, ..., numsr-1, numsr] ，并返回其长度。如果不存在符合条件的子数组，返回 0 。

**双指针**

```c++
class Solution {
public:
    int minSubArrayLen(int target, vector<int>& nums) {
        int n = nums.size();
        if(n == 0)
            return 0;
        int res = INT_MAX;
        int start = 0;
        int end = 0;
        int sum = 0;
        while(end < n){
            sum += nums[end];
            while(sum >= target){
                res = min(res,end - start + 1);
                sum -= nums[start];
                start++;
            }
            end++;
        }
        return res == INT_MAX ? 0 : res;
    }
};
```

# [合并两个有序链表](https://leetcode-cn.com/problems/merge-two-sorted-lists/)

> 将两个升序链表合并为一个新的 **升序** 链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。 

**递归**

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
        if(l1 == nullptr)
            return l2;
        else if(l2 == nullptr)
            return l1;
        if(l1->val < l2->val){
            l1->next = mergeTwoLists(l1->next,l2);
            return l1;
        } 
        else{
            l2->next = mergeTwoLists(l1,l2->next);
            return l2;
        }
    }
};
```

**迭代**

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
        ListNode *prehead = new ListNode(-1);
        ListNode *pre = prehead;
        while(l1 != nullptr && l2 != nullptr)
        {
            if(l1->val < l2->val)
            {
                pre->next = l1;
                l1 = l1->next;
            }
            else
            {
                pre->next = l2;
                l2 = l2->next;
            }
            pre = pre->next;
        }
        pre->next = l1 == nullptr ? l2:l1;
        return prehead->next;
    }
};
```

# [接雨水](https://leetcode-cn.com/problems/trapping-rain-water/)

> 给定 *n* 个非负整数表示每个宽度为 1 的柱子的高度图，计算按此排列的柱子，下雨之后能接多少雨水。

```c++
class Solution {
public:
    int trap(vector<int>& height) {
        int res = 0;
        int left_max = 0;
        int right_max = 0;
        int left = 0;
        int right = height.size()-1;
        while(left < right){
            if(height[left] < height[right]){
                height[left] >= left_max ? left_max = height[left] : res += (left_max - height[left]);
                left++;
            }
            else{
                height[right] >= right_max ? right_max = height[right] : res += (right_max - height[right]);
                right--;
            }
        }
        return res;
    }
};
```

# [二叉树的最近公共祖先](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-tree/)

> 给定一个二叉树, 找到该树中两个指定节点的最近公共祖先。

**递归**

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        if(root == nullptr || p == root || q == root)
            return root;
        TreeNode *left = lowestCommonAncestor(root->left,p,q);
        TreeNode *right = lowestCommonAncestor(root->right,p,q);
        if(left == nullptr && right == nullptr)
            return nullptr;
        if(left == nullptr)
            return right;
        if(right == nullptr)
            return left;
        return root;
    }
};
```

# [有效的括号](https://leetcode-cn.com/problems/valid-parentheses/)

> 给定一个只包括 `'('`，`')'`，`'{'`，`'}'`，`'['`，`']'` 的字符串 `s` ，判断字符串是否有效。

**栈辅助**

```c++
//牛客网可过
class Solution {
public:
    /**
     * 
     * @param s string字符串 
     * @return bool布尔型
     */
    bool isValid(string s) {
        // write code here
        stack<char> c;
        for(int i = 0 ; i < s.length(); i++)
        {
            if(c.empty())
            {
                c.push(s[i]);
                continue;
            }
            if(s[i]==')'&&c.top()=='(')
                c.pop();
            else if(s[i]=='}'&&c.top()=='{')
                c.pop();
            else if(s[i]==']'&&c.top()=='[')
                c.pop();
            else
                c.push(s[i]);
        }
        return c.empty();
    }
};
```

# [最长有效括号](https://leetcode-cn.com/problems/longest-valid-parentheses/)

> 给你一个只包含 `'('` 和 `')'` 的字符串，找出最长有效（格式正确且连续）括号子串的长度。

```c++
class Solution {
public:
    int longestValidParentheses(string s) {
        int maxans = 0;
        stack<int> stk;
        stk.push(-1);
        for (int i = 0; i < s.length(); i++) {
            if (s[i] == '(') {
                stk.push(i);
            } else {
                stk.pop();
                if (stk.empty()) {
                    stk.push(i);
                } else {
                    maxans = max(maxans, i - stk.top());
                }
            }
        }
        return maxans;
    }
};
```

# [括号生成](https://leetcode-cn.com/problems/generate-parentheses)

```c++
class Solution {
public:
void backtrace(vector<string> &res,string &cur,int open,int close,int n){
        if(cur.size() == n * 2){
            res.push_back(cur);
            return;
        }
        if(open < n){
            cur.push_back('(');
            backtrace(res,cur,open+1,close,n);
            cur.pop_back();
        }
        if(close < open){
            cur.push_back(')');
            backtrace(res,cur,open,close+1,n);
            cur.pop_back();
        }
    }
    vector<string> generateParenthesis(int n) {
        vector<string> res;
        string cur;
        backtrace(res,cur,0,0,n);
        return res;
    }
};
```

# [螺旋矩阵](https://leetcode-cn.com/problems/spiral-matrix/)

> 给你一个 `m` 行 `n` 列的矩阵 `matrix` ，请按照 **顺时针螺旋顺序** ，返回矩阵中的所有元素。

```c++
class Solution {
public:
    vector<int> spiralOrder(vector<vector<int>>& matrix) {
        vector <int> ans;
        if(matrix.empty()) return ans; //若数组为空，直接返回答案
        int u = 0; //赋值上下左右边界
        int d = matrix.size() - 1;
        int l = 0;
        int r = matrix[0].size() - 1;
        while(true)
        {
            for(int i = l; i <= r; ++i) ans.push_back(matrix[u][i]); //向右移动直到最右
            if(++ u > d) break; //重新设定上边界，若上边界大于下边界，则遍历遍历完成，下同
            for(int i = u; i <= d; ++i) ans.push_back(matrix[i][r]); //向下
            if(-- r < l) break; //重新设定有边界
            for(int i = r; i >= l; --i) ans.push_back(matrix[d][i]); //向左
            if(-- d < u) break; //重新设定下边界
            for(int i = d; i >= u; --i) ans.push_back(matrix[i][l]); //向上
            if(++ l > r) break; //重新设定左边界
        }
        return ans;
    }
};
```

# [螺旋矩阵 II](https://leetcode-cn.com/problems/spiral-matrix-ii/)

> 给你一个正整数 `n` ，生成一个包含 `1` 到 `n2` 所有元素，且元素按顺时针顺序螺旋排列的 `n x n` 正方形矩阵 `matrix` 。

```c++
class Solution {
public:
    vector<vector<int>> generateMatrix(int n) {
        vector<vector<int>> matrix(n,vector<int>(n));
        int u = 0;
        int d = n-1;
        int l = 0;
        int r = n-1;
        int num = 1;

        while(true){
            for(int i=l; i <= r; i++) matrix[u][i] = num++;
            if (u++ >= d) break;
            for(int i=u; i <= d; i++) matrix[i][r] = num++;
            if (r-- <= l) break;
            for(int i=r; i >= l; i--) matrix[d][i] = num++;
            if (d-- <= u) break;
            for(int i=d; i >= u; i--) matrix[i][l] = num++;
            if (l++ >= r) break;
        }
        return matrix;
    }
};
```



# [路径总和 II](https://leetcode-cn.com/problems/path-sum-ii/)

> 给定一个二叉树和一个目标和，找到所有从根节点到叶子节点路径总和等于给定目标和的路径。

****

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
private:
    vector<vector<int>> res;
    vector<int> path;
public:
    void find(TreeNode *root,int targetSum){
        if(root == nullptr)
            return;
        path.push_back(root->val);
        targetSum -= root->val;
        if(root->left == nullptr && root->right == nullptr && targetSum == 0)
            res.push_back(path);
        find(root->left,targetSum);
        find(root->right,targetSum);
        path.pop_back();
    }
    vector<vector<int>> pathSum(TreeNode* root, int targetSum) {
        find(root,targetSum);
        return res;
    }
};
```

# [二叉树的层序遍历](https://leetcode-cn.com/problems/binary-tree-level-order-traversal/)

> 给你一个二叉树，请你返回其按 **层序遍历** 得到的节点值。 （即逐层地，从左到右访问所有节点）

**队列辅助**

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    vector<vector<int>> levelOrder(TreeNode* root) {
        vector<vector<int>> res;
        queue<TreeNode *> q;
        q.push(root);
        while(!q.empty()){
            int size = q.size();
            vector<int> level;
            for(int i =0;i < size;i++){
                TreeNode *temp = q.front();
                q.pop();
                if(temp == nullptr)
                    continue;
                level.push_back(temp->val);
                q.push(temp->left);
                q.push(temp->right);
            }
            if(!level.empty())
                res.push_back(level);
        }
        return res;
    }
};
```

# [二叉树的右视图](https://leetcode-cn.com/problems/binary-tree-right-side-view/)

> 给定一棵二叉树，想象自己站在它的右侧，按照从顶部到底部的顺序，返回从右侧所能看到的节点值。

**层次遍历 末位加入res**

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    vector<int> rightSideView(TreeNode* root) {
        queue<TreeNode*> que;
        if (root != NULL) que.push(root);
        vector<int> result;
        while (!que.empty()) {
            int size = que.size();
            for (int i = 0; i < size; i++) {
                TreeNode* node = que.front();
                que.pop();
                if (i == (size - 1)) result.push_back(node->val); 
                if (node->left) que.push(node->left);
                if (node->right) que.push(node->right);
            }
        }
        return result;
    }
};
```

# [二叉树的锯齿形层序遍历](https://leetcode-cn.com/problems/binary-tree-zigzag-level-order-traversal/)

> 给定一个二叉树，返回其节点值的锯齿形层序遍历。（即先从左往右，再从右往左进行下一层遍历，以此类推，层与层之间交替进行）

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    vector<vector<int>> zigzagLevelOrder(TreeNode* root) {
        queue<TreeNode *> q;
        q.push(root);
        vector<vector<int>> res;
        bool flag = true;
        while(!q.empty()){
            int size = q.size();
            vector<int> level;
            for(int i = 0;i < size;i++){
                if(flag){
                    TreeNode *temp = q.front();
                    q.pop();
                    if(temp == nullptr)
                        continue;
                    level.push_back(temp->val);
                    q.push(temp->left);
                    q.push(temp->right);
                }
                else{
                    TreeNode *temp = q.front();
                    q.pop();
                    if(temp == nullptr)
                        continue;
                    level.insert(level.begin(),temp->val);
                    q.push(temp->left);
                    q.push(temp->right);
                }
            }
            if(!level.empty())
                res.push_back(level);
            flag = !flag;
        }
        return res;
    }
};
```

# [二叉树的前序遍历](https://leetcode-cn.com/problems/binary-tree-preorder-traversal/)

> 给你二叉树的根节点 `root` ，返回它节点值的 **前序** 遍历

**递归**

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    void preorder(TreeNode *root,vector<int> &res){
        if(root == nullptr)
            return;
        res.push_back(root->val);
        preorder(root->left,res);
        preorder(root->right,res);
    }
    vector<int> preorderTraversal(TreeNode* root) {
        vector<int> res;
        preorder(root,res);
        return res;
    }
};
```

**迭代**

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    vector<int> preorderTraversal(TreeNode* root) {
        vector<int> res;
        if(root == nullptr)
            return res;
        stack<TreeNode *> sk;
        while(root != nullptr || !sk.empty()){
            while(root != nullptr){
                res.push_back(root->val);
                sk.push(root);
                root = root->left;
            }
            root = sk.top();
            sk.pop();
            root = root->right;
        }
        return res;
    }
};
```



# [二叉树的中序遍历](https://leetcode-cn.com/problems/binary-tree-inorder-traversal/)

> 给定一个二叉树的根节点 `root` ，返回它的 **中序** 遍历。

**递归**

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    void inorder(TreeNode *root,vector<int> &res){
        if(root == nullptr)
            return;
        inorder(root->left,res);
        res.push_back(root->val);
        inorder(root->right,res);
    }
    vector<int> inorderTraversal(TreeNode* root) {
        vector<int> res;
        inorder(root,res);
        return res;
    }
};
```

**迭代**

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    vector<int> inorderTraversal(TreeNode* root) {
        vector<int> res;
        stack<TreeNode *> sk;
        while(root != nullptr || !sk.empty())
        {
            while(root != nullptr)
            {
                sk.push(root);
                root = root->left;
            }
            root = sk.top();
            sk.pop();
            res.push_back(root->val);
            root = root->right;
        }
        return res;
    }
};
```

# [二叉树中的最大路径和](https://leetcode-cn.com/problems/binary-tree-maximum-path-sum/)

>路径 被定义为一条从树中任意节点出发，沿父节点-子节点连接，达到任意节点的序列。同一个节点在一条路径序列中 至多出现一次 。该路径 至少包含一个 节点，且不一定经过根节点。

**maxgain辅助函数**

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
private:
    int res = INT_MIN;
public:
    int maxgain(TreeNode *root){
        if(root == nullptr)
            return 0;
        int left = max(maxgain(root->left),0);
        int right = max(maxgain(root->right),0);
        int price = root->val + left + right;
        res = max(res,price);
        return root->val + max(left,right);
    }
    int maxPathSum(TreeNode* root) {
        maxgain(root);
        return res;
    }
};
```

# [翻转二叉树](https://leetcode-cn.com/problems/invert-binary-tree/)

> 翻转一棵二叉树。

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    TreeNode* invertTree(TreeNode* root) {
        if(root == nullptr)
            return nullptr;
        TreeNode *l = invertTree(root->left);
        TreeNode *r = invertTree(root->right);
        root->left = r;
        root->right = l;
        return root;
    }
};
```



# [二叉树的最大深度](https://leetcode-cn.com/problems/maximum-depth-of-binary-tree/)

>给定一个二叉树，找出其最大深度。二叉树的深度为根节点到最远叶子节点的最长路径上的节点数

**递归**

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    int maxDepth(TreeNode* root) {
        if(root == nullptr)
            return 0;
        return max(maxDepth(root->left),maxDepth(root->right))+1;
    }
};
```

# [二叉树最大宽度](https://leetcode-cn.com/problems/maximum-width-of-binary-tree/)

>给定一个二叉树，编写一个函数来获取这个树的最大宽度。树的宽度是所有层中的最大宽度。这个二叉树与满二叉树（full binary tree）结构相同，但一些节点为空。
>
>每一层的宽度被定义为两个端点（该层最左和最右的非空节点，两端点间的`null`节点也计入长度）之间的长度。

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    int widthOfBinaryTree(TreeNode* root) {
        if(!root)
            return 0;
        queue<TreeNode*> q;
        root->val = 1;
        q.push(root);
        int ans = 0;
        while(!q.empty())   {
            ans = max(q.back()->val - q.front()->val + 1, ans);
            int tmp = q.front()->val - 1;

            for(int i = q.size(); i > 0; --i)   {
                auto p = q.front(); q.pop();
                p->val -= tmp;
                if(p->left) {
                    p->left->val = 2 * p->val;
                    q.push(p->left); 
                }
                if(p->right)    {
                    p->right->val = 2 * p->val + 1;
                    q.push(p->right);
                }
            }
        }
        return ans;
    }
};
```



# [从前序与中序遍历序列构造二叉树](https://leetcode-cn.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)

**中序遍历第一个是root，前序遍历root前是左子树，后是右子树**

```c++
#include <unordered_map>
class Solution {
private:
    unordered_map<int, int> index;
public:
    TreeNode* myBuildTree(const vector<int>& preorder, const vector<int>& inorder, int preorder_left, int preorder_right, int inorder_left, int inorder_right) {
        if (preorder_left > preorder_right)
            return nullptr;
        int preorder_root = preorder_left;
        int inorder_root = index[preorder[preorder_root]];
        TreeNode* root = new TreeNode(preorder[preorder_root]);
        int size_left_subtree = inorder_root - inorder_left;
        root->left = myBuildTree(preorder, inorder, preorder_left + 1, preorder_left + size_left_subtree, inorder_left, inorder_root - 1);
        root->right = myBuildTree(preorder, inorder, preorder_left + size_left_subtree + 1, preorder_right, inorder_root + 1, inorder_right);
        return root;
    }
    TreeNode* reConstructBinaryTree(vector<int> pre,vector<int> vin) {
        int n = pre.size();
        for (int i = 0; i < n; ++i)
            index[vin[i]] = i;
        return myBuildTree(pre, vin, 0, n - 1, 0, n - 1);
    }
};
```

# [平衡二叉树](https://leetcode-cn.com/problems/balanced-binary-tree/)

> 给定一个二叉树，判断它是否是高度平衡的二叉树

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    int height(TreeNode *root){
        if(root == nullptr)
            return 0;
        return max(height(root->left),height(root->right))+1;
    }
    bool isBalanced(TreeNode* root) {
        if(root == nullptr)
            return true;
        else{
            return abs(height(root->left) - height(root->right)) <= 1 && isBalanced(root->left) && isBalanced(root->right);
        }
    }
};
```

# [验证二叉搜索树](https://leetcode-cn.com/problems/validate-binary-search-tree/)

> 给定一个二叉树，判断其是否是一个有效的二叉搜索树。

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    bool helper(TreeNode *root,int large,int small){
        if(root == nullptr)
            return true;
        if(root->val < large && root->val > small)
            return true;
        return helper(root->left,root->val,small) && helper(root->right,large,root->val);
    }
    bool isValidBST(TreeNode* root) {
        return helper(root,INT_MAX,INT_MIN);
    }
};
```

# [二叉搜索树中第K小的元素](https://leetcode-cn.com/problems/kth-smallest-element-in-a-bst/)

> 给定一个二叉搜索树的根节点 `root` ，和一个整数 `k` ，请你设计一个算法查找其中第 `k` 个最小元素（从 1 开始计数）。

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
private:
    int res;
public:
    void dfs(TreeNode *root,int &k)
    {
        if(root == nullptr)
            return;
        dfs(root->left,k);
        if(--k == 0)
            res = root->val;
        dfs(root->right,k);
    }
    int kthSmallest(TreeNode* root, int k) {
        dfs(root,k);
        return res;
    }
};
```

# [二叉搜索树的第k大节点](https://leetcode-cn.com/problems/er-cha-sou-suo-shu-de-di-kda-jie-dian-lcof)

> 给定一棵二叉搜索树，请找出其中第k大的节点。

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    int res = 0;
    void inorder(TreeNode* root, int &k){ 
        if(root == NULL)    return ;

        inorder(root->right, k);  
        if(--k == 0)    
        {
            res = root->val;
            return ;
        }
        inorder(root->left , k);
    }
    int kthLargest(TreeNode* root, int k) {
        inorder(root, k);
        return res;;
    }
};
```



# [二叉搜索树与双向链表](https://leetcode-cn.com/problems/er-cha-sou-suo-shu-yu-shuang-xiang-lian-biao-lcof/)

> 输入一棵二叉搜索树，将该二叉搜索树转换成一个排序的循环双向链表。要求不能创建任何新的节点，只能调整树中节点指针的指向。

- leetcode

```c++
class Solution {
public:
    Node *pre,*head;
    void dfs(Node *cur)
    {
        if(cur == nullptr)
            return;
        dfs(cur->left);
        if(pre != nullptr)
            pre->right = cur;
        else
            head = cur;
        cur->left = pre;
        pre = cur;
        dfs(cur->right);
    }
    Node* treeToDoublyList(Node* root) {
        if(root==NULL) return NULL;
        dfs(root);
        head->left=pre;
        pre->right=head;
        return head;
    }
};
```

- 牛客

```c++
/*
struct TreeNode {
	int val;
	struct TreeNode *left;
	struct TreeNode *right;
	TreeNode(int x) :
			val(x), left(NULL), right(NULL) {
	}
};*/
class Solution {
public:
    void inorder(TreeNode* pRoot){
        if(!pRoot)
            return;
        inorder(pRoot->left);
        pRoot->left=last;
        if(last)
            last->right=pRoot;
        last=pRoot;
        inorder(pRoot->right);
    }
    TreeNode* last=nullptr;
    TreeNode* Convert(TreeNode *pRootOfTree) {
        if(!pRootOfTree)
            return pRootOfTree;
        TreeNode* p=pRootOfTree;
        while(p&&p->left)
            p=p->left;
        inorder(pRootOfTree);
        return p;
    }
};
```

# [将有序数组转换为二叉搜索树](https://leetcode-cn.com/problems/convert-sorted-array-to-binary-search-tree/)

> 给你一个整数数组 `nums` ，其中元素已经按 **升序** 排列，请你将其转换为一棵 **高度平衡** 二叉搜索树。

> **高度平衡** 二叉树是一棵满足「每个节点的左右两个子树的高度差的绝对值不超过 1 」的二叉树。

```c++
class Solution {
public:
    TreeNode* sortedArrayToBST(vector<int>& nums) {
        return helper(nums, 0, nums.size() - 1);
    }

    TreeNode* helper(vector<int>& nums, int left, int right) {
        if (left > right) {
            return nullptr;
        }

        // 总是选择中间位置左边的数字作为根节点
        int mid = (left + right) / 2;

        TreeNode* root = new TreeNode(nums[mid]);
        root->left = helper(nums, left, mid - 1);
        root->right = helper(nums, mid + 1, right);
        return root;
    }
};
```

# [二叉树的完全性检验](https://leetcode-cn.com/problems/check-completeness-of-a-binary-tree/)

> 给定一个二叉树，确定它是否是一个*完全二叉树*。

**层次遍历**

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    bool isCompleteTree(TreeNode* root) {
        if (!root)
            return true;
        queue<TreeNode*> q;
        q.push(root);
        bool found = false;
        while (!q.empty()) {
            auto cur = q.front();
            q.pop();
            if (cur == nullptr) {
                found = true;
            } else {
                if (found)
                    return false;
                q.push(cur->left);
                q.push(cur->right);
            }
        }
        return true;
    }
};
```

# [对称二叉树](https://leetcode-cn.com/problems/symmetric-tree/)

> 给定一个二叉树，检查它是否是镜像对称的。

**递归：check(p,q)**

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    bool check(TreeNode *p,TreeNode *q){
        if(p == nullptr && q == nullptr)
            return true;
        else if(p == nullptr || q == nullptr)
            return false;
        return p->val == q->val && check(p->left,q->right) && check(p->right,q->left);
    }
    bool isSymmetric(TreeNode* root) {
        return check(root,root);
    }
};
```

# [二叉树的镜像](https://leetcode-cn.com/problems/er-cha-shu-de-jing-xiang-lcof/)

> 请完成一个函数，输入一个二叉树，该函数输出它的镜像。

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    TreeNode* mirrorTree(TreeNode* root) {
        if (root == nullptr) 
            return nullptr;
        TreeNode *leftRoot = mirrorTree(root->right);
        TreeNode *rightRoot = mirrorTree(root->left);
        root->left = leftRoot;
        root->right = rightRoot;
        return root;
    }
};
```



# [二叉树的直径](https://leetcode-cn.com/problems/diameter-of-binary-tree/)

> 给定一棵二叉树，你需要计算它的直径长度。一棵二叉树的直径长度是任意两个结点路径长度中的最大值。这条路径可能穿过也可能不穿过根结点

**递归**

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    int inorder(TreeNode *root,int &res){
        if(root == nullptr)
            return 0;
        int left = inorder(root->left,res);
        int right = inorder(root->right,res);
        res = max(res,(left+right+1));
        return max(left,right)+1;
    }
    int diameterOfBinaryTree(TreeNode* root) {
        int res = 1;
        inorder(root,res);
        return res-1;
    }
};
```

# [二叉树展开为链表](https://leetcode-cn.com/problems/flatten-binary-tree-to-linked-list/)

> 给你二叉树的根结点 `root` ，请你将它展开为一个单链表

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    void order(TreeNode *root,vector<TreeNode *> &save)
    {
        if(root == nullptr)
            return;
        save.push_back(root);
        order(root->left,save);
        order(root->right,save);
    }
    void flatten(TreeNode* root) {
        vector<TreeNode *> save;
        order(root,save);
        for(int i = 1;i < save.size();i++)
        {
            TreeNode *cur = save[i];
            TreeNode *pre = save[i-1];
            pre->right = cur;
            pre->left = nullptr;
        }
    }
};
```



# [求根到叶子节点数字之和](https://leetcode-cn.com/problems/sum-root-to-leaf-numbers/)

> 给定一个二叉树，它的每个结点都存放一个 `0-9` 的数字，每条从根到叶子节点的路径都代表一个数字。计算从根到叶子节点生成的所有数字之和

**递归+dfs**

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    int dfs(TreeNode *root,int presum)
    {
        if(root == nullptr)
            return 0;
        int sum = presum*10 + root->val;
        if(root->left == nullptr && root->right == nullptr)
            return sum;
        else
            return dfs(root->left,sum) + dfs(root->right,sum);
    }
    int sumNumbers(TreeNode* root) {
        return dfs(root,0);
    }
};
```

# [路径总和](https://leetcode-cn.com/problems/path-sum/)

>给你二叉树的根节点 root 和一个表示目标和的整数 targetSum ，判断该树中是否存在 根节点到叶子节点 的路径，这条路径上所有节点值相加等于目标和 targetSum 。

**递归**

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    bool hasPathSum(TreeNode* root, int targetSum) {
        if(root == nullptr)
            return false;
        if(root->left == nullptr && root->right == nullptr)
            return root->val == targetSum;
        return hasPathSum(root->left,targetSum - root->val) || hasPathSum(root->right,targetSum - root->val);
    }
};
```

# [最小路径和](https://leetcode-cn.com/problems/minimum-path-sum/)

>定一个包含非负整数的 `*m* x *n*` 网格 `grid` ，请找出一条从左上角到右下角的路径，使得路径上的数字总和为最小。**说明：**每次只能向下或者向右移动一步。

**动态规划**

```c++
class Solution {
public:
    int minPathSum(vector<vector<int>>& grid) {
        for(int i = 0;i < grid.size();i++){
            for(int j = 0; j < grid[0].size();j++){
                if(i == 0 && j == 0)
                    continue;
                else if(i == 0)
                    grid[i][j] = grid[i][j] + grid[i][j-1];
                else if(j == 0)
                    grid[i][j] = grid[i][j] + grid[i-1][j];
                else    
                    grid[i][j] = min(grid[i-1][j],grid[i][j-1]) + grid[i][j];
            }
        }
        return grid[grid.size()-1][grid[0].size()-1];
    }
};
```



# [回文链表](https://leetcode-cn.com/problems/palindrome-linked-list/)

> 请判断一个链表是否为回文链表。

- **获取链表中部：endofhalf**
- **翻转链表**

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode *endofhalf(ListNode *head){
        ListNode *fast = head;
        ListNode *slow = head;
        while(fast->next != nullptr && fast->next->next != nullptr){
            fast = fast->next->next;
            slow = slow->next;
        }
        return slow;
    }
    ListNode *reverse(ListNode *head)
    {
        if(head == nullptr ||  head->next == nullptr)
            return head;
        ListNode *dumphead = reverse(head->next);
        head->next->next = head;
        head->next = nullptr;
        return dumphead;
    }
    bool isPalindrome(ListNode* head) {
        if(head == nullptr)
            return true;
        ListNode *end = endofhalf(head);
        ListNode *second = reverse(end->next);
        while(second != nullptr){
            if(second->val != head->val)
                return false;
            else{
                head = head->next;
                second = second->next;
            }
        }
        return true;
    }
};
```

# [最长回文子串](https://leetcode-cn.com/problems/longest-palindromic-substring/)

> 给你一个字符串 `s`，找到 `s` 中最长的回文子串。

????

# [合并两个有序数组](https://leetcode-cn.com/problems/merge-sorted-array/)

>给你两个有序整数数组 nums1 和 nums2，请你将 nums2 合并到 nums1 中，使 nums1 成为一个有序数组。初始化 nums1 和 nums2 的元素数量分别为 m 和 n 。你可以假设 nums1 的空间大小等于 m + n，这样它就有足够的空间保存来自 nums2 的元素。

```c++
class Solution {
public:
    void merge(vector<int>& nums1, int m, vector<int>& nums2, int n) {
        int pos = m-- + n-- -1;
        while(m >= 0 && n >= 0)
            nums1[pos--] = nums1[m] > nums2[n] ? nums1[m--]:nums2[n--];
        while(n >= 0)
            nums1[pos--] = nums2[n--];
    }
};
```

# [最小栈](https://leetcode-cn.com/problems/min-stack/)

> 设计一个支持 `push` ，`pop` ，`top` 操作，并能在常数时间内检索到最小元素的栈

```c++
class MinStack {
private:
    stack<int> sk;
    stack<int> minsk;
public:
    /** initialize your data structure here. */
    MinStack() {
        minsk.push(INT_MAX);
    }
    
    void push(int x) {
        sk.push(x);
        minsk.push(min(minsk.top(),x));
    }
    
    void pop() {
        sk.pop();
        minsk.pop();
    }
    
    int top() {
        return sk.top();
    }
    
    int getMin() {
        return minsk.top();
    }
};

/**
 * Your MinStack object will be instantiated and called as such:
 * MinStack* obj = new MinStack();
 * obj->push(x);
 * obj->pop();
 * int param_3 = obj->top();
 * int param_4 = obj->getMin();
 */
```

# [用两个栈实现队列](https://leetcode-cn.com/problems/yong-liang-ge-zhan-shi-xian-dui-lie-lcof)

> 请你仅使用两个栈实现先入先出队列。队列应当支持一般队列的支持的所有操作（`push`、`pop`、`peek`、`empty`）

**双栈**

```c++
class MyQueue {
private:
    stack<int> A;
    stack<int> B;
public:
    /** Initialize your data structure here. */
    MyQueue() {

    }
    
    /** Push element x to the back of queue. */
    void push(int x) {
        A.push(x);
    }
    
    /** Removes the element from in front of queue and returns that element. */
    int pop() {
        if(A.empty())
            return -1;
        while(!A.empty()){
            B.push(A.top());
            A.pop();
        }
        int res = B.top();
        B.pop();
        while(!B.empty()){
            A.push(B.top());
            B.pop();
        }
        return res;
    }
    
    /** Get the front element. */
    int peek() {
        if(A.empty())
            return -1;
        while(!A.empty()){
            B.push(A.top());
            A.pop();
        }
        int res = B.top();
        while(!B.empty()){
            A.push(B.top());
            B.pop();
        }
        return res;
    }
    
    /** Returns whether the queue is empty. */
    bool empty() {
        return A.empty();
    }
};

/**
 * Your MyQueue object will be instantiated and called as such:
 * MyQueue* obj = new MyQueue();
 * obj->push(x);
 * int param_2 = obj->pop();
 * int param_3 = obj->peek();
 * bool param_4 = obj->empty();
 */
```

# [用队列实现栈](https://leetcode-cn.com/problems/implement-stack-using-queues)

```c++
class MyStack {
private:
    queue<int> q1;
    queue<int> q2;
public:
    /** Initialize your data structure here. */
    MyStack() {

    }
    
    /** Push element x onto stack. */
    void push(int x) {
        q2.push(x);
        while(!q1.empty())
        {
            q2.push(q1.front());
            q1.pop();
        }
        swap(q1,q2);
    }
    
    /** Removes the element on top of the stack and returns that element. */
    int pop() {
        int r = q1.front();
        q1.pop();
        return r;
    }
    
    /** Get the top element. */
    int top() {
        return q1.front();
    }
    
    /** Returns whether the stack is empty. */
    bool empty() {
        return q1.empty();
    }
};

/**
 * Your MyStack object will be instantiated and called as such:
 * MyStack* obj = new MyStack();
 * obj->push(x);
 * int param_2 = obj->pop();
 * int param_3 = obj->top();
 * bool param_4 = obj->empty();
 */
```

# [二分查找](https://leetcode-cn.com/problems/binary-search/)

>给定一个 n 个元素有序的（升序）整型数组 nums 和一个目标值 target  ，写一个函数搜索 nums 中的 target，如果目标值存在返回下标，否则返回 -1。

```c++
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int left = 0;
        int right = nums.size()-1;
        while(left <= right)
        {
            int mid = (left + right)/2;
            if(nums[mid] > target)
                right = mid-1;
            else if(nums[mid] < target)
                left = mid+1;
            else    
                return mid;
        }
        return -1;
    }
};
```

# 二分查找(牛客)

>  请实现有重复数字的升序数组的二分查找。 输出在数组中**第一个**大于等于查找值的位置，如果数组中不存在这样的数(指不存在大于等于查找值的数)，则输出数组长度加一

```c++
    int upper_bound_(int n, int v, vector<int>& a) {
        // write code here
        int l = 0,r = n;
        while(l < r){
            int mid = l + r >> 1;
            if(a[mid] < v)
                l = mid + 1;
            else
                r = mid;
        }
        return l + 1;
    }
```

# [ x 的平方根](https://leetcode-cn.com/problems/sqrtx/)

> 实现 `int sqrt(int x)` 函数。

**二分**

```c++
class Solution {
public:
    int mySqrt(int x) {
        int left = 0;
        int right = x;
        int res = 0;
        while(left <= right){
            int mid = (long long)(left + right)/2;
            if(mid * mid <= x){
                left = mid + 1;
                res = mid;
            }
            else
                right = mid -1;
        }
        return res;
    }
};
```

# [在排序数组中查找元素的第一个和最后一个位置](https://leetcode-cn.com/problems/find-first-and-last-position-of-element-in-sorted-array/)

>给定一个按照升序排列的整数数组 nums，和一个目标值 target。找出给定目标值在数组中的开始位置和结束位置,如果数组中不存在目标值 target，返回 [-1, -1]

```c++
class Solution { 
public:
    int binarySearch(vector<int>& nums, int target, bool lower) {
        int left = 0, right = (int)nums.size() - 1, ans = (int)nums.size();
        while (left <= right) {
            int mid = (left + right) / 2;
            if (nums[mid] > target || (lower && nums[mid] >= target)) {
                right = mid - 1;
                ans = mid;
            } else {
                left = mid + 1;
            }
        }
        return ans;
    }

    vector<int> searchRange(vector<int>& nums, int target) {
        int leftIdx = binarySearch(nums, target, true);
        int rightIdx = binarySearch(nums, target, false) - 1;
        if (leftIdx <= rightIdx && rightIdx < nums.size() && nums[leftIdx] == target && nums[rightIdx] == target) {
            return vector<int>{leftIdx, rightIdx};
        } 
        return vector<int>{-1, -1};
    }
};
```

# [搜索旋转排序数组](https://leetcode-cn.com/problems/search-in-rotated-sorted-array/)

> 整数数组 `nums` 按升序排列，数组中的值 **互不相同** 。在传递给函数之前，nums 在预先未知的某个下标 k（0 <= k < nums.length）上进行了 旋转，使数组变为 [nums[k], nums[k+1], ..., nums[n-1], nums[0], nums[1], ..., nums[k-1]]（下标 从 0 开始 计数）。例如， [0,1,2,4,5,6,7] 在下标 3 处经旋转后可能变为 [4,5,6,7,0,1,2] 。
>
> 给你 旋转后 的数组 nums 和一个整数 target ，如果 nums 中存在这个目标值 target ，则返回它的索引，否则返回 -1 。

**二分！！！！**

```c++
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int n = nums.size();
        if(n == 0)
            return -1;
        if(n == 1)
            return nums[0] == target ? nums[0] : -1;
        int left = 0;
        int right = n-1;
        while(left <= right){
            int mid = (left + right)/2;
            if(nums[mid] == target)
                return mid;
            if(nums[0] <= nums[mid]){
                if(nums[0] <= target && target < nums[mid])
                    right = mid - 1;
                else
                    left = mid + 1;
            }
            else{
                if(nums[mid] < target && target <= nums[n-1])
                    left = mid + 1;
                else
                    right = mid -1;
            }
        }
        return -1;
    }
};
```

# [旋转图像](https://leetcode-cn.com/problems/rotate-image/)

> 给定一个 *n* × *n* 的二维矩阵 `matrix` 表示一个图像。请你将图像顺时针旋转 90 度。

```c++
class Solution {
public:
    void rotate(vector<vector<int>>& matrix) {
        int row = matrix.size();
        int col = matrix[0].size();
        for(int i = 0;i < row/2;i++)
        {
            for(int j = 0;j < col;j++)
                swap(matrix[i][j],matrix[row-i-1][j]);
        }
        for(int i = 0;i < row;i++)
        {
            for(int j = 0;j < i;j++)
                swap(matrix[i][j],matrix[j][i]);
        }
    }
};
```

# [旋转数组](https://leetcode-cn.com/problems/rotate-array/)

> 给定一个数组，将数组中的元素向右移动 `k` 个位置，其中 `k` 是非负数。

```c++
class Solution {
public:
    void reverse(vector<int>& nums, int start, int end) {
        while (start < end) {
            swap(nums[start], nums[end]);
            start += 1;
            end -= 1;
        }
    }

    void rotate(vector<int>& nums, int k) {
        k %= nums.size();
        reverse(nums, 0, nums.size() - 1);
        reverse(nums, 0, k - 1);
        reverse(nums, k, nums.size() - 1);
    }
};
```

# [调整数组顺序使奇数位于偶数前面](https://leetcode-cn.com/problems/diao-zheng-shu-zu-shun-xu-shi-qi-shu-wei-yu-ou-shu-qian-mian-lcof/)

> 输入一个整数数组，实现一个函数来调整该数组中数字的顺序，使得所有奇数位于数组的前半部分，所有偶数位于数组的后半部分

- 不稳定

```c++
class Solution {
public:
    vector<int> exchange(vector<int>& nums) {
        if(nums.size() == 0)
            return nums;
        int left = 0;
        int right = nums.size()-1;
        while(left < right)
        {
            if(nums[left] % 2 == 0 && nums[right] %2==0)
                right--;
            else if(nums[left] %2 != 0 && nums[right] %2 != 0)
                left++;
            else if(nums[left] %2 != 0 && nums[right] %2 == 0)
            {
                left++;
                right--;
            }
            else
            {
                swap(nums[left],nums[right]);
                left++;
                right--;
            }
        }
        return nums;
    }
};
```

- 稳定

```c++
class Solution {
public:
    /**
     * 代码中的类名、方法名、参数名已经指定，请勿修改，直接返回方法规定的值即可
     *
     * 
     * @param array int整型vector 
     * @return int整型vector
     */
    vector<int> reOrderArray(vector<int>& array) {
        // write code here
        vector<int> result;
        for(int i=0;i<array.size();i++){
            if(array[i]%2==1)
                result.push_back(array[i]);
        }
        for(int i=0;i<array.size();i++){
            if(array[i]%2==0)
                result.push_back(array[i]);
        }
        return result;
    }
};
```



# [寻找峰值](https://leetcode-cn.com/problems/find-peak-element/)

>峰值元素是指其值大于左右相邻值的元素。给你一个输入数组 nums，找到峰值元素并返回其索引。数组可能包含多个峰值，在这种情况下，返回 任何一个峰值 所在位置即可。

**二分**

```c++
class Solution {
public:
    int findPeakElement(vector<int>& nums) {
        if(nums.size() == 0)
            return 0;
        int left = 0;
        int right = nums.size()-1;
        while(left < right){
            int mid = (long long)(left + right)/2;
            if(nums[mid] > nums[mid + 1])
                right = mid;
            else
                left = mid + 1;
        }
        return right;
    }
};
```

# [寻找两个正序数组的中位数](https://leetcode-cn.com/problems/median-of-two-sorted-arrays/)

> 给定两个大小为 m 和 n 的正序（从小到大）数组 `nums1` 和 `nums2`。请你找出并返回这两个正序数组的中位数。

```c++
class Solution {
public:
    /**
     * find median in two sorted array
     * @param arr1 int整型vector the array1
     * @param arr2 int整型vector the array2
     * @return int整型
     */
    int findkthitem(vector<int>& arr1, int index1, vector<int>& arr2, int index2, int kth)
    {
        int m = arr1.size() - index1, n = arr2.size() - index2;
        if(m > n) return findkthitem(arr2,index2,arr1,index1,kth);
        if(m==0) return arr2[index2+kth-1];
        if(kth==1) return min(arr1[index1], arr2[index2]);
        int pa = min(kth/2, m);
        int pb = kth - pa;
        if(arr1[index1+pa-1] == arr2[index2+pb-1]) return arr1[index1+pa-1];
        else if(arr1[index1+pa-1] < arr2[index2+pb-1])
            return findkthitem(arr1,index1+pa,arr2,index2,kth-pa);
        else
            return findkthitem(arr1,index1,arr2,index2+pb,kth-pb);
    }
    int findMedianinTwoSortedAray(vector<int>& arr1, vector<int>& arr2) {
        // write code here
        int allnum = arr1.size() + arr2.size();
        int res = 0;
        res = findkthitem(arr1, 0, arr2, 0, allnum/2);
        return res;
    }
};
```



# [搜索二维矩阵 II](https://leetcode-cn.com/problems/search-a-2d-matrix-ii/)

>编写一个高效的算法来搜索 m x n 矩阵 matrix 中的一个目标值 target 。该矩阵具有以下特性：
>
>每行的元素从左到右升序排列。
>每列的元素从上到下升序排列

```c++
class Solution {
public:
    bool searchMatrix(vector<vector<int>>& matrix, int target) {
        int row = matrix.size()-1;
        int col = 0;
        while(row >= 0 && col < matrix[0].size())
        {
            if(matrix[row][col] > target)
                row--;
            else if(matrix[row][col] < target)
                col++;
            else
                return true;
        }
        return false;
    }
};
```



# [缺失的第一个正数](https://leetcode-cn.com/problems/first-missing-positive/)

> 给你一个未排序的整数数组 `nums` ，请你找出其中没有出现的最小的正整数。

```c++
class Solution {
public:
    int firstMissingPositive(vector<int>& nums) {
        int n = nums.size();
        for(int i = 0;i < n;i++){
            while(nums[i] > 0 && nums[i] <= n && nums[nums[i]-1] != nums[i])
                swap(nums[nums[i] - 1],nums[i]);
        }
        for(int i = 0;i < n;i++){
            if(nums[i] != i+1)
                return i+1;
        }
        return n+1;
    }
};
```

# [岛屿数量](https://leetcode-cn.com/problems/number-of-islands/)

>给你一个由 '1'（陆地）和 '0'（水）组成的的二维网格，请你计算网格中岛屿的数量。岛屿总是被水包围，并且每座岛屿只能由水平方向和/或竖直方向上相邻的陆地连接形成。此外，你可以假设该网格的四条边均被水包围。

**DFS**

```c++
class Solution {
public:
    void dfs(vector<vector<char>> &grid,int i,int j){
        if(i < 0 || j < 0 || i >= grid.size() || j >= grid[0].size() || grid[i][j] == '0')
            return;
        grid[i][j] = '0';
        dfs(grid,i-1,j);
        dfs(grid,i+1,j);
        dfs(grid,i,j-1);
        dfs(grid,i,j+1);
    }
    int numIslands(vector<vector<char>>& grid) {
        int res = 0;
        for(int i = 0;i < grid.size();i++){
            for(int j = 0;j < grid[0].size();j++){
                if(grid[i][j] == '1'){
                    res++;
                    dfs(grid,i,j);
                }
            }
        }
        return res;
    }
};
```

# [岛屿的最大面积](https://leetcode-cn.com/problems/max-area-of-island/)

>给定一个包含了一些 0 和 1 的非空二维数组 grid 。一个 岛屿 是由一些相邻的 1 (代表土地) 构成的组合，这里的「相邻」要求两个 1 必须在水平或者竖直方向上相邻。你可以假设 grid 的四个边缘都被 0（代表水）包围着

**DFS**

```c++
class Solution {
public:
    int dfs(vector<vector<int>> &grid,int i,int j){
        if(i < 0 || j < 0 ||i >= grid.size() || j >= grid[0].size() || grid[i][j] == 0)
            return 0;
        grid[i][j] = 0;
        int res = 1;
        res += dfs(grid,i-1,j);
        res += dfs(grid,i+1,j);
        res += dfs(grid,i,j-1);
        res += dfs(grid,i,j+1);
        return res;
    }
    int maxAreaOfIsland(vector<vector<int>>& grid) {
        int res = 0;
        for(int i = 0;i < grid.size();i++){
            for(int j = 0;j < grid[0].size();j++){
                if(grid[i][j] == 1){
                    res = max(res,dfs(grid,i,j));
                }
            }
        }
        return res;
    }
};
```

# [单词搜索](https://leetcode-cn.com/problems/word-search/)

> 给定一个二维网格和一个单词，找出该单词是否存在于网格中。
>
> 单词必须按照字母顺序，通过相邻的单元格内的字母构成，其中“相邻”单元格是那些水平相邻或垂直相邻的单元格。同一个单元格内的字母不允许被重复使用

**DFS**

```c++
class Solution {
public:
    bool dfs(vector<vector<char>> &board,string word,int i,int j,int n){
        if(n == word.size())
            return true;
        if(i < 0 || j < 0 || i >= board.size() || j >= board[0].size() || board[i][j] != word[n])
        return false;
        char t = board[i][j];
        board[i][j] = '0';
        bool res = dfs(board,word,i+1,j,n+1) || 
                    dfs(board,word,i-1,j,n+1) ||
                    dfs(board,word,i,j+1,n+1) ||
                    dfs(board,word,i,j-1,n+1);
        board[i][j] = t;
        return res;
    }
    bool exist(vector<vector<char>>& board, string word) {
        for(int i =0;i < board.size();i++){
            for(int j = 0;j < board[0].size();j++){
                if(board[i][j] == word[0]){
                    if(dfs(board,word,i,j,0))
                        return true;
                }
            }
        }
        return false;
    }
};
```



# [最长公共子序列](https://leetcode-cn.com/problems/longest-common-subsequence/)

>给定两个字符串 text1 和 text2，返回这两个字符串的最长公共子序列的长度。一个字符串的 子序列 是指这样一个新的字符串：它是由原字符串在不改变字符的相对顺序的情况下删除某些字符（也可以不删除任何字符）后组成的新字符串。
>例如，"ace" 是 "abcde" 的子序列，但 "aec" 不是 "abcde" 的子序列。两个字符串的「公共子序列」是这两个字符串所共同拥有的子序列。若这两个字符串没有公共子序列，则返回 0。

**动态规划**

```c++
class Solution {
public:
    int longestCommonSubsequence(string text1, string text2) {
        int m = text1.size();
        int n = text2.size();
        int dp[m+1][n+1];
        memset(dp,0,sizeof(dp));
        for(int i = 1;i <= m;i++){
            for(int j = 1;j <= n;j++){
                if(text1[i-1] == text2[j-1])
                    dp[i][j] = dp[i-1][j-1] + 1;
                else
                    dp[i][j] = max(dp[i-1][j],dp[i][j-1]);
            }
        }
        return dp[m][n];
    }
};
```

# 最长公共子序列(牛客)

>给定两个字符串str1和str2,输出两个字符串的最长公共子串,题目保证str1和str2的最长公共子串存在且唯一。

```c++
class Solution {
public:
    /**
     * longest common substring
     * @param str1 string字符串 the string
     * @param str2 string字符串 the string
     * @return string字符串
     */
    string LCS(string str1, string str2) {
        // write code here
        int len1 = str1.size();
        int len2 = str2.size();
        if(len1 == 0 || len2 == 0)
            return "-1";
        vector<int> d(len2+1,0);
        int reslen = 0;
        int end;
        int temp;
        for(int i = 1;i <=len1;i++){
            int last = 0;
            for(int j = 1;j <= len2;j++){
                temp = d[j]; 
                if(str1[i-1] == str2[j-1])
                    d[j] = last + 1;
                else
                    d[j] = 0;
                last = temp;
                if(reslen < d[j]){
                    reslen = d[j];
                    end = j;
                }
            }
        }
        return reslen > 0 ? str2.substr(end - reslen,reslen) : "-1";
    }
};
```



# [爬楼梯](https://leetcode-cn.com/problems/climbing-stairs/)

>假设你正在爬楼梯。需要 *n* 阶你才能到达楼顶。每次你可以爬 1 或 2 个台阶。你有多少种不同的方法可以爬到楼顶呢？

**动态规划:dp[0] = 1,dp[1] =1**

```c++
class Solution {
public:
    int climbStairs(int n) {
        int dp[n+1];
        dp[0] = 1;
        dp[1] = 1;
        for(int i = 2;i <= n;i++)
            dp[i] = dp[i-2]+dp[i-1];
        return dp[n];
    }
};
```

# [青蛙跳台阶问题](https://leetcode-cn.com/problems/qing-wa-tiao-tai-jie-wen-ti-lcof)

> 一只青蛙一次可以跳上1级台阶，也可以跳上2级台阶。求该青蛙跳上一个 `n` 级的台阶总共有多少种跳法。答案需要取模 1e9+7（1000000007），如计算初始结果为：1000000008，请返回 1。

```c++
class Solution {
public:
    int numWays(int n) {
        int a = 1;
        int b = 1;
        int sum;
        for(int i = 0;i < n;i++)
        {
            sum = (a + b) % 1000000007;
            a = b;
            b = sum;
        }
        return a;
    }
};
```



# [打家劫舍](https://leetcode-cn.com/problems/house-robber/)

>你是一个专业的小偷，计划偷窃沿街的房屋。每间房内都藏有一定的现金，影响你偷窃的唯一制约因素就是相邻的房屋装有相互连通的防盗系统，如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警。给定一个代表每个房屋存放金额的非负整数数组，计算你 不触动警报装置的情况下 ，一夜之内能够偷窃到的最高金额。

**动态规划**

```c++
class Solution {
public:
    int rob(vector<int>& nums) {
        int dp[nums.size()];
        dp[0] = nums[0];
        dp[1] = max(nums[0],nums[1]);
        for(int i = 2;i < nums.size();i++)
            dp[i] = max(dp[i-1],dp[i-2]+nums[i]);
        return dp[nums.size()-1];
    }
};
```

# [不同路径](https://leetcode-cn.com/problems/unique-paths/)

>一个机器人位于一个 m x n 网格的左上角 （起始点在下图中标记为 “Start” ）。机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角（在下图中标记为 “Finish” ）。

**动态规划**

```c++
class Solution {
public:
    int uniquePaths(int m, int n) {
        int dp[m][n];
        for(int i = 0;i < m;i++)
            dp[i][0] = 1;
        for(int i = 0;i < n;i++)
            dp[0][i] = 1;
        for(int i = 1;i < m;i++){
            for(int j = 1;j < n;j++)
                dp[i][j] = dp[i-1][j] + dp[i][j-1];
        }
        return dp[m-1][n-1];
    }
};
```

# [最长重复子数组](https://leetcode-cn.com/problems/maximum-length-of-repeated-subarray/)

> 给两个整数数组 `A` 和 `B` ，返回两个数组中公共的、长度最长的子数组的长度。

**动态规划**

```c++
class Solution {
public:
    int findLength(vector<int>& A, vector<int>& B) {
        vector<vector<int>> dp(A.size()+1,vector<int>(B.size()+1,0));
        int res = 0;
        for(int i = A.size()-1;i >= 0;i--)
        {
            for(int j = B.size()-1;j >= 0;j--)
            {
                dp[i][j] = A[i] == B[j] ? dp[i+1][j+1]+1:0;
                res = max(res,dp[i][j]);
            }
        }
        return res;
    }
};
```



# [全排列](https://leetcode-cn.com/problems/permutations/)

> 给定一个 **没有重复** 数字的序列，返回其所有可能的全排列。

**回溯**

```c++
class Solution {
public:
    void backtrace(vector<int> &nums,vector<vector<int>> &res,int first,int len){
        if(first == len){
            res.push_back(nums);
            return;
        }
        for(int i = first;i < len;i++){
            swap(nums[i],nums[first]);
            backtrace(nums,res,first+1,len);
            swap(nums[i],nums[first]);
        }
    }
    vector<vector<int>> permute(vector<int>& nums) {
        vector<vector<int>> res;
        backtrace(nums,res,0,nums.size());
        return res;
    }
};
```

# 牛客##字符串排列

> 输入一个字符串,按字典序打印出该字符串中字符的所有排列。例如输入字符串abc,则按字典序打印出由字符a,b,c所能排列出来的所有字符串abc,acb,bac,bca,cab和cba

```c++
class Solution {
public:
    void permutations(string str,int index,int len,vector<string> &res)
    {
        if(index==len-1){
            res.push_back(str);
            return ;
        }
        for(int i=index;i<len;i++)
        {
            if(i!=index && str[i]==str[index])
                continue;
            swap(str[i],str[index]);
            permutations(str,index+1,str.size(),res);
        }
    }
    vector<string> Permutation(string str) {
        vector<string> res;
        if(str.size()==0) return res;
        permutations(str,0,str.size(),res);
        return res;
    }
};
```



# [子集](https://leetcode-cn.com/problems/subsets/)

> 给你一个整数数组 `nums` ，数组中的元素 **互不相同** 。返回该数组所有可能的子集（幂集）。

```c++
class Solution {
public:
    vector<vector<int>> res;
    vector<int> path;
    void backtrace(vector<int> &nums,int start)
    {
        res.push_back(path);
        if(start >= nums.size())
            return;
        for(int i = start;i < nums.size();i++)
        {
            path.push_back(nums[i]);
            backtrace(nums,i+1);
            path.pop_back();
        }
    }
    vector<vector<int>> subsets(vector<int>& nums) {
        backtrace(nums,0);
        return res;
    }
};
```



# [下一个排列](https://leetcode-cn.com/problems/next-permutation/)

>实现获取 下一个排列 的函数，算法需要将给定数字序列重新排列成字典序中下一个更大的排列。如果不存在下一个更大的排列，则将数字重新排列成最小的排列（即升序排列）。

```c++
class Solution {
public:
    void nextPermutation(vector<int>& nums) {
        int i = nums.size()-2;
        while(i >= 0 && nums[i] >= nums[i+1])
            i--;
        if(i >= 0){
            int j = nums.size()-1;
            while(j >= 0 && nums[i] >= nums[j])
                j--;
            swap(nums[i],nums[j]);
        }
        reverse(nums.begin()+1+i,nums.end());
    }
};
```



# [多数元素](https://leetcode-cn.com/problems/majority-element/)

> 给定一个大小为 *n* 的数组，找到其中的多数元素。多数元素是指在数组中出现次数 **大于** `⌊ n/2 ⌋` 的元素。

```c++
class Solution {
public:
    int majorityElement(vector<int>& nums) {
        int res = -1;
        int count = 0;
        for(int num:nums){
            if(res == num)
                count++;
            else if(--count < 0){
                res = num;
                count = 1;
            }
        }
        return res;
    }
};
```

# [合并区间](https://leetcode-cn.com/problems/merge-intervals/)

>以数组 intervals 表示若干个区间的集合，其中单个区间为 intervals[i] = [starti, endi] 。请你合并所有重叠的区间，并返回一个不重叠的区间数组，该数组需恰好覆盖输入中的所有区间

**排序**

```c++
class Solution {
public:
    vector<vector<int>> merge(vector<vector<int>>& intervals) {
        if(intervals.size() == 0)
            return {};
        vector<vector<int>> res;
        sort(intervals.begin(),intervals.end());
        for(int i = 0;i < intervals.size();i++){
            int L = intervals[i][0];
            int R = intervals[i][1];
            if(res.size() == 0 || res.back()[1] < L)
                res.push_back({L,R});
            else
                res.back()[1] = max(res.back()[1],R);
        }
        return res;
    }
};
```

# [每日温度](https://leetcode-cn.com/problems/daily-temperatures/)

> 请根据每日 `气温` 列表，重新生成一个列表。对应位置的输出为：要想观测到更高的气温，至少需要等待的天数。如果气温在这之后都不会升高，请在该位置用 `0` 来代替。

```c++
class Solution {
public:
    vector<int> dailyTemperatures(vector<int>& T) {
        vector<int> res(T.size());
        stack<int> s;
        for(int i = 0;i < T.size();i++)
        {
            while(!s.empty() && T[i] > T[s.top()])
            {
                int pre = s.top();
                res[pre] = i -pre;
                s.pop();
            }
            s.push(i);
        }
        return res;
    }
};
```



# [比较版本号](https://leetcode-cn.com/problems/compare-version-numbers/)

> 给你两个版本号 `version1` 和 `version2` ，请你比较它们。

```c++
class Solution {
public:
    int compareVersion(string version1, string version2) {
        int len_1 = version1.size(), len_2 = version2.size();
        int i = 0, j = 0;
        while (i < len_1 || j < len_2)
        {
            int cur_1 = i, cur_2 = j;                     //cur表示当前版本段的结束下标
            while (cur_1 < len_1 && version1[cur_1] != '.') cur_1 ++ ;  
            while (cur_2 < len_2 && version2[cur_2] != '.') cur_2 ++ ;
            //atoi()的参数是const char*,因此对于一个字符串str我们必须调用c_str()方法把这个string转换成const char*类型
            int a = (i == cur_1) ? 0 : atoi(version1.substr(i, cur_1 - i).c_str());
            int b = (j == cur_2) ? 0 : atoi(version2.substr(j, cur_2 - j).c_str());
            if (a > b) return 1;
            else if (a < b) return -1;
            i = cur_1 + 1, j = cur_2 + 1;
        }
        return 0;
    }
};
```



# [零钱兑换](https://leetcode-cn.com/problems/coin-change/)

>给定不同面额的硬币 coins 和一个总金额 amount。编写一个函数来计算可以凑成总金额所需的最少的硬币个数。如果没有任何一种硬币组合能组成总金额，返回 -1。你可以认为每种硬币的数量是无限的

**动态规划**

```c++
class Solution {
public:
    int coinChange(vector<int>& coins, int amount) {
        int Max = amount+1;
        vector<int> dp(amount+1,Max);
        dp[0] = 0;
        for(int i = 1;i <= amount;i++)
        {
            for(int j = 0;j < coins.size();j++)
            {
                if(coins[j] <= i)
                    dp[i] = min(dp[i], dp[i - coins[j]] + 1);
            }
        }
        return dp[amount] > amount ? -1 : dp[amount];
    }
};
```



# ###[零钱兑换 II](https://leetcode-cn.com/problems/coin-change-2/)

> 给定不同面额的硬币和一个总金额。写出函数来计算可以凑成总金额的硬币组合数。假设每一种面额的硬币有无限个。

```c++
class Solution {
public:
    int change(int amount, vector<int>& coins) {
        int* d = new int[amount+1];
        // 初始化
        memset(d, 0, sizeof(int)*(amount+1));
        d[0] = 1;
        int n = coins.size();
        for (int coin : coins)
        {
            // 这里直接从coin开始，否则 x-coin会变为无效数字
            for (int x = coin; x <= amount; ++x)
            {
                d[x] += d[x-coin];
            }
        }
        return d[amount];
    }
};
```



# [ 复原 IP 地址](https://leetcode-cn.com/problems/restore-ip-addresses/)

> 给定一个只包含数字的字符串，用以表示一个 IP 地址，返回所有可能从 `s` 获得的 **有效 IP 地址** 。你可以按任何顺序返回答案。

```c++
class Solution {
public:
    void dfs(string& s, vector<string>& res, vector<string>& cur, int pos) {
        int maxSize = (4 - cur.size()) * 3;
        if(s.size() - pos > maxSize)
            return;
        if(cur.size() == 4 && pos == s.size()) {
            res.push_back(cur[0] + '.' + cur[1] + '.' + cur[2] + '.' + cur[3]);
            return;
        }
        for(int i = pos; i < s.size(); i++) {
            string temp = s.substr(pos, i - pos + 1);
            if(isValid(temp)) {
                cur.push_back(temp);
                dfs(s, res, cur,i + 1);
                cur.pop_back();
            }
        }
    }
    bool isValid(string& s) {
        if((s.size() > 1 && s[0] == '0') || s.size() > 3)
            return false;
        return stoi(s) < 256;
    }
    vector<string> restoreIpAddresses(string s) {
        vector<string> arrResult, arrTmp;
        dfs(s, arrResult, arrTmp, 0);
        return arrResult;
    }
};
```



# [验证IP地址](https://leetcode-cn.com/problems/validate-ip-address/)

???

# [最小覆盖子串](https://leetcode-cn.com/problems/minimum-window-substring/)

> 给你一个字符串 `s` 、一个字符串 `t` 。返回 `s` 中涵盖 `t` 所有字符的最小子串。如果 `s` 中不存在涵盖 `t` 所有字符的子串，则返回空字符串 `""` 

**滑动窗口**

```c++
#include <unordered_map>
class Solution {
public:
    /**
     * 
     * @param S string字符串 
     * @param T string字符串 
     * @return string字符串
     */
    unordered_map <char, int> ori, cnt;
    bool check() {
        for (const auto &p: ori) {
            if (cnt[p.first] < p.second) {
                return false;
            }
        }
        return true;
    }
    string minWindow(string s, string t) {
        // write code here
        for (const auto &c: t) {
            ++ori[c];
        }
        int l = 0, r = -1;
        int len = INT_MAX, ansL = -1, ansR = -1;

        while (r < int(s.size())) {
            if (ori.find(s[++r]) != ori.end()) {
                ++cnt[s[r]];
            }
            while (check() && l <= r) {
                if (r - l + 1 < len) {
                    len = r - l + 1;
                    ansL = l;
                }
                if (ori.find(s[l]) != ori.end()) {
                    --cnt[s[l]];
                }
                ++l;
            }
        }
        return ansL == -1 ? string() : s.substr(ansL, len);
    }
};
```



# [用 Rand7() 实现 Rand10()](https://leetcode-cn.com/problems/implement-rand10-using-rand7/)

> 已有方法 `rand7` 可生成 1 到 7 范围内的均匀随机整数，试写一个方法 `rand10` 生成 1 到 10 范围内的均匀随机整数。

```c++
// The rand7() API is already defined for you.
// int rand7();
// @return a random integer in the range 1 to 7

class Solution {
public:
    int rand10() {
        int row, col, idx;
        do {
            row = rand7();
            col = rand7();
            idx = col + (row - 1) * 7;
        } while (idx > 40);
        return 1 + (idx - 1) % 10;
    }
};
```

# [反转字符串](https://leetcode-cn.com/problems/reverse-string/)

> 编写一个函数，其作用是将输入的字符串反转过来。输入字符串以字符数组 `char[]` 的形式给出。

```c++
class Solution {
public:
    void reverseString(vector<char>& s) {
        int left = 0;
        int right = s.size()-1;
        while(left < right)
        {
            swap(s[left],s[right]);
            left++;
            right--;
        }
    }
};
```

# [最长连续序列](https://leetcode-cn.com/problems/longest-consecutive-sequence/)

> 给定一个未排序的整数数组 `nums` ，找出数字连续的最长序列（不要求序列元素在原数组中连续）的长度。

```
输入：nums = [100,4,200,1,3,2]
输出：4
解释：最长数字连续序列是 [1, 2, 3, 4]。它的长度为 4。
```

```c++
class Solution {
public:
    int longestConsecutive(vector<int>& nums) {
        unordered_set<int> num_set;
        for (const int& num : nums) {
            num_set.insert(num);
        }

        int longestStreak = 0;

        for (const int& num : num_set) {
            if (!num_set.count(num - 1)) {
                int currentNum = num;
                int currentStreak = 1;

                while (num_set.count(currentNum + 1)) {
                    currentNum += 1;
                    currentStreak += 1;
                }

                longestStreak = max(longestStreak, currentStreak);
            }
        }

        return longestStreak;           
    }
};
```



# [最大数](https://leetcode-cn.com/problems/largest-number/)

> 给定一组非负整数 `nums`，重新排列它们每个数字的顺序（每个数字不可拆分）使之组成一个最大的整数。

```c++
class Solution {
public:
    static bool comparison(const int& a, const int& b)
    {
        string concatenation1 = to_string(a) + to_string(b);
        string concatenation2 = to_string(b) + to_string(a);   
        return concatenation1 > concatenation2;
    }
    string largestNumber(vector<int>& nums) {
        if(nums.empty()) 
            return "";
        if(nums.size() == 1) 
            return to_string(nums[0]);
        sort(nums.begin(), nums.end(), comparison); // 调用STL中的sort函数
        string result = "";
        for(int i : nums)
        {
            result += to_string(i);
        }
        if(result[0] == '0')
             return "0"; // 特殊case，全是0的时候应该输出0而不是00000
        return result;
    }
};
```

# [滑动窗口最大值](https://leetcode-cn.com/problems/sliding-window-maximum/)

>给你一个整数数组 nums，有一个大小为 k 的滑动窗口从数组的最左侧移动到数组的最右侧。你只可以看到在滑动窗口内的 k 个数字。滑动窗口每次只向右移动一位。返回滑动窗口中的最大值。

```c++
class Solution {
public:
    vector<int> maxSlidingWindow(vector<int>& nums, int k) {
        priority_queue<pair<int,int>> q;
        int n = nums.size();
        for(int i = 0;i < k;i++)
            q.emplace(nums,i);
        vector<int> res = {q.top().first};
        for(int i = k;i < n ;i++)
        {
            q.emplace(nums[i],i);
            while(q.top().second <= i-k)
                q.pop();
            res.push_back(q.top().first);
        }
        return res;
    }
};
```

# [扑克牌中的顺子](https://leetcode-cn.com/problems/bu-ke-pai-zhong-de-shun-zi-lcof/)

>从扑克牌中随机抽5张牌，判断是不是一个顺子，即这5张牌是不是连续的。2～10为数字本身，A为1，J为11，Q为12，K为13，而大、小王为 0 ，可以看成任意数字。A 不能视为 14。

```c++
class Solution {
public:
    bool isStraight(vector<int>& nums) {
        int zero = 0;
        for(int i =0 ;i < 4;i++)
        {
            if(nums[i] == 0)
            {
                zero++;
                continue;
            }
            if(nums[i] == nums[i+1])
                return false;
            zero -= nums[i+1]-nums[i] - 1;
        }
        return zero >= 0 ? true:false;
    }
};
```

# 快速排序

```c++
    void quickSort(vector<int> &arr,int l,int r){
        if(l>=r-1) return;
        int tmp=arr[r-1];
        int i=l;
        for(int j=l;j<r-1;j++){
            if(arr[j]<=tmp){
                swap(arr[j],arr[i]);
                i++;
            }
        }
        swap(arr[i],arr[r-1]);
        quickSort(arr,l,i);
        quickSort(arr,i+1,r);
    }
    vector<int> MySort(vector<int>& arr) {
        // write code here
        quickSort(arr,0,arr.size());
        return arr;
    }
};
```

# 最长递增子序列

# [最长上升子序列](https://leetcode-cn.com/problems/longest-increasing-subsequence)

牛客

```c++
class Solution {
public:
    /**
     * retrun the longest increasing subsequence
     * @param arr int整型vector the array
     * @return int整型vector
     */
    vector<int> LIS(vector<int>& arr)
    {
        // 第一步：利用贪心+二分求最长递增子序列长度
        vector<int> res;
        vector<int> maxLen;
        if (arr.size()<1) return res;
        res.push_back(arr[0]);
        maxLen.push_back(1);
        for (int i = 1; i < arr.size(); ++i)
        {
            if (arr[i] > res.back())
            {
                res.push_back(arr[i]);
                maxLen.push_back(res.size());
            }
            else
            {
                // lower_bound(begin, end, val)包含在<algorithm>中
                // 它的作用是返回有序数组begin..end中第一个大于等于val的元素的迭代器
                int pos = lower_bound(res.begin(), res.end(), arr[i]) - res.begin();
                res[pos] = arr[i];
                maxLen.push_back(pos+1);
            }
        }
        // 第二步：填充最长递增子序列
        //res中放置的是最长递增序列，maxLen存放的是arr数组中对应每个数字应该放在res数组中的位置
        //从maxLen的最后往前遍历，依次根据maxLen中元素从大到小取对应的arr中的元素，遇到重复的应选择第一个。
        for (int i = arr.size()-1, j = res.size(); j > 0; --i)
        {
            if (maxLen[i] == j)
            {
                res[--j] = arr[i];
            }
        }
        return res;
    }
};
```

leetcode

```c++
class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        int len = 1, n = (int)nums.size();
        if (n == 0) {
            return 0;
        }
        vector<int> d(n + 1, 0);
        d[len] = nums[0];
        for (int i = 1; i < n; ++i) {
            if (nums[i] > d[len]) {
                d[++len] = nums[i];
            } else {
                int l = 1, r = len, pos = 0; // 如果找不到说明所有的数都比 nums[i] 大，此时要更新 d[1]，所以这里将 pos 设为 0
                while (l <= r) {
                    int mid = (l + r) >> 1;
                    if (d[mid] < nums[i]) {
                        pos = mid;
                        l = mid + 1;
                    } else {
                        r = mid - 1;
                    }
                }
                d[pos + 1] = nums[i];
            }
        }
        return len;
    }
};
```



# 旋转排序数组搜索

```c++
class Solution {
public:
    /**
     * 
     * @param A int整型一维数组 
     * @param n int A数组长度
     * @param target int整型 
     * @return int整型
     */
    int search(int* A, int n, int target) {
        // write code here
        int left = 0;
        int right=n-1;
        while(left <= right){
            int mid = left +(right-left)/2;
            if(A[mid] == target)
                return mid;
            else if(A[mid] >= A[left]){
                //左侧有序
                if(A[mid] > target && A[left] <= target)
                    right=mid-1;
                else
                    left=mid+1;
            }
            else{
                //右侧有序
                if(A[mid]<target && A[right] >= target)
                    left=mid+1;
                else
                    right=mid-1;
            }
        }
        return -1;
    }
};
```

# 最长回文子串

>对于一个字符串，请设计一个高效算法，计算其中最长回文子串的长度。给定字符串**A**以及它的长度**n**，请返回最长回文子串的长度。

```c++
class Solution {
public:
    int getLongestPalindrome(string A, int n) {
        // write code here
        int left = 0;
        int right = 0;
        int res = 0;
        for(int i = 0;i < n;i++){
            left = i;
            right = i;
            while(right < n && left >= 0 && A[right] == A[left]){
                left--;
                right++;
            }
            res = max(res,right - left - 1);
            left = i;
            right = i + 1;
            while(right < n && left >= 0 && A[right] == A[left]){
                left--;
                right++;
            }
            res = max(res,right - left -1);
        }
        return res;
    }
};
```

# [验证回文串](https://leetcode-cn.com/problems/valid-palindrome/)

> 给定一个字符串，验证它是否是回文串，只考虑字母和数字字符，可以忽略字母的大小写。

```c++
class Solution {
public:
    bool isPalindrome(string s) {
        string sgood;
        for (char ch: s) {
            if (isalnum(ch)) {
                sgood += tolower(ch);
            }
        }
        int n = sgood.size();
        int left = 0, right = n - 1;
        while (left < right) {
           if (sgood[left] != sgood[right]) {
                return false;
            }
            ++left;
            --right;
        }
        return true;
    }
};
```

# [验证回文字符串 Ⅱ](https://leetcode-cn.com/problems/valid-palindrome-ii)

> 给定一个非空字符串 `s`，**最多**删除一个字符。判断是否能成为回文字符串。

```c++
class Solution {
public:
    bool checkPalindrome(const string& s, int low, int high) {
        for (int i = low, j = high; i < j; ++i, --j) {
            if (s[i] != s[j]) {
                return false;
            }
        }
        return true;
    }

    bool validPalindrome(string s) {
        int low = 0, high = s.size() - 1;
        while (low < high) {
            char c1 = s[low], c2 = s[high];
            if (c1 == c2) {
                ++low;
                --high;
            } else {
                return checkPalindrome(s, low, high - 1) || checkPalindrome(s, low + 1, high);
            }
        }
        return true;
    }
};
```



# 单链表排序

```c++
/**
 * struct ListNode {
 *	int val;
 *	struct ListNode *next;
 * };
 */

class Solution {
public:
    /**
     * 
     * @param head ListNode类 the head node
     * @return ListNode类
     */
    ListNode *merge(ListNode *l1,ListNode *l2){
        if(l1 == nullptr)
            return l2;
        else if(l2 == nullptr)
            return l1;
        if(l1->val < l2->val){
            l1->next = merge(l1->next, l2);
            return l1;
        }
        else{
            l2->next = merge(l1, l2->next);
            return l2;
        }
    }
    ListNode *findmid(ListNode *head){
        ListNode *fast = head;
        ListNode *slow = head;
        ListNode *pre;
        while(fast != nullptr && fast->next != nullptr){
            pre = slow;
            slow = slow->next;
            fast = fast->next->next;
        }
        pre->next = nullptr;
        return slow;
    }
    ListNode *mergesort(ListNode *head){
        if(head->next == nullptr)
            return head;
        ListNode *mid = findmid(head);
        ListNode *l1 = mergesort(head);
        ListNode *l2 = mergesort(mid);
        return merge(l1, l2);
    }
    ListNode* sortInList(ListNode* head) {
        // write code here
        return head == nullptr ? nullptr : mergesort(head);
    }
};
```

- 数组中只出现一次

> 一个整型数组里除了两个数字之外，其他的数字都出现了两次。请写程序找出这两个只出现一次的数字

```c++
#include <unordered_map>
class Solution {
public:
    /**
     * 代码中的类名、方法名、参数名已经指定，请勿修改，直接返回方法规定的值即可
     *
     * 
     * @param array int整型vector 
     * @return int整型vector
     */
    vector<int> FindNumsAppearOnce(vector<int>& array) {
        // write code here
        int n = array.size();
        vector<int> res;
        unordered_map<int, int> map;
        for(int i = 0;i < n;i++)
            map[array[i]]++;
        for(int i = 0;i < n;i++){
            if(map[array[i]] == 1)
                res.push_back(array[i]);
        }
        return res;
    }
};
```

- 进制转化

> 给定一个十进制数M，以及需要转换的进制数N。将十进制数M转化为N进制数

```c++
class Solution {
public:
    /**
     * 进制转换
     * @param M int整型 给定整数
     * @param N int整型 转换到的进制
     * @return string字符串
     */
    string solve(int M, int N) {
        // write code here
        string ans;
        bool flag = 0;
        if (M<0) {
            M=-M;
            flag = 1;
        }
        const char* t = {"0123456789ABCDEF"} ;
        while (M!=0) {
            ans= t[M%N]+ans;
            M/=N;
        }
        if (flag)
            ans = '-'+ans;
        return ans;
    }
};
```

- 链表区间翻转

```c++
/**
 * struct ListNode {
 *	int val;
 *	struct ListNode *next;
 * };
 */

class Solution {
public:
    /**
     * 
     * @param head ListNode类 
     * @param m int整型 
     * @param n int整型 
     * @return ListNode类
     */
    ListNode* reverseBetween(ListNode* head, int m, int n) {
        // write code here
        ListNode *dummy=new ListNode(-1);
        dummy->next=head;
        ListNode *pre=dummy;
        for(int i=1;i<m;++i)
            pre=pre->next;
        head=pre->next;
        for(int i=m;i<n;++i){
            ListNode *nxt=head->next;
            head->next=nxt->next;
            nxt->next=pre->next;
            pre->next=nxt;
        }
        return dummy->next;
    }
};
```

- 数组中未出现的最小正整数

```c++
class Solution {
public:
    /**
     * return the min number
     * @param arr int整型vector the array
     * @return int整型
     */
    int minNumberdisappered(vector<int>& arr) {
        // write code here
        for(int i = 0;i < arr.size();i++){
            if(arr[i] < arr.size())
                swap(arr[i],arr[arr[i] - 1]);
        }
        for(int i = 0;i < arr.size();i++){
            if(arr[i] != i+1)
                return i+1;
        }
        return arr.size()+1;
    }
};
```

- 字符串转整数

```c++
class Solution {
public:
    int atoi(const char *str) {
         if (!strlen(str) ) return 0;
        int ans = 0;
        int i = 0;
        bool neg = false;
        while(str[i]==' ') 
            i++;
        if(str[i]=='-') {
            neg = true;
            i++;
        }
        if(str[i]=='+') i++;
        while(str[i]!='\0') {
            if(str[i]>'9'||str[i]<'0')
                break;
            if(ans>=INT_MAX/10) 
                return neg ? INT_MIN:INT_MAX;
            ans = 10*ans + str[i]-'0';
            i++;
        }
        return neg?-ans:ans;
    }
};
```

- 判断二叉树搜索和完全二叉树

```c++
/**
 * struct TreeNode {
 *	int val;
 *	struct TreeNode *left;
 *	struct TreeNode *right;
 * };
 */

class Solution {
public:
    /**
     * 
     * @param root TreeNode类 the root
     * @return bool布尔型vector
     */
    bool isSousuo(TreeNode* root,long long lowwer,long long upper)
    {
        if(root == nullptr)return true;
        if(root->val <= lowwer || root->val >= upper)return false;
        return isSousuo(root->left, lowwer, root->val)&&isSousuo(root->right, root->val, upper);
    }
    bool isComplete(TreeNode* root)
    {
        queue<TreeNode*>q;
        q.push(root);
        while(q.front())
        {
            root = q.front();
            q.pop();
            q.push(root->left);
            q.push(root->right);
        }
        while(!q.empty() && q.front() == nullptr)
        {
            q.pop();
        }
        return q.empty();
    }
    vector<bool> judgeIt(TreeNode* root) {
        // write code here
        vector<bool>res;
        res.push_back(isSousuo(root, LONG_MIN, LONG_MAX));
        res.push_back(isComplete(root));
        return res;
    }
};
```

- 排序

```c++
class Solution {
public:
    /**
     * 代码中的类名、方法名、参数名已经指定，请勿修改，直接返回方法规定的值即可
     * 将给定数组排序
     * @param arr int整型vector 待排序的数组
     * @return int整型vector
     */
    void quickSort(vector<int> &arr,int l,int r){
        if(l>=r-1) return;
        int tmp=arr[r-1];
        int i=l;
        for(int j=l;j<r-1;j++){
            if(arr[j]<=tmp){
                swap(arr[j],arr[i]);
                i++;
            }
        }
        swap(arr[i],arr[r-1]);
        quickSort(arr,l,i);
        quickSort(arr,i+1,r);
    }
    vector<int> MySort(vector<int>& arr) {
        // write code here
        quickSort(arr,0,arr.size());
        return arr;
    }
};
```

- 最小编辑代价

> 给定两个字符串str1和str2，再给定三个整数ic，dc和rc，分别代表插入、删除和替换一个字符的代价，请输出将str1编辑成str2的最小代价。

```c++
class Solution {
public:
    /**
     * min edit cost
     * @param str1 string字符串 the string
     * @param str2 string字符串 the string
     * @param ic int整型 insert cost
     * @param dc int整型 delete cost
     * @param rc int整型 replace cost
     * @return int整型
     */
    int minEditCost(string str1, string str2, int ic, int dc, int rc) {
        // write code here
        int n1 = str1.size();
        int n2 = str2.size();
        vector<vector<int>> dp(n1+1, vector<int>(n2+1,0));
        for(int i = 0;i<=n1;i++)
            dp[i][0]=i*dc;
        for(int j = 0;j<=n2;j++)
            dp[0][j] = j*ic;
        for(int i = 1; i<=n1; i++){
            for(int j = 1; j<=n2; j++){
                if(str1[i-1]==str2[j-1]){
                    dp[i][j]=dp[i-1][j-1];
                }else{
                    int ict = dp[i][j-1] + ic;
                    int dct = dp[i-1][j] + dc;
                    int rct = dp[i-1][j-1] + rc;
                    dp[i][j] = min({ict,dct, rct});
                }
            }
        }
        return dp[n1][n2];
    }
};
```

# 排序

## 选择排序

```c++
//选择排序
#include <iostream>
#include <vector>
using namespace std;
void selectSort(vector<int> &arr){
    for(int i = 0;i < arr.size();i++){
        int min = i;
        for(int j = i + 1;j < arr.size();j++){
            if(arr[j] < arr[min])
                min = j;
        }
        if(i != min){
            swap(arr[i],arr[min]);
        }
    }
}
int main(int argc, char const *argv[])
{
    vector<int> test{1,2,1,5,6,4,88,4,5,0,8};
    selectSort(test);
    for(int i : test)
        cout<<i<<' ';
    return 0;
}
```

## 插入排序

>// 将第一待排序序列第一个元素看做一个有序序列，把第二个元素到最后一个元素当成是未排序序列。
>
>从头到尾依次扫描未排序序列，将扫描到的每个元素插入有序序列的适当位置。
>
>如果待插入的元素与有序序列中的某个元素相等，则将待插入元素插入到相等元素的后面。

```c++
//插入排序
#include <iostream>
#include <vector>
using namespace std;
void insertSort(vector<int> &arr){
    for(int i = 1;i < arr.size();i++){
        int temp = arr[i];
        // 从已经排序的序列最右边的开始比较，找到比其小的数
        int j = i;
        while (j > 0 && temp < arr[j - 1])
        {
            arr[j] = arr[j - 1];
            j--;
        }
        if(j != i)
            arr[j] = temp;
    }
}   
int main(int argc, char const *argv[])
{
    vector<int> test{1,2,1,5,6,4,88,4,5,0,8};
    insertSort(test);
    for(int i : test)
        cout<<i<<' ';
    return 0;
}
```

## 快速排序

> 从数列中挑出一个元素，称为 “基准”（pivot）
>
> 重新排序数列，所有元素比基准值小的摆放在基准前面，所有元素比基准值大的摆在基准的后面（相同的数可以到任一边）。在这个分区退出之后，该基准就处于数列的中间位置。这个称为分区（partition）操作
>
> 递归地（recursive）把小于基准值元素的子数列和大于基准值元素的子数列排序；

```c++
//快速排序
#include <iostream>
#include <vector>
using namespace std;
int paritition(vector<int> &arr,int low,int high){
    int pivot = arr[low];
    while(low < high){
        while(low < high && arr[high] >= pivot)
            --high;
        arr[low] = arr[high];
        while(low < high && arr[low] <= pivot)
            ++low;
        arr[high] = arr[low];
    }
    arr[low] = pivot;
    return low;
}
void quickSort(vector<int> &arr,int low,int high){
    if(low < high){
        int pivot = paritition(arr,low,high);
        quickSort(arr,low,pivot - 1);
        quickSort(arr,pivot + 1,high);
    }
}  
int main(int argc, char const *argv[])
{
    vector<int> test{1,2,1,5,6,4,88,4,5,0,8};
    quickSort(test,0,test.size()-1);
    for(int i : test)
        cout<<i<<' ';
    return 0;
}
```

## 堆排序

**居然有人不会堆排序？？？**

```c++
//堆排序
#include <iostream>
#include <vector>
using namespace std;
void buildMaxHeap(vector<int> &arr,int i,int heapsize){
    int left = 2 * i + 1;
    int right = 2 * i + 2;
    int largest = i;
    if(left < heapsize && arr[left] > arr[largest])
        largest = left;
    if(right < heapsize && arr[right] > arr[largest])
        largest = right;
    if(largest != i){
        swap(arr[i],arr[largest]);
        buildMaxHeap(arr,largest,heapsize);
    }
}
void buildHeap(vector<int> &arr,int heapsize){
    for(int i = heapsize/2; i >= 0;i--)
        buildMaxHeap(arr,i,heapsize);
} 
void HeapSort(vector<int> &arr){
    int heapsize = arr.size();
    buildHeap(arr,heapsize);
    for(int i = heapsize - 1;i > 0;i--){
        swap(arr[0],arr[i]);
        heapsize--;
        buildMaxHeap(arr,0,heapsize);
    }
} 
int main(int argc, char const *argv[])
{
    vector<int> test{1,2,1,5,6,4,88,4,5,0,8};
    HeapSort(test);
    for(int i : test)
        cout<<i<<' ';
    return 0;
}
```

# [组合总和](https://leetcode-cn.com/problems/combination-sum)

>给定一个无重复元素的数组 candidates 和一个目标数 target ，找出 candidates 中所有可以使数字和为 target 的组合。candidates 中的数字可以无限制重复被选取。

```c++
class Solution {
private:
    vector<vector<int>> result;
    vector<int> path;
    void backtracking(vector<int>& candidates, int target, int sum, int startIndex)
    {
        if(sum > target) return;
        if(sum == target)
        {
            result.push_back(path);
            return;
        }
        //如果 sum + candidates[i] > target 就终止遍历
        for(int i = startIndex; i < candidates.size() && sum + candidates[i] <= target; i++)
        {
            sum += candidates[i];
            path.push_back(candidates[i]);
            backtracking(candidates, target, sum, i);//不用i+1，因为此时可以重复
            sum -= candidates[i];//回溯
            path.pop_back();//回溯
        }
    }
public:
    vector<vector<int>> combinationSum(vector<int>& candidates, int target) {
        result.clear();
        path.clear();
        sort(candidates.begin(), candidates.end());
        backtracking(candidates, target, 0, 0);
        return result;
    }
};
```

# [寻找重复数](https://leetcode-cn.com/problems/find-the-duplicate-number)

> 给定一个包含 `n + 1` 个整数的数组 `nums` ，其数字都在 `1` 到 `n` 之间（包括 `1` 和 `n`），可知至少存在一个重复的整数。

```c++
class Solution {
public:
    int findDuplicate(vector<int>& nums) {
    for(int i = 0; i < nums.size(); ++i){
        while(nums[i] != i + 1  && nums[i] != nums[nums[i] - 1])
            swap(nums[i], nums[nums[i] - 1]);
    }
    return nums.back();
    }
};
```

# [木头切割问题](https://mp.weixin.qq.com/s/o-1VJO2TQZjC5ROmV7CReA)

> 给定长度为n的数组，每个元素代表一个木头的长度，木头可以任意截断，从这堆木头中截出至少k个相同长度为m的木块。已知k，求max(m)。

```c++
#include <iostream>
using namespace std;
const int N = 100010;
int a[N];
int n, k;
int check(int mid)
{
    int res = 0;
    for (int i = 0; i < n; i ++ ) res += a[i] / mid;
    return res;
}
int main()
{
    cin >> n >> k;
    int l = 1, r = -1;
    for (int i = 0; i < n; i ++ )
    {
        cin >> a[i];
        r = max(r, a[i]);
    }
    while (l < r)
    {
        int mid = l + r + 1 >> 1;
        if (check(mid) >= k) l = mid;
        else r = mid - 1;
    } 
    cout << l << endl;
    return 0;
}
```

# [数组中的逆序对](https://leetcode-cn.com/problems/shu-zu-zhong-de-ni-xu-dui-lcof)

> 在数组中的两个数字，如果前面一个数字大于后面的数字，则这两个数字组成一个逆序对。输入一个数组,求出这个数组中的逆序对的总数P。并将P对1000000007取模的结果输出。 即输出P%1000000007

```c++
class Solution {
public:
    int cnt;
    void merge_sort(vector<int> &data, vector<int> &arr, int left, int right)
    {
        if(left < right){
            int section_len = (right-left)>>2;
            merge_sort(arr, data, left, left+section_len);
            merge_sort(arr, data, left+section_len+1, right);
            int mid_l = left+section_len, l = mid_l, r = right, idx = right;
            for( ; l>=left && r>mid_l; ){
                if(data[l] > data[r]){
                    cnt += r-mid_l;   cnt %= 1000000007;
                    arr[ idx-- ] = data[ l-- ];   continue;
                }
                arr[ idx-- ] = data[ r-- ];;
            }
            for( ; l>=left; ) arr[ idx-- ] = data[ l-- ];
            for( ; r>mid_l; ) arr[ idx-- ] = data[ r-- ];
        }
    }
    int InversePairs(vector<int> data) {
        cnt = 0;
        vector<int> arr( data );
        merge_sort(data, arr, 0, data.size()-1);
        return cnt;
    }
};
```

# [复制带随机指针的链表](https://leetcode-cn.com/problems/copy-list-with-random-pointer)

```c++
/*
// Definition for a Node.
class Node {
public:
    int val;
    Node* next;
    Node* random;
    
    Node(int _val) {
        val = _val;
        next = NULL;
        random = NULL;
    }
};
*/

class Solution {
public:
    Node* copyRandomList(Node* head) {
        unordered_map<Node*,Node*>mp;
        mp[nullptr]=nullptr;
        Node *p=head;
        while(p){
            mp[p]=new Node(p->val);
            p=p->next;
        }
        p=head;
        while(p){
            mp[p]->next=mp[p->next];
            p=p->next;
        }
        p=head;
        while(p){
            mp[p]->random=mp[p->random];
            p=p->next;
        }
        return mp[head];
    }
};
```

# [有序数组的平方](https://leetcode-cn.com/problems/squares-of-a-sorted-array)

> 给你一个按 **非递减顺序** 排序的整数数组 `nums`，返回 **每个数字的平方** 组成的新数组，要求也按 **非递减顺序** 排序。

```c++
class Solution {
public:
    vector<int> sortedSquares(vector<int>& nums) {
        int n = nums.size();
        vector<int> res(n);
        for(int i =0,j = n-1,pos = n-1;i <= j;){
            if(nums[i]*nums[i] > nums[j]*nums[j]){
                res[pos] = nums[i]*nums[i];
                i++;
            }
            else{
                res[pos] = nums[j] * nums[j];
                j--;
            }
            pos--;
        }
        return res;
    }
};
```

# [圆环回原点问题](https://mp.weixin.qq.com/s/VnGFEWHeD3nh1n9JSDkVUg)

> 圆环上有10个点，编号为0~9。从0点出发，每次可以逆时针和顺时针走一步，问走n步回到0点共有多少种走法。

```python
class Solution:
    def backToOrigin(self,n):
        #点的个数为10
        length = 10
        dp = [[0 for i in range(length)] for j in range(n+1)]
        dp[0][0] = 1
        for i in range(1,n+1):
            for j in range(length):
                #dp[i][j]表示从0出发，走i步到j的方案数
                dp[i][j] = dp[i-1][(j-1+length)%length] + dp[i-1][(j+1)%length]
        return dp[n][0]
```

# [最大交换](https://leetcode-cn.com/problems/maximum-swap)

> 给定一个非负整数，你**至多**可以交换一次数字中的任意两位。返回你能得到的最大值。

```c++
class Solution {
public:
    int maximumSwap(int num) {
        string res=to_string(num);
        const int n=res.size();
        unordered_map<char, int> m;
        for(int i=n-1; i>=0; i--){
            if(m.count(res[i])==0)
                m[res[i]]=i;
        }
        for(int i=0; i<n-1; i++){
            for(char c='9'; c>res[i]; c--){
                if(m.count(c) && m[c]>i){
                    std::swap(res[i], res[m[c]]);
                    return stoi(res);
                }
            }
        }
        return num;
    }
};
```

# [位1的个数](https://leetcode-cn.com/problems/number-of-1-bits)

> 编写一个函数，输入是一个无符号整数（以二进制串的形式），返回其二进制表达式中数字位数为 '1' 的个数（也被称为[汉明重量](https://baike.baidu.com/item/汉明重量)）

```c++
class Solution {
public:
//n&(n-1):消除最右边的1
    int hammingWeight(uint32_t n) {
        int res = 0;
        while(n != 0)
        {
            n = n&(n-1);
            res++;
        }
        return res;
    }
};
```

# [最长公共前缀](https://leetcode-cn.com/problems/longest-common-prefix)

> 编写一个函数来查找字符串数组中的最长公共前缀。

```c++
class Solution {
public:
    /**
     * 
     * @param strs string字符串vector 
     * @return string字符串
     */
    string longestCommonPrefix(vector<string>& strs) {
        // write code here
        if(strs.empty()) 
            return string();
        sort(strs.begin(), strs.end());
        string st = strs.front();
        string en = strs.back();
        int i, num = min(st.size(), en.size());
        for(i = 0; i < num && st[i] == en[i]; i ++);
        return string(st, 0, i);
    }
};
```

# [最小编辑代价](https://www.nowcoder.com/practice/05fed41805ae4394ab6607d0d745c8e4?tpId=196&tqId=37134&rp=1&ru=%2Factivity%2Foj&qru=%2Fta%2Fjob-code-total%2Fquestion-ranking&tab=answerKey)

> 给定两个字符串str1和str2，再给定三个整数ic，dc和rc，分别代表插入、删除和替换一个字符的代价，请输出将str1编辑成str2的最小代价。

```c++
class Solution {
public:
    /**
     * min edit cost
     * @param str1 string字符串 the string
     * @param str2 string字符串 the string
     * @param ic int整型 insert cost
     * @param dc int整型 delete cost
     * @param rc int整型 replace cost
     * @return int整型
     */
    int minEditCost(string str1, string str2, int ic, int dc, int rc) {
        // write code here
        int n1 = str1.size();
        int n2 = str2.size();
        vector<vector<int>> dp(n1+1, vector<int>(n2+1,0));
        for(int i = 0;i<=n1;i++)
            dp[i][0]=i*dc;
        for(int j = 0;j<=n2;j++)
            dp[0][j] = j*ic;
        for(int i = 1; i<=n1; i++){
            for(int j = 1; j<=n2; j++){
                if(str1[i-1]==str2[j-1]){
                    dp[i][j]=dp[i-1][j-1];
                }else{
                    int ict = dp[i][j-1] + ic;
                    int dct = dp[i-1][j] + dc;
                    int rct = dp[i-1][j-1] + rc;
                    dp[i][j] = min({ict,dct, rct});
                }
            }
        }
        return dp[n1][n2];
    }
};
```

# [函数 atoi](https://www.nowcoder.com/practice/44d8c152c38f43a1b10e168018dcc13f?tpId=196&tqId=37088&rp=1&ru=%2Factivity%2Foj&qru=%2Fta%2Fjob-code-total%2Fquestion-ranking&tab=answerKey)

```c++
class Solution {
public:
    int atoi(const char *str) {
         if (!strlen(str) ) return 0;
        int ans = 0;
        int i = 0;
        bool neg = false;
        while(str[i]==' ') 
            i++;
        if(str[i]=='-') {
            neg = true;
            i++;
        }
        if(str[i]=='+') i++;
        while(str[i]!='\0') {
            if(str[i]>'9'||str[i]<'0')
                break;
            if(ans>=INT_MAX/10) 
                return neg ? INT_MIN:INT_MAX;
            ans = 10*ans + str[i]-'0';
            i++;
        }
        return neg?-ans:ans;
    }
};
```

# [字典序的第K小数字](https://leetcode-cn.com/problems/k-th-smallest-in-lexicographical-order)

> 给定整数 `n` 和 `k`，找到 `1` 到 `n` 中字典序第 `k` 小的数字。

```c++
class Solution {
public:
typedef long long LL;
    int findKthNumber(int n, int k) {
        int prefix = 1;
        k--; // k记录要找的数字在prefix后的第几个
        while (k>0){
            int cnt = getCnt(n, prefix); // 当前prefix 下有多少个元素;包含prefix
            if (cnt <= k) { // 向右
                k -= cnt;
                prefix++;
            } else { // 向下
                k--;
                prefix*=10;
            }
        }
        return prefix;
    }
    int getCnt(LL n, LL prefix){
        LL cnt = 0;
        for (LL first = prefix, second = prefix+1; first<=n; first*=10, second*=10){
            cnt+= min(n + 1, second) - first;
        }
        return cnt;
    }
};
```

# [求区间最小数乘区间和的最大值](https://mp.weixin.qq.com/s/ABNN4lJpvttulwWaUTgYZQ)

>给定一个数组，要求选出一个区间, 使得该区间是所有区间中经过如下计算的值最大的一个：区间中的最小数 * 区间所有数的和。数组中的元素都是非负数。

```c++
//单调栈，时间复杂度O(N)
#include <iostream>
#include <vector>
#include <stack>
using namespace std;
const int N = 500000+10;
int a[N];
int dp[N];
stack<int> s;
int main()
{
    int n,res=0;
    cin >> n;
    for(int i = 0; i < n; i ++) cin >> a[i];
    //前缀和便于快速求区间和，例如求[l,r]区间和=dp[r+1]-dp[l]。l和r的取值范围是[0,n)
    for(int i = 1; i <= n; i ++) dp[i] = dp[i-1] + a[i-1]; 
    for(int i = 0; i < n; i ++) {
        while(!s.empty() && a[i] <= a[s.top()]) {
            int peak = a[s.top()];
            s.pop();
            int l = s.empty()? -1 : s.top();
            int r = i; 
            //l和r是边界，因此区间是[l+1,r-1]，其区间和dp[r+1]-dp[l]
            int dist = dp[r] - dp[l+1];
            res = max(res,peak*dist);
        }
        s.push(i);
    }
    while(!s.empty())
    {
        int peak = a[s.top()];
        s.pop();
        int l = s.empty()? -1 : s.top();
        int r = n; 
        
        int dist = dp[r] - dp[l+1];
        res = max(res,peak*dist);
    }
    cout << res << endl; 
}
```

# [最大正方形](https://leetcode-cn.com/problems/maximal-square)

> 在一个由 `'0'` 和 `'1'` 组成的二维矩阵内，找到只包含 `'1'` 的最大正方形，并返回其面积。

```c++
class Solution {
public:
    int maximalSquare(vector<vector<char>>& matrix) {
        if (matrix.size() == 0 || matrix[0].size() == 0)
            return 0;
        int maxSide = 0;
        int rows = matrix.size(), columns = matrix[0].size();
        vector<vector<int>> dp(rows, vector<int>(columns));
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < columns; j++) {
                if (matrix[i][j] == '1') {
                    if (i == 0 || j == 0) {
                        dp[i][j] = 1;
                    } else {
                        dp[i][j] = min(min(dp[i - 1][j], dp[i][j - 1]), dp[i - 1][j - 1]) + 1;
                    }
                    maxSide = max(maxSide, dp[i][j]);
                }
            }
        }
        return maxSide*maxSide;
    }
};
```

# [压缩字符串](https://leetcode-cn.com/problems/string-compression/)

> 给定一组字符，使用[原地算法](https://baike.baidu.com/item/原地算法)将其压缩。

```c++
class Solution {
public:
    int compress(vector<char>& chars) {
        int n = chars.size(), index = 0; //下标index表示下个可以放置字符的位置
        int i = 0; //用i遍历整个数组
        while (i < n) {
            if (i + 1 < n && chars[i] == chars[i + 1]) { //遇到前后相同的，说明重复了，就利用指针j找到重复子串的末尾，该重复的子串的长度即为j - i
                int j = i + 1;
                while (j < n && chars[j] == chars[i]) j++;
                string len = to_string (j - i);
                chars[index++] = chars[i]; //先放置字符，再放置它的长度，放置完记得将index下标右移一位
                for (auto& ch : len) chars[index++] = ch;
                i = j; //指针i可以直接跳到重复段的末尾后了
            } else { //前后不相同，则直接放置该字符，同时指针i后移一位
                chars[index++] = chars[i++];
            }
        }
        return index;
    }
};
```

