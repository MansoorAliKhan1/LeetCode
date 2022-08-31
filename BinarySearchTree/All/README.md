# [95. Unique Binary Search Trees II (Medium)](https://leetcode.com/problems/unique-binary-search-trees-ii/)

Given an integer _n_, generate all structurally unique **BST's** (binary search trees) that store values 1 ... _n_.

**Example:**

**Input:** 3  
**Output:**
```
[
  [1,null,3,2],
  [3,2,null,1],
  [3,1,null,null,2],
  [2,1,3],
  [1,null,2,null,3]
]
```
**Explanation:**  
The above output corresponds to the 5 unique BST's shown below:
```

   1         3     3      2      1
    \       /     /      / \      \
     3     2     1      1   3      2
    /     /       \                 \
   2     1         2                 3
```

## Solution 1.

Pick a number `i` for root node.

Numbers `[1, i - 1]` should be put on the left sub-tree, while `[i + 1, N]` on the right.

Both of them are sub-problems. We can simply repeat this process.

```cpp
// OJ: https://leetcode.com/problems/unique-binary-search-trees-ii/
// Author: github.com/lzl124631x
// Time: O(N*C(N)) where C(N) is Catalan Number which equals
//   the count of unique BST. For each tree we visit each node once.
// Space: O(C(N)) because the intermediate `lefts` and `rights` vectors.
//   The actual nodes and the returned vector are not counted as consumptions.
class Solution {
private:
    vector<TreeNode*> generateTrees(int first, int last) {
        if (first > last) return { NULL };
        vector<TreeNode*> v;
        for (int i = first; i <= last; ++i) {
            auto lefts = generateTrees(first, i - 1);
            auto rights = generateTrees(i + 1, last);
            for (auto left : lefts) {
                for (auto right : rights) {
                    v.push_back(new TreeNode(i));
                    v.back()->left = left;
                    v.back()->right = right;
                }
            }
        }
        return v;
    }
public:
    vector<TreeNode*> generateTrees(int n) {
        if (n <= 0) return {};
        return generateTrees(1, n);
    }
};
```
# [96. Unique Binary Search Trees (Medium)](https://leetcode.com/problems/unique-binary-search-trees/)

<p>Given an integer <code>n</code>, return <em>the number of structurally unique <strong>BST'</strong>s (binary search trees) which has exactly </em><code>n</code><em> nodes of unique values from</em> <code>1</code> <em>to</em> <code>n</code>.</p>

<p>&nbsp;</p>
<p><strong>Example 1:</strong></p>
<img alt="" src="https://assets.leetcode.com/uploads/2021/01/18/uniquebstn3.jpg" style="width: 600px; height: 148px;">
<pre><strong>Input:</strong> n = 3
<strong>Output:</strong> 5
</pre>

<p><strong>Example 2:</strong></p>

<pre><strong>Input:</strong> n = 1
<strong>Output:</strong> 1
</pre>

<p>&nbsp;</p>
<p><strong>Constraints:</strong></p>

<ul>
	<li><code>1 &lt;= n &lt;= 19</code></li>
</ul>


