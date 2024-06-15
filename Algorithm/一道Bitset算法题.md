
前几周遇到一个很有趣的算法题，问题是：

```markdown
You are given an integer array `rewardValues` of length `n`, representing the values of rewards.

Initially, your total reward `x` is 0, and all indices are **unmarked**. You are allowed to perform the following operation **any** number of times:

- Choose an **unmarked** index `i` from the range `[0, n - 1]`.
- If `rewardValues[i]` is **greater** than your current total reward `x`, then add `rewardValues[i]` to `x` (i.e., `x = x + rewardValues[i]`), and **mark** the index `i`.

Return an integer denoting the **maximum** _total reward_ you can collect by performing the operations optimally.

**Constraints:**
- `1 <= rewardValues.length <= 50000`
- `1 <= rewardValues[i] <= 50000`
```
**Example 1:**
**Input:** rewardValues = [1,1,3,3]
**Output:** 4
**Explanation:**
During the operations, we can choose to mark the indices 0 and 2 in order, and the total reward will be 4, which is the maximum.

**Example 2:**
**Input:** rewardValues = [1,6,4,3,2]
**Output:** 11
**Explanation:**
Mark the indices 0, 2, and 1 in order. The total reward will then be 11, which is the maximum.

我一开始的做法是维持一个所有和的有序set，每次二分找一个合适的值来算最大值，然后再把低于这个值的全部加一遍，放进这个set。
仔细想下就能发现这个是O(n^2)的复杂度，但我想不出更好的了。

看了答案才知道，这种数据量足够小的情况，是可以用一个bitset来做的，比如这里最大值就是2N，那么我们可以设一个100000大小的bitset，每个bit的意思为对应数字是否是存在的，比如第一个bit如果为1，就是说0这个值存在，第3001个bit如果为1，就是说值为3000的数字存在。

这样规定后，数字和bit是一一对应的，对于“对每个小于k的值加k”这个操作，可以用一个mask先与一下，然后右移k位——遍历操作只需要一个与操作及一个右移操作就可以完成，非常优雅。
这也是bitset的核心优势吧。

PS：这道题的Python写法简单又优雅，因为不需要定义一堆bitset<>：

```Python
class Solution:
	def solve(rewardValues):
		rewardValues = sorted(list(rewardValues)
		x = 1
		for r in rewardValues:
			y = x & ((1 << r) - 1)
			x |= y << r;
		return x.bit_length() - 1
```
