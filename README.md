
前段时间忙于写系列文章，怒刷算法题的进度算是耽误了不少。刚好遇到了一道需要滑动窗口的题目，做完之后觉得挺有意思，有必要好好聊一下滑动窗口。
所谓滑动窗口（slide window）是一种优化算法的抽象概念，主要于解决数组、字符串等线性结构中的子数组或子序列问题。它的整个思路是通过维护一个窗口（window）在数组上滑动，每次滑动一个单元距离，从而减少重复计算。
滑动窗口一般分为2种：


**固定大小窗口**：窗口的大小是固定的，通过移动窗口在数组或字符串上的位置来解决问题。例如：求数组种固定长度子数组的最大和。


**可变大小窗口**：根据条件动态增大或者缩小窗口。例如：求不超过给定和的最大子数组。


对于固定大小窗口问题，最典型的就是leetCode第三十题：


**给定一个字符串 s 和一个包含多个长度相同的单词的数组 words**，要求找出 s 中**所有的起始索引**，这些起始索引所对应的子串正好是 words 中所有单词的**串联排列**（**顺序不限定**，但每个单词必须用一次）。


示例：



```
输入：
s = "barfoothefoobarman"
words = ["foo", "bar"]

输出：
[0, 9]

解释：
从索引 0 的子串是 "barfoo"，它是 ["foo", "bar"] 的串联。
从索引 9 的子串是 "foobar"，它也是 ["foo", "bar"] 的串联。

```

利用滑动窗口，可以很容易实现如下代码：



```
from collections import Counter
from typing import List

class Solution:
    def findSubstring(self, s: str, words: List[str]) -> List[int]:
        if not s or not words:
            return []

        word_len = len(words[0])
        word_count = len(words)
        total_len = word_len * word_count
        word_freq = Counter(words)
        result = []

        # 只遍历 word_len 次
        for i in range(word_len):
            left = i
            right = i
            seen = Counter()
            matched_count = 0

            while right + word_len <= len(s):
                # 提取当前单词
                word = s[right:right + word_len]
                right += word_len

                if word in word_freq:
                    seen[word] += 1
                    matched_count += 1

                    # 如果当前单词出现次数超过了预期，移动左指针
                    while seen[word] > word_freq[word]:
                        left_word = s[left:left + word_len]
                        seen[left_word] -= 1
                        matched_count -= 1
                        left += word_len

                    # 如果匹配的单词数量等于 words 中的单词数量，记录起始索引
                    if matched_count == word_count:
                        result.append(left)
                else:
                    # 当前单词不在 words 中，重置窗口
                    seen.clear()
                    matched_count = 0
                    left = right

        return result


```

在上述的代码中，定义了left和right两个指针，不断地移动left和right的位置，来让窗口中的字符串符合条件。当单词出现的频率大于预期的数量，则把left变量向右移动一个单词长度（word\_len)，并且减少seen变量中对应单词的数量，同时减少matched\_count。


当遇到不在规定中的单词，则重置整个窗口。


这里第一个循环比较取巧，只循环单词长度（word\_len)就能遍历出所有的不重复结果。


第二个例子是大小不固定的窗口案例，题目是：


**求数组中和不超过 k 的最大长度子数组。**



```
def get_max_sub_arry_length(nums: List[int], k: int) -> int:
    left = 0
    max_length = 0
    sum = 0
    for right in range(len(nums)):
        # 扩大窗口
        sum += nums[right]

        while sum > k and left <= right:
            # 缩小窗口
            sum -= nums[left]
            left += 1
        # 重新计算最大长度
        max_length = max(max_length, right - left + 1)
    
    return max_length

nums = [1,2,3,4,5]
k = 8
print(get_max_sub_arry_length(nums, k))

```

 本博客参考[FlowerCloud机场](https://yunbeijia.com)。转载请注明出处！