**Companies**:  
[Amazon](https://leetcode.com/company/amazon), [Bloomberg](https://leetcode.com/company/bloomberg), [Adobe](https://leetcode.com/company/adobe)

**Related Topics**:  
[Math](https://leetcode.com/tag/math/), [Dynamic Programming](https://leetcode.com/tag/dynamic-programming/), [Tree](https://leetcode.com/tag/tree/), [Binary Search Tree](https://leetcode.com/tag/binary-search-tree/), [Binary Tree](https://leetcode.com/tag/binary-tree/)

**Similar Questions**:
* [Unique Binary Search Trees II (Medium)](https://leetcode.com/problems/unique-binary-search-trees-ii/)

## Solution 1. DP Bottom-up

Let `dp[i]` be the answer.

To get `dp[i]`, we can pick `j` as the root node (`1 <= j <= i`), then there are `j - 1` nodes in the left sub-tree and `i - j` nodes in the right sub-tree, and they have `dp[j - 1]` and `dp[i - j]` unique BSTs respectively. So `dp[i] = sum( dp[j - 1] * dp[i - j] | 1 <= j <= i)`

```cpp
// OJ: https://leetcode.com/problems/unique-binary-search-trees
// Author: github.com/lzl124631x
// Time: O(N^2)
// Space: O(N)
class Solution {
public:
    int numTrees(int n) {
        int dp[20] = {[0 ... 1] = 1};
        for (int i = 2; i <= n; ++i) {
            for (int j = 1; j <= i; ++j) {
                dp[i] += dp[j - 1] * dp[i - j];
            }
        }
        return dp[n];
    }
};
```

## Solution 2. DP Top-down

Let `dp[i]` be the number of unique BST's given `i` nodes.

If there are `j` nodes in the left subtree, then there are `n - j - 1` nodes in the right subtree. Both subtrees are BST's as well. So `dp[i] = sum( dp[j] * dp[n - j - 1] | 0 <= j < n )`.

```cpp
// OJ: https://leetcode.com/problems/unique-binary-search-trees/
// Author: github.com/lzl124631x
// Time: O(N^2)
// Space: O(N)
class Solution {
public:
    int numTrees(int n) {
        int dp[20] = {[0 ... 1] = 1};
        function<int(int)> dfs = [&](int n) {
            if (dp[n]) return dp[n];
            for (int i = 1; i <= n; ++i) dp[n] += dfs(i - 1) * dfs(n - i);
            return dp[n];
        };
        return dfs(n);
    }
};
```

## Solution 2. Catalan Number

$$
C_n = \dfrac{1}{n+1}{2n \choose n} = \dfrac{(2n)!}{(n+1)!n!}=\prod_{k=2}^{n}\dfrac{n+k}{k}\quad \text{for } n \ge 0
$$

```cpp
// OJ: https://leetcode.com/problems/unique-binary-search-trees
// Author: github.com/lzl124631x
// Time: O(N)
// Space: O(1)
// Ref: https://discuss.leetcode.com/topic/13321/a-very-simple-and-straight-ans-based-on-math-catalan-number-o-n-times-o-1-space
class Solution {
public:
    int numTrees(int n) {
        long long ans = 1, i;
        for (i = 1; i <= n; ++i) ans = ans * (i + n) / i;
        return ans / i;
    }
};
```
# [98. Validate Binary Search Tree (Medium)](https://leetcode.com/problems/validate-binary-search-tree/)

<p>Given the <code>root</code> of a binary tree, <em>determine if it is a valid binary search tree (BST)</em>.</p>

<p>A <strong>valid BST</strong> is defined as follows:</p>

<ul>
	<li>The left subtree of a node contains only nodes with keys <strong>less than</strong> the node's key.</li>
	<li>The right subtree of a node contains only nodes with keys <strong>greater than</strong> the node's key.</li>
	<li>Both the left and right subtrees must also be binary search trees.</li>
</ul>

<p>&nbsp;</p>
<p><strong>Example 1:</strong></p>
<img alt="" src="https://assets.leetcode.com/uploads/2020/12/01/tree1.jpg" style="width: 302px; height: 182px;">
<pre><strong>Input:</strong> root = [2,1,3]
<strong>Output:</strong> true
</pre>

<p><strong>Example 2:</strong></p>
<img alt="" src="https://assets.leetcode.com/uploads/2020/12/01/tree2.jpg" style="width: 422px; height: 292px;">
<pre><strong>Input:</strong> root = [5,1,4,null,null,3,6]
<strong>Output:</strong> false
<strong>Explanation:</strong> The root node's value is 5 but its right child's value is 4.
</pre>

<p>&nbsp;</p>
<p><strong>Constraints:</strong></p>

<ul>
	<li>The number of nodes in the tree is in the range <code>[1, 10<sup>4</sup>]</code>.</li>
	<li><code>-2<sup>31</sup> &lt;= Node.val &lt;= 2<sup>31</sup> - 1</code></li>
</ul>


**Companies**:  
[Amazon](https://leetcode.com/company/amazon), [Facebook](https://leetcode.com/company/facebook), [Bloomberg](https://leetcode.com/company/bloomberg), [Microsoft](https://leetcode.com/company/microsoft), [Zillow](https://leetcode.com/company/zillow), [Apple](https://leetcode.com/company/apple), [Google](https://leetcode.com/company/google), [Uber](https://leetcode.com/company/uber), [ByteDance](https://leetcode.com/company/bytedance)

**Related Topics**:  
[Tree](https://leetcode.com/tag/tree/), [Depth-First Search](https://leetcode.com/tag/depth-first-search/), [Binary Search Tree](https://leetcode.com/tag/binary-search-tree/), [Binary Tree](https://leetcode.com/tag/binary-tree/)

**Similar Questions**:
* [Binary Tree Inorder Traversal (Easy)](https://leetcode.com/problems/binary-tree-inorder-traversal/)
* [Find Mode in Binary Search Tree (Easy)](https://leetcode.com/problems/find-mode-in-binary-search-tree/)

## Solution 1. Pre-order Traversal

```cpp
// OJ: https://leetcode.com/problems/validate-binary-search-tree
// Author: github.com/lzl124631x
// Time: O(N)
// Space: O(H)
class Solution {
public:
    bool isValidBST(TreeNode* root, TreeNode *left = NULL, TreeNode *right = NULL) {
        if (!root) return true;
        if ((left && root->val <= left->val) || (right && root->val >= right->val)) return false;
        return isValidBST(root->left, left, root) && isValidBST(root->right, root, right);
    }
};
```

Or

```cpp
// OJ: https://leetcode.com/problems/validate-binary-search-tree/
// Author: github.com/lzl124631x
// Time: O(N)
// Space: O(H)
class Solution {
public:
    bool isValidBST(TreeNode* root, long left = LONG_MIN, long right = LONG_MAX) {
        if (!root) return true;
        if (root->val <= left || root->val >= right) return false;
        return isValidBST(root->left, left, root->val) && isValidBST(root->right, root->val, right);
    }
};
```

## Solution 2. In-order traversal

```cpp
// OJ: https://leetcode.com/problems/validate-binary-search-tree
// Author: github.com/lzl124631x
// Time: O(N)
// Space: O(logN)
class Solution {
    TreeNode *prev = NULL;
public:
    bool isValidBST(TreeNode* root) {
        if (!root) return true;
        if (!isValidBST(root->left) || (prev && prev->val >= root->val)) return false; 
        prev = root;
        return isValidBST(root->right);
    }
};
```
# [99. Recover Binary Search Tree (Hard)](https://leetcode.com/problems/recover-binary-search-tree/)

<p>Two elements of a binary search tree (BST) are swapped by mistake.</p>

<p>Recover the tree without changing its structure.</p>

<p><strong>Example 1:</strong></p>

<pre><strong>Input:</strong> [1,3,null,null,2]

&nbsp;  1
&nbsp; /
&nbsp;3
&nbsp; \
&nbsp;  2

<strong>Output:</strong> [3,1,null,null,2]

&nbsp;  3
&nbsp; /
&nbsp;1
&nbsp; \
&nbsp;  2
</pre>

<p><strong>Example 2:</strong></p>

<pre><strong>Input:</strong> [3,1,4,null,null,2]

  3
 / \
1   4
&nbsp;  /
&nbsp; 2

<strong>Output:</strong> [2,1,4,null,null,3]

  2
 / \
1   4
&nbsp;  /
 &nbsp;3
</pre>

<p><strong>Follow up:</strong></p>

<ul>
	<li>A solution using O(<em>n</em>) space is pretty straight forward.</li>
	<li>Could you devise a constant space solution?</li>
</ul>


**Related Topics**:  
[Tree](https://leetcode.com/tag/tree/), [Depth-first Search](https://leetcode.com/tag/depth-first-search/)

## Solution 1. DFS

DFS to find the minimal incorrect node `a` and the maximum incorrect node `b`, and swap them in the end.

In DFS, we use `left` and `right` to point to the left and right bound nodes respectively.

* If `root->val` is smaller than `left->val`, `root, left` is an incorrect pair.
* If `root->val` is greater than `right->val`, `right, root` is an incorrect pair.

```cpp
// OJ: https://leetcode.com/problems/recover-binary-search-tree/
// Author: github.com/lzl124631x
// Time: O(N)
// Space: O(H)
class Solution {
    TreeNode *a = NULL, *b = NULL;
    void update(TreeNode *x, TreeNode *y) {
        if (!a || x->val < a->val) a = x;
        if (!b || y->val > b->val) b = y;
    }
    void dfs(TreeNode *root, TreeNode *left = NULL, TreeNode *right = NULL) {
        if (!root) return;
        if (left && left->val > root->val) update(root, left);
        if (right && right->val < root->val) update(right, root);
        dfs(root->left, left, root);
        dfs(root->right, root, right);
    }
public:
    void recoverTree(TreeNode* root) {
        dfs(root);
        swap(a->val, b->val);
    }
};
```
# [108. Convert Sorted Array to Binary Search Tree (Easy)](https://leetcode.com/problems/convert-sorted-array-to-binary-search-tree/)

<p>Given an integer array <code>nums</code> where the elements are sorted in <strong>ascending order</strong>, convert <em>it to a <strong>height-balanced</strong> binary search tree</em>.</p>

<p>A <strong>height-balanced</strong> binary tree is a binary tree in which the depth of the two subtrees of every node never differs by more than one.</p>

<p>&nbsp;</p>
<p><strong>Example 1:</strong></p>
<img alt="" src="https://assets.leetcode.com/uploads/2021/02/18/btree1.jpg" style="width: 302px; height: 222px;">
<pre><strong>Input:</strong> nums = [-10,-3,0,5,9]
<strong>Output:</strong> [0,-3,9,-10,null,5]
<strong>Explanation:</strong> [0,-10,5,null,-3,null,9] is also accepted:
<img alt="" src="https://assets.leetcode.com/uploads/2021/02/18/btree2.jpg" style="width: 302px; height: 222px;">
</pre>

<p><strong>Example 2:</strong></p>
<img alt="" src="https://assets.leetcode.com/uploads/2021/02/18/btree.jpg" style="width: 342px; height: 142px;">
<pre><strong>Input:</strong> nums = [1,3]
<strong>Output:</strong> [3,1]
<strong>Explanation:</strong> [1,3] and [3,1] are both a height-balanced BSTs.
</pre>

<p>&nbsp;</p>
<p><strong>Constraints:</strong></p>

<ul>
	<li><code>1 &lt;= nums.length &lt;= 10<sup>4</sup></code></li>
	<li><code>-10<sup>4</sup> &lt;= nums[i] &lt;= 10<sup>4</sup></code></li>
	<li><code>nums</code> is sorted in a <strong>strictly increasing</strong> order.</li>
</ul>


**Companies**:  
[Amazon](https://leetcode.com/company/amazon), [Facebook](https://leetcode.com/company/facebook), [Google](https://leetcode.com/company/google), [Apple](https://leetcode.com/company/apple)

**Related Topics**:  
[Array](https://leetcode.com/tag/array/), [Divide and Conquer](https://leetcode.com/tag/divide-and-conquer/), [Tree](https://leetcode.com/tag/tree/), [Binary Search Tree](https://leetcode.com/tag/binary-search-tree/), [Binary Tree](https://leetcode.com/tag/binary-tree/)

**Similar Questions**:
* [Convert Sorted List to Binary Search Tree (Medium)](https://leetcode.com/problems/convert-sorted-list-to-binary-search-tree/)

## Solution 1.

```cpp
// OJ: https://leetcode.com/problems/convert-sorted-array-to-binary-search-tree/
// Author: github.com/lzl124631x
// Time: O(N)
// Space: O(logN)
class Solution {
    TreeNode *dfs(vector<int> &A, int begin, int end) {
        if (begin + 1 > end) return NULL;
        int mid = (begin + end) / 2;
        auto root = new TreeNode(A[mid]);
        root->left = dfs(A, begin, mid);
        root->right = dfs(A, mid + 1, end);
        return root;
    }
public:
    TreeNode* sortedArrayToBST(vector<int>& A) {
        return dfs(A, 0, A.size());
    }
};
```
# [109. Convert Sorted List to Binary Search Tree (Medium)](https://leetcode.com/problems/convert-sorted-list-to-binary-search-tree/)

<p>Given the <code>head</code> of a singly linked list where elements are <strong>sorted in ascending order</strong>, convert it to a height balanced BST.</p>

<p>For this problem, a height-balanced binary tree is defined as a binary tree in which the depth of the two subtrees of <em>every</em> node never differ by more than 1.</p>

<p>&nbsp;</p>
<p><strong>Example 1:</strong></p>
<img alt="" src="https://assets.leetcode.com/uploads/2020/08/17/linked.jpg" style="width: 600px; height: 466px;">
<pre><strong>Input:</strong> head = [-10,-3,0,5,9]
<strong>Output:</strong> [0,-3,9,-10,null,5]
<strong>Explanation:</strong> One possible answer is [0,-3,9,-10,null,5], which represents the shown height balanced BST.
</pre>

<p><strong>Example 2:</strong></p>

<pre><strong>Input:</strong> head = []
<strong>Output:</strong> []
</pre>

<p><strong>Example 3:</strong></p>

<pre><strong>Input:</strong> head = [0]
<strong>Output:</strong> [0]
</pre>

<p><strong>Example 4:</strong></p>

<pre><strong>Input:</strong> head = [1,3]
<strong>Output:</strong> [3,1]
</pre>

<p>&nbsp;</p>
<p><strong>Constraints:</strong></p>

<ul>
	<li>The number of nodes in <code>head</code> is in the range <code>[0, 2 * 10<sup>4</sup>]</code>.</li>
	<li><code>-10^5 &lt;= Node.val &lt;= 10^5</code></li>
</ul>


**Companies**:  
[Facebook](https://leetcode.com/company/facebook), [Microsoft](https://leetcode.com/company/microsoft), [Oracle](https://leetcode.com/company/oracle)

**Related Topics**:  
[Linked List](https://leetcode.com/tag/linked-list/), [Depth-first Search](https://leetcode.com/tag/depth-first-search/)

**Similar Questions**:
* [Convert Sorted Array to Binary Search Tree (Easy)](https://leetcode.com/problems/convert-sorted-array-to-binary-search-tree/)

## Solution 1.

```cpp
// OJ: https://leetcode.com/problems/convert-sorted-list-to-binary-search-tree/
// Author: github.com/lzl124631x
// Time: O(NlogN)
// Space: O(logN)
class Solution {
    int getLength(ListNode *head) {
        int ans = 0;
        for (; head; head = head->next, ++ans);
        return ans;
    }
    TreeNode *dfs(ListNode *head, int len) {
        if (len == 0) return NULL;
        if (len == 1) return new TreeNode(head->val);
        auto p = head;
        for (int i = 0; i < len / 2; ++i) p = p->next;
        auto root = new TreeNode(p->val);
        root->left = dfs(head, len / 2);
        root->right = dfs(p->next, (len - 1) / 2);
        return root;
    }
public:
    TreeNode* sortedListToBST(ListNode* head) {
        int len = getLength(head);
        return dfs(head, len);
    }
};
```

## Solution 2. Fast Slow Pointers

```cpp
// OJ: https://leetcode.com/problems/convert-sorted-list-to-binary-search-tree/
// Author: github.com/lzl124631x
// Time: O(NlogN)
// Space: O(logN)
class Solution {
    TreeNode *dfs(ListNode *head, ListNode *end) {
        if (head == end) return NULL;
        if (head->next == end) return new TreeNode(head->val);
        auto p = head, q = head;
        while (q != end && q->next != end) {
            p = p->next;
            q = q->next->next;
        }
        auto root = new TreeNode(p->val);
        root->left = dfs(head, p);
        root->right = dfs(p->next, end);
        return root;
    }
public:
    TreeNode* sortedListToBST(ListNode* head) {
        return dfs(head, NULL);
    }
};
```

## Solution 3.

```cpp
// OJ: https://leetcode.com/problems/convert-sorted-list-to-binary-search-tree/
// Author: github.com/lzl124631x
// Time: O(N)
// Space: O(logN)
class Solution {
    ListNode *head;
    int getLength(ListNode *head) {
        int ans = 0;
        for (; head; head = head->next, ++ans);
        return ans;
    }
    TreeNode *dfs(int begin, int end) {
        if (begin == end) return NULL;
        int mid = (begin + end) / 2;
        auto left = dfs(begin, mid);
        auto root = new TreeNode(head->val);
        head = head->next;
        root->left = left;
        root->right = dfs(mid + 1, end);
        return root;
    }
public:
    TreeNode* sortedListToBST(ListNode* head) {
        this->head = head;
        return dfs(0, getLength(head));
}
};
```
# [173. Binary Search Tree Iterator (Medium)](https://leetcode.com/problems/binary-search-tree-iterator/)

<p>Implement the <code>BSTIterator</code> class that represents an iterator over the <strong><a href="https://en.wikipedia.org/wiki/Tree_traversal#In-order_(LNR)" target="_blank">in-order traversal</a></strong> of a binary search tree (BST):</p>

<ul>
	<li><code>BSTIterator(TreeNode root)</code> Initializes an object of the <code>BSTIterator</code> class. The <code>root</code> of the BST is given as part of the constructor. The pointer should be initialized to a non-existent number smaller than any element in the BST.</li>
	<li><code>boolean hasNext()</code> Returns <code>true</code> if there exists a number in the traversal to the right of the pointer, otherwise returns <code>false</code>.</li>
	<li><code>int next()</code> Moves the pointer to the right, then returns the number at the pointer.</li>
</ul>

<p>Notice that by initializing the pointer to a non-existent smallest number, the first call to <code>next()</code> will return the smallest element in the BST.</p>

<p>You may assume that <code>next()</code> calls will always be valid. That is, there will be at least a next number in the in-order traversal when <code>next()</code> is called.</p>

<p>&nbsp;</p>
<p><strong>Example 1:</strong></p>
<img alt="" src="https://assets.leetcode.com/uploads/2018/12/25/bst-tree.png" style="width: 189px; height: 178px;">
<pre><strong>Input</strong>
["BSTIterator", "next", "next", "hasNext", "next", "hasNext", "next", "hasNext", "next", "hasNext"]
[[[7, 3, 15, null, null, 9, 20]], [], [], [], [], [], [], [], [], []]
<strong>Output</strong>
[null, 3, 7, true, 9, true, 15, true, 20, false]

<strong>Explanation</strong>
BSTIterator bSTIterator = new BSTIterator([7, 3, 15, null, null, 9, 20]);
bSTIterator.next();    // return 3
bSTIterator.next();    // return 7
bSTIterator.hasNext(); // return True
bSTIterator.next();    // return 9
bSTIterator.hasNext(); // return True
bSTIterator.next();    // return 15
bSTIterator.hasNext(); // return True
bSTIterator.next();    // return 20
bSTIterator.hasNext(); // return False
</pre>

<p>&nbsp;</p>
<p><strong>Constraints:</strong></p>

<ul>
	<li>The number of nodes in the tree is in the range <code>[1, 10<sup>5</sup>]</code>.</li>
	<li><code>0 &lt;= Node.val &lt;= 10<sup>6</sup></code></li>
	<li>At most <code>10<sup>5</sup></code> calls will be made to <code>hasNext</code>, and <code>next</code>.</li>
</ul>

<p>&nbsp;</p>
<p><strong>Follow up:</strong></p>

<ul>
	<li>Could you implement <code>next()</code> and <code>hasNext()</code> to run in average <code>O(1)</code> time and use&nbsp;<code>O(h)</code> memory, where <code>h</code> is the height of the tree?</li>
</ul>


**Companies**:
[Facebook](https://leetcode.com/company/facebook), [Microsoft](https://leetcode.com/company/microsoft), [Bloomberg](https://leetcode.com/company/bloomberg), [Google](https://leetcode.com/company/google), [Amazon](https://leetcode.com/company/amazon), [Salesforce](https://leetcode.com/company/salesforce)

**Related Topics**:
[Stack](https://leetcode.com/tag/stack/), [Tree](https://leetcode.com/tag/tree/), [Design](https://leetcode.com/tag/design/), [Binary Search Tree](https://leetcode.com/tag/binary-search-tree/), [Binary Tree](https://leetcode.com/tag/binary-tree/), [Iterator](https://leetcode.com/tag/iterator/)

**Similar Questions**:
* [Binary Tree Inorder Traversal (Easy)](https://leetcode.com/problems/binary-tree-inorder-traversal/)
* [Flatten 2D Vector (Medium)](https://leetcode.com/problems/flatten-2d-vector/)
* [Zigzag Iterator (Medium)](https://leetcode.com/problems/zigzag-iterator/)
* [Peeking Iterator (Medium)](https://leetcode.com/problems/peeking-iterator/)
* [Inorder Successor in BST (Medium)](https://leetcode.com/problems/inorder-successor-in-bst/)
* [Binary Search Tree Iterator II (Medium)](https://leetcode.com/problems/binary-search-tree-iterator-ii/)

## Solution 1. Stack

```cpp
// OJ: https://leetcode.com/problems/binary-search-tree-iterator/
// Author: github.com/lzl124631x
// Time: O(1) amortized
// Space: O(H)
class BSTIterator {
private:
    stack<TreeNode*> s;
    void pushNodes(TreeNode *node) {
        while (node) {
            s.push(node);
            node = node->left;
        }
    }
public:
    BSTIterator(TreeNode* root) {
        pushNodes(root);
    }
    int next() {
        auto node = s.top();
        s.pop();
        pushNodes(node->right);
        return node->val;
    }
    bool hasNext() {
        return s.size();
    }
};
```
# [230. Kth Smallest Element in a BST (Medium)](https://leetcode.com/problems/kth-smallest-element-in-a-bst/)

<p>Given the <code>root</code> of a binary search tree, and an integer <code>k</code>, return <em>the</em> <code>k<sup>th</sup></code> <em>smallest value (<strong>1-indexed</strong>) of all the values of the nodes in the tree</em>.</p>

<p>&nbsp;</p>
<p><strong>Example 1:</strong></p>
<img alt="" src="https://assets.leetcode.com/uploads/2021/01/28/kthtree1.jpg" style="width: 212px; height: 301px;">
<pre><strong>Input:</strong> root = [3,1,4,null,2], k = 1
<strong>Output:</strong> 1
</pre>

<p><strong>Example 2:</strong></p>
<img alt="" src="https://assets.leetcode.com/uploads/2021/01/28/kthtree2.jpg" style="width: 382px; height: 302px;">
<pre><strong>Input:</strong> root = [5,3,6,2,4,null,null,1], k = 3
<strong>Output:</strong> 3
</pre>

<p>&nbsp;</p>
<p><strong>Constraints:</strong></p>

<ul>
	<li>The number of nodes in the tree is <code>n</code>.</li>
	<li><code>1 &lt;= k &lt;= n &lt;= 10<sup>4</sup></code></li>
	<li><code>0 &lt;= Node.val &lt;= 10<sup>4</sup></code></li>
</ul>

<p>&nbsp;</p>
<p><strong>Follow up:</strong> If the BST is modified often (i.e., we can do insert and delete operations) and you need to find the kth smallest frequently, how would you optimize?</p>


**Companies**:  
[Amazon](https://leetcode.com/company/amazon), [Uber](https://leetcode.com/company/uber), [Facebook](https://leetcode.com/company/facebook)

**Related Topics**:  
[Tree](https://leetcode.com/tag/tree/), [Depth-First Search](https://leetcode.com/tag/depth-first-search/), [Binary Search Tree](https://leetcode.com/tag/binary-search-tree/), [Binary Tree](https://leetcode.com/tag/binary-tree/)

**Similar Questions**:
* [Binary Tree Inorder Traversal (Easy)](https://leetcode.com/problems/binary-tree-inorder-traversal/)
* [Second Minimum Node In a Binary Tree (Easy)](https://leetcode.com/problems/second-minimum-node-in-a-binary-tree/)

## Solution 1. In-order traversal (Recursive)

```cpp
// OJ: https://leetcode.com/problems/kth-smallest-element-in-a-bst/
// Author: github.com/lzl124631x
// Time: O(N)
// Space: O(H)
class Solution {
public:
    int kthSmallest(TreeNode* root, int k) {
        function<int(TreeNode*)> inorder = [&](TreeNode *root) {
            if (!root) return -1;
            int val = inorder(root->left);
            if (val != -1) return val;
            if (--k == 0) return root->val;
            return inorder(root->right);
        };
        return inorder(root);
    }
};
```

## Solution 1. In-order traversal (Iterative)

```cpp
// OJ: https://leetcode.com/problems/kth-smallest-element-in-a-bst/
// Author: github.com/lzl124631x
// Time: O(N)
// Space: O(H)
class Solution {
public:
    int kthSmallest(TreeNode* root, int k) {
        stack<TreeNode*> s;
        while (root || s.size()) {
            while (root) {
                s.push(root);
                root = root->left;
            }
            root = s.top();
            s.pop();
            if (--k == 0) return root->val;
            root = root->right;
        }
        return -1;
    }
};
```
# [235. Lowest Common Ancestor of a Binary Search Tree (Easy)](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-search-tree/)

<p>Given a binary search tree (BST), find the lowest common ancestor (LCA) of two given nodes in the BST.</p>

<p>According to the <a href="https://en.wikipedia.org/wiki/Lowest_common_ancestor" target="_blank">definition of LCA on Wikipedia</a>: “The lowest common ancestor is defined between two nodes <code>p</code> and <code>q</code> as the lowest node in <code>T</code> that has both <code>p</code> and <code>q</code> as descendants (where we allow <b>a node to be a descendant of itself</b>).”</p>

<p>&nbsp;</p>
<p><strong>Example 1:</strong></p>
<img alt="" src="https://assets.leetcode.com/uploads/2018/12/14/binarysearchtree_improved.png" style="width: 200px; height: 190px;">
<pre><strong>Input:</strong> root = [6,2,8,0,4,7,9,null,null,3,5], p = 2, q = 8
<strong>Output:</strong> 6
<strong>Explanation:</strong> The LCA of nodes 2 and 8 is 6.
</pre>

<p><strong>Example 2:</strong></p>
<img alt="" src="https://assets.leetcode.com/uploads/2018/12/14/binarysearchtree_improved.png" style="width: 200px; height: 190px;">
<pre><strong>Input:</strong> root = [6,2,8,0,4,7,9,null,null,3,5], p = 2, q = 4
<strong>Output:</strong> 2
<strong>Explanation:</strong> The LCA of nodes 2 and 4 is 2, since a node can be a descendant of itself according to the LCA definition.
</pre>

<p><strong>Example 3:</strong></p>

<pre><strong>Input:</strong> root = [2,1], p = 2, q = 1
<strong>Output:</strong> 2
</pre>

<p>&nbsp;</p>
<p><strong>Constraints:</strong></p>

<ul>
	<li>The number of nodes in the tree is in the range <code>[2, 10<sup>5</sup>]</code>.</li>
	<li><code>-10<sup>9</sup> &lt;= Node.val &lt;= 10<sup>9</sup></code></li>
	<li>All <code>Node.val</code> are <strong>unique</strong>.</li>
	<li><code>p != q</code></li>
	<li><code>p</code> and <code>q</code> will exist in the BST.</li>
</ul>


**Companies**:  
[LinkedIn](https://leetcode.com/company/linkedin), [Facebook](https://leetcode.com/company/facebook), [Microsoft](https://leetcode.com/company/microsoft), [Google](https://leetcode.com/company/google), [Uber](https://leetcode.com/company/uber)

**Related Topics**:  
[Tree](https://leetcode.com/tag/tree/), [Depth-First Search](https://leetcode.com/tag/depth-first-search/), [Binary Search Tree](https://leetcode.com/tag/binary-search-tree/), [Binary Tree](https://leetcode.com/tag/binary-tree/)

**Similar Questions**:
* [Lowest Common Ancestor of a Binary Tree (Medium)](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/)
* [Smallest Common Region (Medium)](https://leetcode.com/problems/smallest-common-region/)
* [Lowest Common Ancestor of a Binary Tree II (Medium)](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree-ii/)
* [Lowest Common Ancestor of a Binary Tree III (Medium)](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree-iii/)
* [Lowest Common Ancestor of a Binary Tree IV (Medium)](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree-iv/)

## Solution 1. DFS

```cpp
// OJ: https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-search-tree/
// Author: github.com/lzl124631x
// Time: O(H)
// Space: O(H)
class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        if (p->val > q->val) swap(p, q);
        function<TreeNode*(TreeNode*)> dfs = [&](TreeNode *node) -> TreeNode* {
            if (!node) return nullptr;
            if (node->val >= p->val && node->val <= q->val) return node;
            if (node->val < p->val) return dfs(node->right);
            return dfs(node->left);
        };
        return dfs(root);
    }
};
```

## Solution 2. DFS

```cpp
// OJ: https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-search-tree/
// Author: github.com/lzl124631x
// Time: O(H)
// Space: O(H)
class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        if (root->val > p->val && root->val > q->val) return lowestCommonAncestor(root->left, p, q);
        if (root->val < p->val && root->val < q->val) return lowestCommonAncestor(root->right, p, q);
        return root;
    }
};
```
# [255. Verify Preorder Sequence in Binary Search Tree (Medium)](https://leetcode.com/problems/verify-preorder-sequence-in-binary-search-tree/)

<p>Given an array of <strong>unique</strong> integers <code>preorder</code>, return <code>true</code> <em>if it is the correct preorder traversal sequence of a binary search tree</em>.</p>

<p>&nbsp;</p>
<p><strong>Example 1:</strong></p>
<img alt="" src="https://assets.leetcode.com/uploads/2021/03/12/preorder-tree.jpg" style="width: 292px; height: 302px;">
<pre><strong>Input:</strong> preorder = [5,2,1,3,6]
<strong>Output:</strong> true
</pre>

<p><strong>Example 2:</strong></p>

<pre><strong>Input:</strong> preorder = [5,2,6,1,3]
<strong>Output:</strong> false
</pre>

<p>&nbsp;</p>
<p><strong>Constraints:</strong></p>

<ul>
	<li><code>1 &lt;= preorder.length &lt;= 10<sup>4</sup></code></li>
	<li><code>1 &lt;= preorder[i] &lt;= 10<sup>4</sup></code></li>
	<li>All the elements of <code>preorder</code> are <strong>unique</strong>.</li>
</ul>

<p>&nbsp;</p>
<p><strong>Follow up:</strong> Could you do it using only constant space complexity?</p>


**Companies**:  
[VMware](https://leetcode.com/company/vmware)

**Related Topics**:  
[Stack](https://leetcode.com/tag/stack/), [Tree](https://leetcode.com/tag/tree/), [Binary Search Tree](https://leetcode.com/tag/binary-search-tree/), [Recursion](https://leetcode.com/tag/recursion/), [Monotonic Stack](https://leetcode.com/tag/monotonic-stack/), [Binary Tree](https://leetcode.com/tag/binary-tree/)

**Similar Questions**:
* [Binary Tree Preorder Traversal (Easy)](https://leetcode.com/problems/binary-tree-preorder-traversal/)

## Solution 1. Monotonic Stack

```cpp
// OJ: https://leetcode.com/problems/verify-preorder-sequence-in-binary-search-tree/
// Author: github.com/lzl124631x
// Time: O(N)
// Space: O(N)
class Solution {
public:
    bool verifyPreorder(vector<int>& A) {
        stack<int> s;
        int mn = INT_MIN;
        for (int n : A) {
            if (n < mn) return false;
            while (s.size() && s.top() < n) {
                mn = s.top();
                s.pop();
            }
            s.push(n);
        }
        return true;
    }
};
```

Or if we are allowed to change the input array to simulate the stack

```cpp
// OJ: https://leetcode.com/problems/verify-preorder-sequence-in-binary-search-tree/
// Author: github.com/lzl124631x
// Time: O(N)
// Space: O(1) if we don't count the space used by the input array.
class Solution {
public:
    bool verifyPreorder(vector<int>& A) {
        int mn = INT_MIN, j = -1;
        for (int i = 0; i < A.size(); ++i) {
            if (A[i] < mn) return false;
            while (j >= 0 && A[j] < A[i]) mn = A[j--];
            A[++j] = A[i];
        }
        return true;
    }
};
```
# [270. Closest Binary Search Tree Value (Easy)](https://leetcode.com/problems/closest-binary-search-tree-value/)

<p>Given a non-empty binary search tree and a target value, find the value in the BST that is closest to the target.</p>

<p><b>Note:</b></p>

<ul>
	<li>Given target value is a floating point.</li>
	<li>You are guaranteed to have only one unique value in the BST that is closest to the target.</li>
</ul>

<p><strong>Example:</strong></p>

<pre><strong>Input:</strong> root = [4,2,5,1,3], target = 3.714286

    4
   / \
  2   5
 / \
1   3

<strong>Output:</strong> 4
</pre>


**Companies**:  
[Facebook](https://leetcode.com/company/facebook)

**Related Topics**:  
[Binary Search](https://leetcode.com/tag/binary-search/), [Tree](https://leetcode.com/tag/tree/)

**Similar Questions**:
* [Count Complete Tree Nodes (Medium)](https://leetcode.com/problems/count-complete-tree-nodes/)
* [Closest Binary Search Tree Value II (Hard)](https://leetcode.com/problems/closest-binary-search-tree-value-ii/)
* [Search in a Binary Search Tree (Easy)](https://leetcode.com/problems/search-in-a-binary-search-tree/)

## Solution 1.

```cpp
// OJ: https://leetcode.com/problems/closest-binary-search-tree-value/
// Author: github.com/lzl124631x
// Time: O(N)
// Space: O(logN)
class Solution {
public:
    int closestValue(TreeNode* root, double target) {
        double dist = abs(target - root->val);
        if (dist < .5) return root->val;
        if (root->val < target) {
            if (!root->right) return root->val;
            int right = closestValue(root->right, target);
            return dist < abs(target - right) ? root->val : right;
        }
        if (!root->left) return root->val;
        int left = closestValue(root->left, target);
        return dist < abs(target - left) ? root->val : left;
    }
};
```

## Solution 2.

```cpp
// OJ: https://leetcode.com/problems/closest-binary-search-tree-value/
// Author: github.com/lzl124631x
// Time: O(N)
// Space: O(logN)
// Ref: https://leetcode.com/problems/closest-binary-search-tree-value/discuss/70331/Clean-and-concise-java-solution
class Solution {
public:
    int closestValue(TreeNode* root, double target) {
        int ans = root->val;
        while (root) {
            if (abs(target - root->val) < abs(target - ans)) ans = root->val;
            root = root->val > target ? root->left : root->right;
        }
        return ans;
    }
};
```
# [285. Inorder Successor in BST (Medium)](https://leetcode.com/problems/inorder-successor-in-bst/)

<p>Given a binary search tree and a node in it, find the in-order successor of that node in the BST.</p>

<p><b>Note</b>: If the given node has no in-order successor in the tree, return <code>null</code>.</p>

<p><b>Example 1:</b></p>

<pre><b>Input:</b> root = <code>[2,1,3], p = 1

  2
 / \
1   3
</code>
<b>Output:</b> 2</pre>

<p><b>Example 2:</b></p>

<pre><b>Input:</b> root = <code>[5,3,6,2,4,null,null,1], p = 6

      5
     / \
    3   6
   / \
  2   4
 /   
1
</code>
<b>Output:</b> null</pre>


**Companies**:  
[Amazon](https://leetcode.com/company/amazon), [Google](https://leetcode.com/company/google), [Microsoft](https://leetcode.com/company/microsoft), [Palantir Technologies](https://leetcode.com/company/palantir-technologies), [Facebook](https://leetcode.com/company/facebook)

**Related Topics**:  
[Tree](https://leetcode.com/tag/tree/)

**Similar Questions**:
* [Binary Tree Inorder Traversal (Medium)](https://leetcode.com/problems/binary-tree-inorder-traversal/)
* [Binary Search Tree Iterator (Medium)](https://leetcode.com/problems/binary-search-tree-iterator/)

## Solution 1. Recursive

```cpp
// OJ: https://leetcode.com/problems/inorder-successor-in-bst/
// Author: github.com/lzl124631x
// Time: O(N)
// Space: O(logN)
class Solution {
private:
    TreeNode *target, *ans = NULL;
    bool seen = false;
    void inorder(TreeNode *root) {
        if (!root) return;
        inorder(root->left);
        if (seen && !ans) ans = root;
        if (seen && ans) return;
        if (root == target) seen = true;
        inorder(root->right);
    }
public:
    TreeNode* inorderSuccessor(TreeNode* root, TreeNode* p) {
        target = p;
        inorder(root);
        return ans;
    }
};
```

## Solution 2.

Solution 1 doesn't take advantage of BST. Try to find the smallest number greater than `p->val`.

```cpp
// OJ: https://leetcode.com/problems/inorder-successor-in-bst/
// Author: github.com/lzl124631x
// Time: O(logN)
// Space: O(1)
// Ref: https://leetcode.com/problems/inorder-successor-in-bst/discuss/72721/10-(and-4)-lines-O(h)-JavaC%2B%2B
class Solution {
public:
    TreeNode* inorderSuccessor(TreeNode* root, TreeNode* p) {
        TreeNode *best = NULL;
        while (root) {
            root = root->val > p->val ? (best = root)->left : root->right;
        }
        return best;
    }
};
```
# [333. Largest BST Subtree (Medium)](https://leetcode.com/problems/largest-bst-subtree/)

<p>Given the root of a binary tree, find the largest subtree, which is also a Binary Search Tree (BST), where the largest means subtree has the largest number of nodes.</p>

<p>A <strong>Binary Search Tree (BST)</strong> is a tree in which all the nodes follow the below-mentioned properties:</p>

<ul>
	<li>The left subtree values are less than the value of their parent (root) node's value.</li>
	<li>The right subtree values are greater than the value of their parent (root) node's value.</li>
</ul>

<p><strong>Note:</strong> A subtree must include all of its descendants.</p>

<p>&nbsp;</p>
<p><strong>Example 1:</strong></p>

<p><strong><img alt="" src="https://assets.leetcode.com/uploads/2020/10/17/tmp.jpg" style="width: 571px; height: 302px;"></strong></p>

<pre><strong>Input:</strong> root = [10,5,15,1,8,null,7]
<strong>Output:</strong> 3
<strong>Explanation: </strong>The Largest BST Subtree in this case is the highlighted one. The return value is the subtree's size, which is 3.</pre>

<p><strong>Example 2:</strong></p>

<pre><strong>Input:</strong> root = [4,2,7,2,3,5,null,2,null,null,null,null,null,1]
<strong>Output:</strong> 2
</pre>

<p>&nbsp;</p>
<p><strong>Constraints:</strong></p>

<ul>
	<li>The number of nodes in the tree is in the range <code>[0, 10<sup>4</sup>]</code>.</li>
	<li><code>-10<sup>4</sup> &lt;= Node.val &lt;= 10<sup>4</sup></code></li>
</ul>

<p>&nbsp;</p>
<p><strong>Follow up:</strong> Can you figure out ways to solve it with <code>O(n)</code> time complexity?</p>


**Companies**:  
[Amazon](https://leetcode.com/company/amazon), [ByteDance](https://leetcode.com/company/bytedance)

**Related Topics**:  
[Dynamic Programming](https://leetcode.com/tag/dynamic-programming/), [Tree](https://leetcode.com/tag/tree/), [Depth-First Search](https://leetcode.com/tag/depth-first-search/), [Binary Search Tree](https://leetcode.com/tag/binary-search-tree/), [Binary Tree](https://leetcode.com/tag/binary-tree/)

## Solution 1. Post-order Traversal

```cpp
// OJ: https://leetcode.com/problems/largest-bst-subtree/
// Author: github.com/lzl124631x
// Time: O(N)
// Space: O(H)
class Solution {
    int ans = 0;
    array<int, 3> dfs(TreeNode *root) { // min, max, count. If invalid, count = -1
        if (!root) return {INT_MAX,INT_MIN,0};
        auto left = dfs(root->left), right = dfs(root->right);
        bool valid = left[2] != -1 && right[2] != -1 && left[1] < root->val && right[0] > root->val;
        if (valid) ans = max(ans, 1 + left[2] + right[2]);
        return {left[2] ? left[0] : root->val, right[2] ? right[1] : root->val, valid ? 1 + left[2] + right[2] : -1};
    }
public:
    int largestBSTSubtree(TreeNode* root) {
        dfs(root);
        return ans;
    }
};
```
