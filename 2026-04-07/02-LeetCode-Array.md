# 算法练习 - 力扣数组专题

## 📅 时间
2026-04-07 17:10-18:30 (1h20m)

## 🎯 学习目标
- 复习数组基本概念和操作
- 完成 3 道力扣数组相关题目（从简单开始）
- 理解时间复杂度和空间复杂度分析

### 推荐题目
1. [两数之和](https://leetcode.cn/problems/two-sum/) - Easy
2. [删除有序数组中的重复项](https://leetcode.cn/problems/remove-duplicates-from-sorted-array/) - Easy
3. [买卖股票的最佳时机](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock/) - Easy

## 📝 学习过程
<!-- 在这里记录你的学习过程、遇到的问题、解决方案 -->
```
class Solution(object):

    def longestConsecutive(self, nums):

        """

        :type nums: List[int]

        :rtype: int

        """

        hash={}

        max_len=0

        for num in nums:        

            if num-1  not in nums:

                hash[num]=1

                i=1

                cur_len=1

                while num+i in nums:

                    i+=1

                    hash[num]+=1

                    cur_len+=1

                if cur_len>max_len:

                    max_len=cur_len

        return max_len
```
这是第一次的代码，时间复杂度n方超时，并且用字典没有集合去重效果好


## ✅ 完成内容
<!-- 列出你完成的具体内容 -->
- [x] 题目 1：最长序列排序
- [ ] 题目 2：
- [ ] 题目 3：


## 💡 收获与思考
<!-- 你的收获、疑问、下一步想法 -->


## 🔗 参考资料
<!-- 用到的教程、文档链接 -->


---
*此文件由 yjh 🦞 自动创建 | 最后更新：2026-04-06*
