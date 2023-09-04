## <font color='red'>导弹防御系统【贪心+单调栈+dfs剪枝搜索】</font>

## **`我做的所有的题解`**

# [包括基础提高以及一些零散刷的各种各样的题](https://www.acwing.com/blog/content/33005/) 
----------
为了对抗附近恶意国家的威胁，R 国更新了他们的导弹防御系统。

一套防御系统的导弹拦截高度要么一直 严格单调 上升要么一直 严格单调 下降。

例如，一套系统先后拦截了高度为 3 和高度为 4 的两发导弹，那么接下来该系统就只能拦截高度大于 4 的导弹。

给定即将袭来的一系列导弹的高度，请你求出至少需要多少套防御系统，就可以将它们全部击落。

**输入格式**

输入包含多组测试用例。

对于每个测试用例，第一行包含整数 n，表示来袭导弹数量。

第二行包含 n 个不同的整数，表示每个导弹的高度。

当输入测试用例 n=0 时，表示输入终止，且该用例无需处理。

**输出格式**

对于每个测试用例，输出一个占据一行的整数，表示所需的防御系统数量。

**数据范围**

```
1 ≤ n ≤ 50
```

**输入样例：**

```
5
3 5 2 4 1
0 
```

**输出样例：**

```
2
```

**样例解释**
对于给出样例，最少需要两套防御系统。

一套击落高度为 3,4 的导弹，另一套击落高度为 5,2,1 的导弹。

## 题目证明

这道题和[拦截导弹](https://www.acwing.com/solution/content/142833/)（链接是我上一题的题解） 出现了不同点，**上一次是唯一确定方向，只需要一个队列去维护组的右端点**。而这道题是同时存在上升和下降子序列，问什么情况下使得拦截所有导弹的两组和最小。

仔细思考，**我们在进行判别最少组数的方法，肯定仍然是贪心做法，只不过加入了到底使用上升导弹拦截系统，还是下降导弹拦截系统。**这时只需要进行一个**`DFS`，把所有的组合情况进行组合，暴力搜索，复杂度是2的N次方**。为了避免复杂度太高，我们肯定要引入一个**`比较好的剪枝策略`**。我们保存当前搜索的最优路径和，如果之后的搜索发现此时答案超过最优答案，即可直接对后面的子树剪枝。如果递归到结尾，更新我们的最优答案。这个策略下，我们的复杂度得以大大减小。

对于贪心做法是否加组的做法，和上一题大同小异，可以直接进入我的链接去看二分法的策略，这题二分复杂度有些高，并且清理现场的操作比较麻烦，故直接采用遍历，找最先满足的点进行替换。

# java
```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;

public class Main {
    static int N = 60, n, res;
    static int [] a = new int[N];
    static int [] up = new int[N];
    static int [] down = new int[N];
    static BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

    static void dfs(int a_cnt, int up_cnt, int down_cnt){
        //如果枚举到当前数量已经大于等于目前最优解即可剪枝
        if ((up_cnt + down_cnt) >= res) return;
        //递归到尽头如果优于最优解则更新
        if (a_cnt == n) {
            if (up_cnt + down_cnt < res)    res = up_cnt + down_cnt;
            return;
        }
        //上升导弹拦截系统
        int k = 0;
        // 导弹高度上升子序列dfs --> 即击落高度递增的导弹拦截系统
        // 对于拦截系统内部队列应该是单调下降的每组结尾
        // 我们依次遍历，在第一个小于a[a_cnt]的组停下，此时替换放入为贪心最优
        // 若不存在，即当前元素小于任何一个组的结尾，需要给它开新组
        while(k < up_cnt && a[a_cnt] <= up[k]) k++;
        // 为了恢复现场
        int t = up[k];
        up[k] = a[a_cnt];
        if (k < up_cnt)
            dfs(a_cnt + 1, up_cnt, down_cnt);
        else
            dfs(a_cnt + 1, up_cnt + 1, down_cnt);
        //恢复现场
        up[k] = t;
        
        //下降导弹拦截系统
        k = 0;
        // 导弹高度下降子序列dfs --> 即击落高度递减的导弹拦截系统
        // 对于拦截系统内部队列应该是单调上升的每组结尾    
        // 我们依次遍历，在第一个大于a[a_cnt]的组停下，此时替换放入为贪心最优
        // 若不存在，即当前元素大于任何一个组的结尾，需要给它开新组        
        while(k < down_cnt && a[a_cnt] >= down[k]) k++;
        // 为了恢复现场
        t = down[k];
        down[k] = a[a_cnt];
        if (k < down_cnt)
            dfs(a_cnt + 1, up_cnt, down_cnt);
        else
            dfs(a_cnt + 1, up_cnt, down_cnt + 1);
        // 恢复现场
        down[k] = t;
    }

    public static void main(String[] args) throws IOException {
        while (true){
            n = Integer.parseInt(br.readLine());
            if (n == 0) break;
            res = n;
            String [] str = br.readLine().split(" ");
            for (int i = 0; i < n; i++) a[i] = Integer.parseInt(str[i]);
            dfs(0, 0, 0);
            System.out.println(res);
        }
    }
}

```
# python

```python
N = 60
up = [0] * N
down = [0] * N
res = N

def dfs(a_cnt, up_cnt, down_cnt):
    global res
    # 如果枚举到当前数量已经大于等于目前最优解即可剪枝
    if up_cnt + down_cnt >= res:    return
    # 如果dfs到一条路结束发现优于目前最优解就更新
    if a_cnt == n:
        if up_cnt + down_cnt < res:
            res = up_cnt + down_cnt
        return
    
    # 导弹高度上升子序列dfs --> 即击落高度递增的导弹拦截系统
    # 对于拦截系统内部队列应该是单调下降的每组结尾
    # 我们依次遍历，在第一个小于a[a_cnt]的组停下，此时替换放入为贪心最优
    # 若不存在，即当前元素小于任何一个组的结尾，需要给它开新组
    k = 0
    while k < up_cnt and up[k] > a[a_cnt]:
        k += 1
    t = up[k]
    up[k] = a[a_cnt]
    if k < up_cnt:
        dfs(a_cnt + 1, up_cnt, down_cnt)
    else:
        dfs(a_cnt + 1, up_cnt + 1, down_cnt)
    # 恢复现场
    up[k] = t
    
    # 导弹高度下降子序列dfs --> 即击落高度递减的导弹拦截系统
    # 对于拦截系统内部队列应该是单调上升的每组结尾    
    # 我们依次遍历，在第一个大于a[a_cnt]的组停下，此时替换放入为贪心最优
    # 若不存在，即当前元素大于任何一个组的结尾，需要给它开新组
    k = 0
    while k < down_cnt and down[k] < a[a_cnt]:
        k += 1
    t = down[k]
    down[k] = a[a_cnt]
    if k < down_cnt:
        dfs(a_cnt + 1, up_cnt, down_cnt)
    else:
        dfs(a_cnt + 1, up_cnt, down_cnt + 1)
    # 恢复现场
    down[k] = t


if __name__ == '__main__':
    while 1:
        n = int(input())
        if not n:   
            break
        a = [int(x) for x in input().split()]
        res = len(a)
        dfs(0, 0, 0)
        print(res)
        
```