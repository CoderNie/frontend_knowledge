* [Two Sum 【easy】](#1-two-sum)
* [Add Two Numbers【medium】](#2-add-two-number-medium)
* [Longest Substring Without Repeating Characters【medium】](#3-longest-substring-without-repeating-characters-medium)
* [Median of Two Sorted Arrays](#4-median-of-two-sorted-arrays)
* [Longest Palindromic Substring](#5-longest-palindromic-substring)

## 

## 1. Two Sum

##### 解题思路：

##### 暴力解法O\(N^2\)：

嵌套两层循环：第一层：i 从 0 到 n - 2；第二层：j 从 i + 1 到 n - 1；判断 nums\[i\] + nums\[j\] == target ，如果成立则是正确答案

##### map解法O\(N\*logN\)：

从 0 到 n - 1 依次遍历，利用map存放**每一个数值的下标**，在map中寻找是否有使（nums\[i\] + x == target）成立的x的存在，如果存在则返回i和它的下标（即myMap\[ target - nums\[i\] \]\)。

复杂度分析：因为只遍历了一次数组，map每次的查询的时间复杂度为O\(logN\)所以整体复杂度为O\(N\*logN\)、如果这里使用hash\_map可以将查询复杂度降低到O\(1\)，从而使得整体复杂度为O\(N\)，但是hash\_map不是标准的C++库，所以这里没有使用。

实现代码：

```javascript
// 1. Two Sum
vector<int> twoSum(vector<int>& nums, int target) {
  map<int, int> myMap;
  vector<int> result;
  for (int i = 0; i < nums.size(); i++) {
    if (myMap.find(target - nums[i]) == myMap.end()) {
      myMap[nums[i]] = i;
    } else {
      result = {myMap[target - nums[i]], i};
      break;
    }
  }
  return result;
}
```

## 2. Add Two Numbers【medium】

##### 解题思路：

从左到右遍历链表，依次相加，每一个位置生成一个新的结点即可。

时间复杂度：O\( max\( len\(l1\), len\(l2\) \) \)

##### 考虑边界条件：

1.进位的的处理：carry表示进位，当最后一位还有进位时，即使 l1 和 l2 均为NULL的情况下，还需要生成一个新的结点，所以while的条件中加入了 carry != 0 判断项。

2.返回头结点：当头结点为NULL的时候记录头结点，并且让p等于头结点；后续情况让 p-&gt;next 等于新的结点，并让 p 指向 p-&gt;next。

实现代码：

```javascript
// 2. Add Two Numbers
ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
  ListNode *head = NULL, *p;
  int carry = 0, sum;
  while (l1 != NULL || l2 != NULL || carry != 0) {
    sum = 0;
    if (l1 != NULL) {
      sum += l1->val;
      l1 = l1->next;
    }
    if (l2 != NULL) {
      sum += l2->val;
      l2 = l2->next;
    }
    sum += carry;
    carry = sum / 10;
    sum %= 10;
    ListNode *newNode = new ListNode(sum);
    if (head == NULL) {
      head = newNode;
      p = newNode;
    } else {
      p->next = newNode;   
      p = p->next;
    }
  }
  return head;
}
```

## 3. Longest Substring Without Repeating Characters【medium】

##### 解题思路：

##### 暴力解法  O\(N^3\)：

从每个点遍历、依次遍历这个点后的每一个点、通过遍历该点之前的点判断该点是否出现过。听上去有点拗口，代码在下方，这个暴力方法Leetcode也可以AC，但是不推荐使用。

##### 头尾标记法 O\(N\*logN\)：

头标记指向当前最长无重复字符串的头部，尾标记指向其尾部。**通过一个 map 来记录出现过的字符最后出现的位置。**

依次遍历数组，如果当前字符已出现过，则让头标记指向其最后出现过的位置的后一个位置。然后每次通过头、尾标记计算当前无重复字符串的长度，并与已知最大值作比较。这里查询map的复杂度为 O\(logN\)，遍历的复杂度为 O\(N\)，因此整体复杂度为 O\(N\*logN\)。如果这里使用hash\_map可以将查询复杂度降低到O\(1\)，从而使得整体复杂度为O\(N\)，但是hash\_map不是标准的C++库，所以这里没有使用。

实现代码：

```javascript
// 3. Longest Substring Without Repeating Characters
// 暴力解法
int lengthOfLongestSubstring_bruteForce(string s) {
  int res = 0, sum;
  for (int i = s.size() - 1; i >= 0; i--) {
    sum = 1;
    for (int j = i - 1; j >= 0; j--) {
      bool flag = true;
      for (int k = i; k > j; k--) {
        if (s[j] == s[k]) {
          flag = false;
          break;
        }
      }
      if (flag) {
        sum++;
      } else {
        break;
      }
    }
    res = max(res, sum);
  }
  return res;      
}
// 头尾标记法
int lengthOfLongestSubstring(string s) {
  map<char, int> myMap;
  int res = 0;
  for (int i = 0, j = 0; j < s.size(); j++){
    if (myMap.find(s[j]) != myMap.end()) {
      i = max(i, myMap[s[j]] + 1);
    }
    myMap[s[j]] = j;
    res = max(res, j - i + 1);
  }
  return res;
}
```

## 4. Median of Two Sorted Arrays【hard】

##### 解题思路：

这道题咋一看像二分查找，但是仔细看题，发现有两个有序数组，而且不是让我们找一个特定的数，而是要找两个数组合并后的中位数，这样一看就比较难了，也难怪归类为hard类别。这道题除了一下介绍的二分查找法，还有两个数组分别进行二分查找的方法，不过代码量相对更多\(也可能是因为笔者水平不够导致代码量过大\)，而且看了下面的二分查找法后，感叹于该算法作者的脑洞，所以在这里只介绍该种方法。

##### 暴力方法 O\(\(m + n\)\*log\(m + n\)\)：

将两个数组合并，然后进行快排，中间的数即中位数。由于题目说了复杂度不能超过O\(log\(m + n\)\)，所以这个方法当然回Time Limit Excess，所以我们得探究一种更高效的解法。

二分查找法 O\(log\(min\(m, n\)\)\)：

首先分别把两个数组分成两边，大概为下面这种形式：\(A表示nums1， B表示nums2\)

```
          left_part          |        right_part
    A[0], A[1], ..., A[i-1]  |  A[i], A[i+1], ..., A[m-1]
    B[0], B[1], ..., B[j-1]  |  B[j], B[j+1], ..., B[n-1]
```

因为有序数列的性质，我们知道只要我们满足以下两个条件，我们就可以找到中位数了：

> **条件一：len\(A\_left\) + len\(B\_left\) = len\(A\_right\) + len\(B\_right\)**
>
> **条件二：max\[A\_left, B\_left\] &lt;= min\[A\_right, B\_right\]**

为了使问题简单化，我们先只考虑 m + n 为偶数的情况下，只要满足上述两个条件，我们的中位数就等于左边的最大值加上右边的最小值除以二。

为了满足条件一，我们只要令** i + j == m + n - i - j \(+ 1\)**即可（这里加一是为了之后考虑 m + n 为奇数的情况）

而为了满足条件二，根据有序数组的性质，我们知道只需要满足 **A\[i - 1\] &lt;= B\[j\] 且 B\[j - 1\] &lt;= A\[i\] **即可。

接下来开始我们的算法探究：

假设我们首先随机选择一个 i \(这里 0 &lt;= i &lt; m\)，所以我们根据条件一，可以求得 j = \(m + n + 1\) / 2 - i；

为了满足条件二，我们开始分别比较 A\[i - 1\] 与 B\[j\] 和 B\[j - 1\] 与 A\[i\]：

不难知道可能会有四种情况：

* A\[i - 1\] &lt;= B\[j\] 且 B\[j - 1\] &lt;= A\[i\] ：这不正是我们要找的 i 吗？可以直接返回答案
* A\[i - 1\] &gt; B\[j\] 且 B\[j - 1\] &gt; A\[i\] ：根据有序数列的性质，再利用反证法不难证明这种情况不可能存在
* A\[i - 1\] &lt;= B\[j\] 且 B\[j - 1\] &gt; A\[i\]：为了使情况更加接近我们的答案，也就是情况1。也就是要使 B\[j - 1\] &lt;= A\[i\]，因为现在 B\[j - 1\] &gt; A\[i\]，所以我们要想办法缩小 B\[j - 1\]，扩大A\[i\]，所以当然是让我们的 i 增大，否则情况会越来越远离我们的正确答案。
* A\[i - 1\] &gt; B\[j\] 且 B\[j - 1\] &lt;= A\[i\]：与情况3恰恰相反，这里我们应该缩小我们的 i。

那我们如何缩小和扩大我们的 i 呢，那就是采用二分查找的方式啦，首先将 i 置为数组A的中间下标，如果需要增大，则把其设为上半区的中间下标，反之则设为下半区的中间下标，所以这种搜索方式的时间复杂度和二分查找的时间复杂度一样，为了使时间复杂度尽量的小，我们使A成为长度更小的那个数组，如果初始A比B长，我们则交换它们的位置。

##### 考虑边界条件：

1.如果不存在满足条件二的情况会怎么样呢？也就是 i 走到了数组A的尽头，依旧没法满足条件二，这个时候我们不难知道如果 i 走到数组A的最左端，那么它一定是在不断地经历情况4，而这时 A\[0\] &gt; B\[j\]，那么我们不难知道这时left\_part的最大值就是B\[j - 1\]；反之我们也可以推出 i 到了A的最右端、j 到了B的最左端或者最右端的情况。

2.m + n 为奇数。这个时候我们不难推出 left\_part 的最大值就是中位数。

3.A或B为空数组，因为当数组空的时候无法对数组进行下标访问，所以我们在进行二分查找前就应该对该情况进行特殊处理，处理方式也是很简单的啦。

实现代码：

```javascript
// 4. Median of Two Sorted Arrays
double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) {
  // 使得 nums1 短于 nums2
  int m = nums1.size();
  int n = nums2.size();
  if (m > n) {
    vector<int> temp = nums1;
    nums1 = nums2;
    nums2 = temp;
    m = m + n;
    n = m - n;
    m = m - n;
  }
  // 考虑数组长度为0的边界情况
  if (m == 0) {
    if (n == 0) {
      return 0;
    } else {
      if (n % 2 == 1) {
        return nums2[n / 2];
      } else {
        return (double)(nums2[n / 2] + nums2[n / 2 - 1]) / 2;
      }
    }
  }
  int iMin = 0, iMax = m, sizeSum = (m + n + 1) / 2, i, j;
  while (iMin <= iMax) {
    i = (iMax + iMin) / 2;
    j = sizeSum - i;
    if (nums2[j - 1] > nums1[i] && i < iMax) {
      iMin = i + 1;
    } else if (nums1[i - 1] > nums2[j] && i > iMin) {
      iMax = i - 1;
    } else {
      int maxLeft, minRight;
      if (i == 0) {
        maxLeft = nums2[j - 1];
      } else if (j == 0) {
        maxLeft = nums1[i - 1];
      } else {
        maxLeft = max(nums1[i - 1], nums2[j - 1]);
      }
      if ((m + n) % 2 == 1) {
        return maxLeft;
      }
      if (i == m) {
        minRight = nums2[j];
      } else if (j == n) {
        minRight = nums1[i];
      } else {
        minRight = min(nums1[i], nums2[j]);
      }
      return (double)(maxLeft + minRight) / 2;
    }
  }
  return 0;
}
```

## 5. Longest Palindromic Substring【medium】

##### 解题思路：

##### 暴力解法 O\(N^3\)：

字符串有 n\(n-1\)/2 个子串，对每个子串进行检测，看其是否是回文子串。因此复杂度为 O\(N^3\)。

##### 从字符串中间开始扩展 O\(N^2\)：

把字符串的每个点分别当成回文子串的中间点，开始往两端扩展，不断检测，直到两端不相等为止。因此复杂度为 O\(N^2\)。

##### 动态规划 O\(N^2\)：

用 dp\[i\]\[j\] 表示下标为 i 开头 j 结尾的子串是否是回文子串。

转移方程：dp\[i\]\[j\] = \(dp\[i + 1\]\[j - 1\] && s\[i\] == s\[j\]\) 【含义：当且仅当子串首尾两端相等，且去除首尾两端依旧是回文串时，该子串才会是回文串】

初始条件：对于每个长度为1的子串 dp\[i\]\[i\] 都为回文串；对于每个长度为2的子串 dp\[i\]\[i + 1\]，当其首尾两端相等时，其为回文串，否则不是。

实现代码：

```javascript
// 5. Longest Palindromic Substring (动态规划)
string longestPalindrome(string s) {
  int length = s.size();
  if (length == 0) return s;
  int resI = 0, resJ = 0;
  bool dp[length + 1][length + 1];
  for (int i = 0; i <= length; i++)
    dp[i][i] = true;
  for (int i = 0; i < length; i++) {
    if (s[i] == s[i + 1]) {
      dp[i][i + 1] = true;
      if (resJ - resI < 1) {
        resI = i;
        resJ = i + 1;
      }
    } else {
      dp[i][i + 1] = false;
    }
  }
  for (int gap = 2; gap < length; gap++) {
    for (int i = 0; i + gap < length; i++) {
      int j = i + gap;
      if (s[i] == s[j] && dp[i + 1][j - 1]) {
        dp[i][j] = true;
        if (resJ - resI < j - i) {
          resI = i;
          resJ = j;
        }
      } else {
        dp[i][j] = false;
      }
    }
  }
  return s.substr(resI, resJ - resI + 1);
}
```

## 6. ZigZag Conversion【medium】

##### 解题思路：

这道题倒没有特别的方法，就按照题目意思来模拟 Z 字形即可，用一个字符串数组来存放每一行的字符串，最后进行拼接即可。

##### 考虑边界条件：

当numRows等于1的时候，因为point无法增加也无法减小，所以没办法共用后面的代码，考虑到numRows等于1的时候，答案就是原字符串，所以这里直接返回s即可。

实现代码：

```javascript
// 6. ZigZag Conversion
string convert(string s, int numRows) {
  if (numRows == 1) return s;
  string res;
  bool shouldIncrease = true;
  string strArr[numRows];
  int point = 0;
  for (char c : s) {
    strArr[point] += c;
    if (point == numRows - 1) {
      shouldIncrease = false;
    } else if (point == 0) {
      shouldIncrease = true;
    }
    if (shouldIncrease) {
      point++;
    } else {
      point--;
    }
  }
  for (string str: strArr) {
    res += str;
  }
  return res;
}
```

## 7. Reverse Integer【easy】

##### 解题思路：

挨个遍历，不断把末位数赋给新的值即可。

##### 考虑边界条件：

当结果溢出时返回0，所以为了不让中间值溢出，采用 long 类型来保存结果。

实现代码：

```javascript
// 7. Reverse Integer
int reverse(int x) {
  long result = 0, longX = abs((long)x);
  while (longX > 0) {
    result = result * 10 + longX % 10;
    longX /= 10;
  }
  result = (x > 0) ? result : -result;
  if (result > INT32_MAX || result < INT32_MIN) {
    return 0;
  } else {
    return (int)result;
  }
}
```

## 8. String to Integer \(atoi\)【medium】

##### 解题思路：

遍历字符串然后进行分情况讨论：（ isInit 表示数字是否已经开始，通过 isInit 的值判断是否为开头，如果为 true 表示不是开头）

\(1\) 空格：如果为开头空格则continue，否则跳出循环

\(2\) 正负号：如果为开头正负号则设置isNeg的值，否则跳出循环

\(3\) 数字：将 isInit 置为true，累加结果

\(4\) 其他符号：跳出循环

##### 考虑边界条件：

当结果溢出时根据正负返回 INT32\_MAX 或者 INT32\_MIN，所以为了不让中间值溢出，采用 long 类型来保存结果。

实现代码：

```
// 8. String to Integer (atoi)
int myAtoi(string str) {
  long result = 0;
  bool isInit = false;
  bool isNeg = false;
  for (char c : str) {
    if (c == ' ') {
      if (isInit) {
        break;
      } else {
        continue;
      }
    } else if (c == '-' || c == '+') {
      if (!isInit) {
        isInit = true;
      } else {
        break;
      }
      isNeg = (c == '-');
    } else if (c >= 48 && c <= 57) {
      isInit = true;
      result = result * 10 + (c - 48);
      if (result > INT32_MAX) { 
        return isNeg ? INT32_MIN : INT32_MAX;
      }
    } else {
      break;
    }
  }      
  return (int)(isNeg ? -result : result);
}
```

## 9. Palindrome Number【medium】

##### 解题思路：

利用第七题的代码，将数字反转，判断与原数字是否相等即可，这里考虑到负数全部都不是回文数字，所以直接返回false。

实现代码：

```javascript
// 9. Palindrome Number
int reverse(int x) {
  long result = 0, longX = abs((long)x);
  while (longX > 0) {
    result = result * 10 + longX % 10;
    longX /= 10;
  }
  result = (x > 0) ? result : -result;
  if (result > INT32_MAX || result < INT32_MIN) {
    return 0;
  } else {
    return (int)result;
  }
}
bool isPalindrome(int x) {
  if (x < 0) {
    return false;
  } else {
    return (x == reverse(x));
  }
}
```

## 10. Regular Expression Matching【hard】

##### 解题思路：

##### 动态规划：

用 dp\[i\]\[j\] 表示 s 的前 i 个字符组成的字符串和 p 的 前 j 个字符组成的字符串是否匹配。

转移方程：

当 p\[j - 1\] == '\*' 时：因为 \* 可以表示匹配零位或者多位，正则匹配这里要做贪心考虑，分三种情况，只要其中一种满足即为true：

* 匹配零位：则 dp\[i\]\[j\] = dp\[i\]\[j - 2\]
* 匹配一位：则 dp\[i\]\[j\] = dp\[i - 1\]\[j - 2\] && \(满足最后一位匹配\)
* 匹配多位：则一位一位匹配，dp\[i\]\[j\] = dp\[i - 1\]\[j\] && \(满足最后一位匹配\)

当 p\[j - 1\] != '\*' 时，dp\[i\]\[j\] 当且仅当 dp\[i - 1\]\[j - 1\]为true时，并且最后一位匹配成功时，才为true。

初始状态：

显然，当 s 不为空，p 为空的时候dp\[i\]\[j\] = false；

其次，当 s 为空，p不为空的时候，考虑到 \* 可以匹配零位，所以利用状态转移方程判断其是否应该为true。

实现代码：

```javascript
// 10. Regular Expression Matching
bool isMatch(string s, string p) {
  int n = s.size();
  int m = p.size();
  // initial
  bool dp[n + 1][m + 1];
  for (int i = 0; i < n + 1; i++) {
    for (int j = 0; j < m + 1; j++) {
      dp[i][j] = false;
    }
  }
  // start
  dp[0][0] = true;
  for (int i = 1; i < n + 1; i++) {
    dp[i][0] = false;
  }
  for (int j = 1; j < m + 1; j++) {
    if (j % 2 == 0) {
      dp[0][j] = dp[0][j - 2] && p[j - 1] == '*';
    } else {
      dp[0][j] = false;
    }
  }
  // trans
  bool compare;
  for (int i = 1; i < n + 1; i++) {
    for (int j = 1; j < m + 1; j++) {
      if (p[j - 1] != '*') {
        dp[i][j] = dp[i - 1][j - 1] && (s[i - 1] == p[j - 1] || p[j - 1] == '.');
      } else {
        compare = (s[i - 1] == p[j - 2] || p[j - 2] == '.');
        dp[i][j] = dp[i][j - 2] || (dp[i - 1][j - 2] && compare) || (dp[i - 1][j] && compare);
      }
    }
  }
  return dp[n][m];
}
```



