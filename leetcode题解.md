* [1. Two Sum](#1-two-sum)
* [2. Add Two Numbers](#2-add-two-numbers)
* [3. Longest Substring Without Repeating Characters](#3-longest-substring-without-repeating-characters)
* [4. Median of Two Sorted Arrays](#4-median-of-two-sorted-arrays)
* [5. Longest Palindromic Substring](#5-longest-palindromic-substring)

### 

### 1. Two Sum

#### 解题思路：

#### 暴力解法O\(N^2\)：

嵌套两层循环：第一层：i 从 0 到 n - 2；第二层：j 从 i + 1 到 n - 1；判断 nums\[i\] + nums\[j\] == target ，如果成立则是正确答案

#### map解法O\(N\*logN\)：

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

### 2. Add Two Numbers

#### 解题思路：

从左到右遍历链表，依次相加，每一个位置生成一个新的结点即可。

时间复杂度：O\( max\( len\(l1\), len\(l2\) \) \)

#### 考虑边界条件：

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

## 3. Longest Substring Without Repeating Characters

#### 解题思路：

#### 暴力解法  O\(N^3\)：

从每个点遍历、依次遍历这个点后的每一个点、通过遍历该点之前的点判断该点是否出现过。听上去有点拗口，代码在下方，这个暴力方法Leetcode也可以AC，但是不推荐使用。

#### 头尾标记法 O\(N\*logN\)：

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

## 4. Median of Two Sorted Arrays

#### 解题思路：

这道题咋一看像二分查找，但是仔细看题，发现有两个有序数组，而且不是让我们找一个特定的数，而是要找两个数组合并后的中位数，这样一看就比较难了，也难怪归类为hard类别。这道题除了一下介绍的二分查找法，还有两个数组分别进行二分查找的方法，不过代码量相对更多\(也可能是因为笔者水平不够导致代码量过大\)，而且看了下面的二分查找法后，感叹于该算法作者的脑洞，所以在这里只介绍该种方法。

#### 暴力方法 O\(\(m + n\)\*log\(m + n\)\)：

将两个数组合并，然后进行快排，中间的数即中位数。由于题目说了复杂度不能超过O\(log\(m + n\)\)，所以这个方法当然回Time Limit Excess，所以我们得探究一种更高效的解法。

#### 二分查找法 O\(log\(min\(m, n\)\)\)：

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

#### 考虑边界条件：

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

## 5. Longest Palindromic Substring

#### 解题思路：

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

## 6. ZigZag Conversion

#### 解题思路：

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

## 7. Reverse Integer

#### 解题思路：

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

## 8. String to Integer \(atoi\)

#### 解题思路：

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

## 9. Palindrome Number

#### 解题思路：

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

### 10. Regular Expression Matching

#### 解题思路：

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

## 11. Container With Most Water

#### 解题思路：

这道题可以考虑暴力枚举的方式，复杂度应该是O\(N^2\)，嵌套循环即可实现，下面重点讲复杂度只有O\(N\)的**头尾标记法**。

考虑到：

**容器的容量 = 两端中较矮的板子长度（L） \* 两端的距离（d）**

在数组两端设置头尾标记，以头尾标记为容器的两端，假设头标记对应的板子长度比较短，那么现在 容器的容量 = 头标记板子 \* 头尾标记的距离。那么以头标记为一端的所有容器的容量必然小于当前容器，因为其他以头标记为一端的容器的**L**一定小于或等于当前容器，而距离**d**一定小于当前容器。所以这些容器都可以无需遍历，因此让头标记向尾部移动一个单位；假设尾标记对应的板子长度比较短，则以此类推，让尾标记向头部移动一个单位，直至头尾标记相遇则停止寻找。

实现代码：

```javascript
// 11. Container With Most Water
int maxArea(vector<int>& height) {
  int res = 0, i = 0, j = height.size() - 1;
  while (i != j) {
    res = max(res, (j - i) * min(height[i], height[j]));
    if (height[i] > height[j]) {
      j--;
    } else {
      i++;
    }
  }
  return res;
}
```

## 12. Integer To Roman

#### 解题思路：

这道题没有特别多技巧，直接按照转换规则进行转换即可，对每一位进行遍历，注意考虑4和9的特殊情况即可。

在查看Leetcode讨论区的时候还看到了一种脑洞大开的“全部映射法”，这里贴上来给大家欣赏一下，也开拓了一下思路，这种方法告诉我们在情况不多的时候，可以考虑一下这种思路，代码更为简洁。

实现代码：

```javascript
// 12. Integer to Roman
string intToRoman(int num) {
  map<int, char> romanMap;
  romanMap[1] = 'I';
  romanMap[5] = 'V';
  romanMap[10] = 'X';
  romanMap[50] = 'L';
  romanMap[100] = 'C';
  romanMap[500] = 'D';
  romanMap[1000] = 'M';
  string res;
  for (int i = 3; i >= 0; i--) {
    int fold = pow(10, i);
    if (num / fold == 9) {
      res += romanMap[fold];
      res += romanMap[fold * 10];
    } else if (num / fold == 4) {
      res += romanMap[fold];
      res += romanMap[fold * 5];
    } else if (num / fold > 0) {
      if (num / fold >= 5) {
        res += romanMap[fold * 5];
        num -= 5 * fold;
      }
      for (int i = 0; i < num / fold; i++) {
        res += romanMap[fold];
      }
    }
    num %= fold;
  }
  return res;
}
// 脑洞大开的全部映射法
string intToRoman(int num) {
  string M[] = {"", "M", "MM", "MMM"};
  string C[] = {"", "C", "CC", "CCC", "CD", "D", "DC", "DCC", "DCCC", "CM"};
  string X[] = {"", "X", "XX", "XXX", "XL", "L", "LX", "LXX", "LXXX", "XC"};
  string I[] = {"", "I", "II", "III", "IV", "V", "VI", "VII", "VIII", "IX"};
  return M[num/1000] + C[(num%1000)/100] + X[(num%100)/10] + I[num%10];
}
```

## 13. Roman To Integer

#### 解题思路：

这道题和上一题一样，直接按照转换规则进行转换即可，对每一位进行遍历，注意考虑4和9的特殊情况即可。

实现代码：

```javascript
// 13. Roman To Integer
int romanToInt (string s) {
  int res = 0;
  for (int i = 0; i < s.size(); i++) {
    if (s[i] == 'I') {
      res += 1;
    } else if (s[i] == 'V') {
      res += 5;
      if (i - 1 >= 0 && s[i - 1] == 'I') {
        res -= 2;
      }
    } else if (s[i] == 'X') {
      res += 10;
      if (i - 1 >= 0 && s[i - 1] == 'I') {
        res -= 2;
      }
    } else if (s[i] == 'L') {
      res += 50;
      if (i - 1 >= 0 && s[i - 1] == 'X') {
        res -= 20;
      }
    } else if (s[i] == 'C') {
      res += 100;
      if (i - 1 >= 0 && s[i - 1] == 'X') {
        res -= 20;
      }
    } else if (s[i] == 'D') {
      res += 500;
      if (i - 1 >= 0 && s[i - 1] == 'C') {
        res -= 200;
      }
    } else if (s[i] == 'M') {
      res += 1000;
      if (i - 1 >= 0 && s[i - 1] == 'C') {
        res -= 200;
      }
    }
  }
  return res;
}
```

## 14. Longest Common Prefix

#### 解题思路：

这道题从头到尾每一个字符遍历，如果出现不同或者已经不够长，则把已知的共同前缀返回即可。

**考虑边界条件：**  
当输入字符串数组为空时，返回空字符串""。

实现代码：

```javascript
// 14. Longest Common Prefix
string longestCommonPrefix(vector<string>& strs) {
  if (strs.size() == 0) return "";
  int length = 0;
  while (true) {
    if (strs[0].size() < length + 1) break;
    for (int i = 0; i < strs.size() - 1; i++)
      if (strs[i + 1].size() < length + 1 || strs[i][length] != strs[i + 1][length])
        return strs[0].substr(0, length);
    length++;
  }
  return strs[0].substr(0, length);
}
```

## 15. 3Sum

#### 解题思路：

**双指针法**枚举，复杂度O\(N^2\)。

首先，对数组进行排序。然后，从第一个数字 i 开始遍历，每一层遍历中有两个指针 p, q 分别指向该数字后续的数组中的头尾两端，通过判断这三个数组的和与0的关系，移动头尾指针：

**如果和大于0，尾指针前移；如果和小于0，头指针后移；如果和等于0，分别移动头尾指针。**

这里注意要考虑到数组中处理出现重复数字的情况。

**如果 i 与 i - 1重复则直接跳过该项的遍历，如果 p 重复则 p++，如果 q 重复则 q++。**

**考虑边界条件：**

当输入数组长度不足3时，返回空字符串数组。

实现代码：

```javascript
// 15. 3Sum
vector<vector<int> > threeSum(vector<int>& nums) {
  vector<vector<int> > res;
  if (nums.size() < 3) return res;
  sort(nums.begin(), nums.end());
  int p, q, sum;
  for (int i = 0; i < nums.size() - 2; i++) {
    if (nums[i] > 0) 
      break;
    else if (i > 0 && nums[i] == nums[i - 1]) 
      continue;
    p = i + 1;
    q = nums.size() - 1;
    while (p < q) {
      sum = nums[i] + nums[p] + nums[q];
      if (sum == 0) {
        res.push_back({nums[i], nums[p], nums[q]});
        while (nums[p] == nums[p + 1] && p < q)
          p++;
        p++;
        while (nums[q] == nums[q - 1] && p < q)
          q--;
        q--;
      } else if (sum > 0) {
        while (nums[q] == nums[q - 1] && p < q)
          q--;
        q--;
      } else {
        while (nums[p] == nums[p + 1] && p < q)
          p++;
        p++;
      }
    }
  }
  return res;
}
```

## 16. 3Sum Closest

#### 解题思路：

与15题如出一辙，采用双指针法枚举，复杂度O\(N^2\)。

首先，对数组进行排序。然后，从第一个数字 i 开始遍历，每一层遍历中有两个指针 p, q 分别指向该数字后续的数组中的头尾两端，通过判断这三个数组的和与0的关系，移动头尾指针：

**如果和大于0，返回target；如果和小于0，头指针后移；如果和等于0，分别移动头尾指针。**

这里注意要考虑到数组中处理出现重复数字的情况。

**如果 i 与 i - 1重复则直接跳过该项的遍历，如果 p 重复则 p++，如果 q 重复则 q++。**

实现代码：

```javascript
// 16. 3Sum Closest
int threeSumClosest(vector<int>& nums, int target) {
  sort(nums.begin(), nums.end());
  int p, q, sum;
  int gap = INT_MAX;
  int res;
  for (int i = 0; i < nums.size() - 2; i++) {
    if (i > 0 && nums[i] == nums[i - 1]) continue;
    p = i + 1;
    q = nums.size() - 1;
    while (p < q) {
      sum = nums[i] + nums[p] + nums[q];
      if (sum == target) {
        return target;
      } else if (sum > target) {
        if (sum - target < gap) {
          gap = sum - target;
          res = sum;
        }
        while (nums[q] == nums[q - 1] && p < q)
          q--;
        q--;
      } else {
        if (target - sum < gap) {
          gap = target - sum;
          res = sum;
        }
        while (nums[p] == nums[p + 1] && p < q)
          p++;
        p++;
      }
    }
  }
  return res;
}
```

## 17. Letter Combinations of a Phone Numbe

#### 解题思路：

经典的排列问题，直接用回溯法解决。

下面的解法是使用循环实现非递归的回溯法。linshi作为中间变量，每一个数字按下之后的结果。

实现代码：

```javascript
// 17. Letter Combinations of a Phone Number
vector<string> letterCombinations(string digits) {
  if (digits.size() == 0) return {};
  map<int, string> digitalMap;
  digitalMap[2] = "abc";
  digitalMap[3] = "def";
  digitalMap[4] = "ghi";
  digitalMap[5] = "jkl";
  digitalMap[6] = "mno";
  digitalMap[7] = "pqrs";
  digitalMap[8] = "tuv";
  digitalMap[9] = "wxyz";
  vector<string> linshi;
  vector<string> res = {""};
  for (int i = 0; i < digits.size(); i++) {
    string tails = digitalMap[digits[i] - 48];
    for (int j = 0; j < res.size(); j++) {
      for (int n = 0; n < tails.size(); n++) {
        linshi.push_back(res[j] + tails[n]);
      }
    }
    res = linshi;
    linshi = {};
  }
  return res;
}
```

## 18. 4Sum

#### 解题思路：

和15题3Sum如出一辙，在3Sum的解法外面再套一层即可。

实现代码：

```javascript
// 18. 4Sum 
vector<vector<int>> fourSum(vector<int>& nums, int target) {
      vector<vector<int> > res;
  if (nums.size() < 4) return res;
  sort(nums.begin(), nums.end());
  int p, q, sum;
  for (int j = 0; j < nums.size() - 3; j++) {
    if (j > 0 && nums[j] == nums[j - 1])
      continue;
    int target3 = target - nums[j];  
    for (int i = j + 1; i < nums.size() - 2; i++) {
      if (i > j + 1 && nums[i] == nums[i - 1]) 
        continue;
      p = i + 1;
      q = nums.size() - 1;
      while (p < q) {
        sum = nums[i] + nums[p] + nums[q];
        if (sum == target3) {
          res.push_back({nums[j], nums[i], nums[p], nums[q]});
          while (nums[p] == nums[p + 1] && p < q)
            p++;
          p++;
          while (nums[q] == nums[q - 1] && p < q)
            q--;
          q--;
        } else if (sum > target3) {
          while (nums[q] == nums[q - 1] && p < q)
            q--;
          q--;
        } else {
          while (nums[p] == nums[p + 1] && p < q)
            p++;
          p++;
        }
      }
    }
  }
  return res;
}
```

## 19. Remove Nth Node From End of List

#### 解题思路：

**快慢指针思想**，让两个指针 p, q都指向头结点，p 先向后移动 n + 1 步，然后p, q一起向后移动，当 p 到达尾结点时，q指向目标节点的前驱结点，做删除操作，然后按照题目要求返回头结点即可。

实现代码：

```javascript
// 20. Valid Parentheses
bool isValid(string s) {
  stack<char> brackets;
  map<char, char> bracketMap;
  bracketMap[')'] = '(';
  bracketMap[']'] = '[';
  bracketMap['}'] = '{';
  for (int i = 0; i < s.size(); i++) {
    if (s[i] == '(' || s[i] == '[' || s[i] == '{') {
      brackets.push(s[i]);
    } else if (s[i] == ')' || s[i] == ']' || s[i] == '}') {
      if (!brackets.empty() && brackets.top() == bracketMap[s[i]]) {
        brackets.pop();
      } else {
        return false;
      }
    }
  }
  if (brackets.empty()) return true;
  else return false;
}
```

## 20. Valid Parentheses

#### 解题思路：

**栈思想**，从左往右遍历，如果是左括号则入栈，右括号则出栈，最后判断栈是否为空，空则为有效括号组，否则无效。

实现代码：

```javascript
// 20. Valid Parentheses
bool isValid(string s) {
  stack<char> brackets;
  map<char, char> bracketMap;
  bracketMap[')'] = '(';
  bracketMap[']'] = '[';
  bracketMap['}'] = '{';
  for (int i = 0; i < s.size(); i++) {
    if (s[i] == '(' || s[i] == '[' || s[i] == '{') {
      brackets.push(s[i]);
    } else if (s[i] == ')' || s[i] == ']' || s[i] == '}') {
      if (!brackets.empty() && brackets.top() == bracketMap[s[i]]) {
        brackets.pop();
      } else {
        return false;
      }
    }
  }
  if (brackets.empty()) return true;
  else return false;
}
```

## 21. Merge Two Sorted Lists

#### 解题思路：

从头至尾一起遍历，每次比较头结点的大小，先添加小的结点，直到某一个链表为空为止，最后再将另一个还不为空的链表添加到末尾即可。

实现代码：

```javascript
// 21. Merge Two Sorted Lists
ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
  ListNode* head = new ListNode(0);
  ListNode* p = head;
  while (l1 != NULL && l2 != NULL) {
    if (l1->val < l2 ->val) {
      p->next = l1;
      l1 = l1->next;
    } else {
      p->next = l2;
      l2 = l2->next;
    }
    p = p->next;
  }
  if (l1 != NULL) {
    p->next = l1;
  } else if (l2 != NULL) {
    p->next = l2;
  }
  return head->next;
}
```

## 22. Generate Parentheses

#### 解题思路：

**回溯法**，用一个数组记录【当前字符串，左括号数量，右括号数量】，做 2n 次循环，每次循环遍历当前数组，对左括号不足 n 的添加左括号，右括号不足左括号的添加右括号（确保parentheses是有效的）

实现代码：

```javascript
// 22. Generate Parentheses O(2^N)
vector<string> generateParenthesis(int n) {
  if (n == 0) return {};
  // nowString, leftCount, rightCount
  vector<pair<string, pair<int, int> > > res = {{"", {0, 0}}};
  vector<pair<string, pair<int, int> > > linshi;
  for (int i = 0; i < 2 * n; i++) {
    for (int j = 0; j < res.size(); j++) {
      string oldString = res[j].first;
      if (res[j].second.first < n) {
        linshi.push_back({oldString + '(', {res[j].second.first + 1, res[j].second.second}});
      }
      if (res[j].second.first > res[j].second.second) {
        linshi.push_back({oldString + ')', {res[j].second.first, res[j].second.second + 1}});
      }
    }
    res = linshi;
    linshi = {};
  }
  vector<string> result;
  for (int i = 0; i < res.size(); i++) {
    result.push_back(res[i].first);
  }
  return result;
}
```

## 23. Merge k Sorted Lists

#### 解题思路：

考虑使用**优先队列**，这道题相当于21题的加强版，但是如果单纯使用21题的每轮比较法，那么每轮只要要比较 k 次，而我们知道这其中肯定会存在很多重复的比较，所以我们可以使用二叉堆来保存每一轮的比较信息，而在C++中STL已经为我们实现了这种数据结构，那就是优先队列priority\_queue，直接调用即可。

实现代码：

```javascript
// 23. Merge k Sorted Lists
ListNode* mergeKLists(vector<ListNode*>& lists) {
  ListNode *dump = new ListNode(0), *p = dump, *nowPoint;
  // val, node
  priority_queue<pair<int, ListNode*>, vector<pair<int, ListNode*>>, greater<pair<int, ListNode*>> > pQueue;
  for (ListNode* node : lists) {
    if (node != NULL)
      pQueue.push({node->val, node});
  }
  while (!pQueue.empty()) {
    nowPoint = pQueue.top().second;
    p->next = nowPoint;
    p = p->next;
    pQueue.pop();
    if (nowPoint->next != NULL) {
      pQueue.push({nowPoint->next->val, nowPoint->next});
    }
  }
  return dump->next;
}
```

## 24. Swap Nodes in Pairs

#### 解题思路：

两两交换即可，这里每轮需要变换三个指向，第一个的next要指向第二个的next，第二个的next要指向第一个，前驱的next要指向第二个，每轮过后让 p 指向第一个即可。

实现代码：

```javascript
// 24. Swap Nodes in Pairs
ListNode* swapPairs(ListNode* head) {
  ListNode *dump = new ListNode(0), *p = dump, *first, *second;
  dump->next = head;
  while (p != NULL && p->next != NULL && p->next->next != NULL) {
    first = p->next;
    second = p->next->next;
    first->next = second->next;
    second->next = first;
    p->next = second;
    p = first;
  }
  return dump->next; 
}
```

## 25. Reverse Nodes in k-Group

#### 解题思路：

这题相当于24题的加强版，将24题中的2变成了k，换汤不换药，稍微复杂一点，所以建议把每一步的思路理清楚，不然很容易弄错，大的思路是先检查是否还剩下足够数量的结点；然后进行反向操作：反向操作分为三步，中间结点关系反向，头结点指向处理 和 尾结点指向处理；最后让 p 结点后挪即可。

实现代码：

```javascript
// 25. Reverse Nodes in k-Group
ListNode* reverseKGroup(ListNode* head, int k) {
  if (k == 1) return head;
  vector<ListNode*> nodeList;
  ListNode *dump = new ListNode(0), *p = dump, *former, *latter, *nextLatter, *q;
  dump->next = head;
  while (true) {
    // check count of left nodes
    q = p;
    for (int i = 0; i < k + 1; i++) {
      if (q != NULL) {
        q = q->next;
      } else {
        return dump->next;      
      }
    }
    // deal with medial relations 
    former = p->next;
    latter = former->next;
    for (int i = 0; i < k - 1; i++) {
      // save latter->next as next latter
      nextLatter = latter->next;
      // latter->next = former
      latter->next = former;
      // save latter as next former  
      former = latter;
      latter = nextLatter;
    }
    // deal with head
    q = p->next;
    p->next = former;
    // deal with tail
    q->next = latter;

    p = q;
  }
  return dump->next;      
}
```

## 26. Remove Duplicates from Sorted Array

#### 解题思路：

按照题意，把数组中不重复出现的数字**保存在数组前端**即可。

实现代码：

```javascript
// 26. Remove Duplicates from Sorted Array
int removeDuplicates(vector<int>& nums) {
  if (nums.size() == 0) return 0;
  int j = 1;
  for (int i = 1; i < nums.size(); i++) {
    if (nums[i] != nums[i - 1]) {
      nums[j++] = nums[i];
    }
  } 
  return j;      
}
```

## 27. Remove Element

#### 解题思路：

这道题和 26 题几乎没有区别，把判断条件稍微改变即可。

实现代码：

```javascript
// 27. Remove Element
int removeElement(vector<int>& nums, int val) {
  int j = 0;
  for (int i = 0; i < nums.size(); i++) {
    if (nums[i] != val) {
      nums[j++] = nums[i];
    }
  }
  return j;
}
```

### 28. Implement strStr\(\)

#### 解题思路：

字符串匹配算法，选择比较多，最容易的就是**暴力匹配**，高阶一点可以使用**KMP**，这里简单起见，采用暴力解法，嘻嘻。

实现代码：

```javascript
// 28. Implement strStr()
int strStr(string haystack, string needle) {
  if (needle.size() == 0) return 0;
  int hLen = haystack.size(), nLen = needle.size();
  for (int i = 0; i < hLen; i++) {
    if (hLen - i < nLen) {
      return -1;
    } else {
      if (haystack.substr(i, nLen).compare(needle) == 0) {
        return i;
      }
    }
  }
  return -1;
}
```

### 29. Devide Two Integers

#### 解题思路：

这道题如果用减法去实现会造成时间复杂度过高，从而导致时间溢出，所以这里采用**位运算法**，从 2 的 31 次方开始除，一直除到 2 的 0 次方，为了防止内存溢出，我们把结果和中间变量保存在 long 类型中的。

实现代码：

```javascript
// 29. Divide Two Integers
int divide(int dividend, int divisor) {
  if (dividend > INT32_MAX || dividend < INT32_MIN || divisor > INT32_MAX || divisor < INT32_MIN) return INT32_MAX;
  long son = abs((long)divisor), father = abs((long)dividend), res = 0, base = 1, sum = 0;
  for (int i = 31; i >= 0; i--) {
    if (sum + (son << i) <= father) {
      sum += son << i;
      res += base << i;
    }
  }
  if ((dividend >= 0 && divisor > 0) || (dividend < 0 && divisor < 0)) {
    return (res > INT32_MAX) ? INT32_MAX : res;
  } else {
    return (-res < INT32_MIN) ? INT32_MAX : -res;
  }
}
```

## 30. Substring with Concatenation of All Words

#### 解题思路：

使用**哈希表**存储 words 数组中各个单词的数量，然后使用暴力匹配即可。

实现代码：

```javascript
// 30. Substring with Concatenation of All Words
vector<int> findSubstring(string s, vector<string>& words) {
  vector<int> result;
  if (words.size() == 0 || words[0].size() == 0 || s.size() < words.size() * words[0].size()) {
    return result;
  }
  unordered_map<string, int> counts;
  for (string word : words) {
    if (counts.find(word) == counts.end()) {
      counts[word] = 1;
    } else {
      counts[word]++;
    }
  }
  int wordCount = words.size(), wordLength = words[0].size(), strLength = s.size();
  for (int i = 0; i <= strLength - wordLength * wordCount; i++) {
    unordered_map<string, int> innerCounts = counts;
    bool flag = true;
    for (int j = 0; j < wordCount; j++) {
      string nowStr = s.substr(i + j * wordLength, wordLength);
      if (innerCounts.find(nowStr) == innerCounts.end() || innerCounts[nowStr] == 0) {
        flag = false;
        break;
      } else {
        innerCounts[nowStr]--;
      }
    }
    if (flag) {
      result.push_back(i);
    }
  }
  return result;
}
```



