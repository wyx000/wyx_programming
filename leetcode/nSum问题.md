#### [1. 两数之和](https://leetcode-cn.com/problems/two-sum/)

> 给定一个整数数组 `nums` 和一个整数目标值 `target`，请你在该数组中找出 **和为目标值** *`target`* 的那 **两个** 整数，并返回它们的数组下标。
>
> ```
> 输入：nums = [2,7,11,15], target = 9
> 输出：[0,1]
> 解释：因为 nums[0] + nums[1] == 9 ，返回 [0, 1] 。
> ```

- 最简单：穷举遍历 
  - 时间复杂度O(N^2)，空间复杂度O(1)

- **借助哈希表**
  - 判断是否存在`nums[j]==target - nums[i]`,返回`i`,`j`的下标

``` c++
vector<int> twoSum(vector<int>& nums, int target) {
    int n = nums.size();
    unordered_map<int,int> index;
    for(int i = 0; i < n; i++){
        index[nums[i]] = i; 
    }
    for(int i = 0; i < n; i++){
        int other = target - nums[i];
        if(index.count(other) != 0 && index[other]!= i)
        {
            return {i,index[other]};
        }
    }
    return {-1,-1};
}
```

#### [167. 两数之和 II - 输入有序数组](https://leetcode-cn.com/problems/two-sum-ii-input-array-is-sorted/)

- 扩展一下，如果2Sum中数组是有序的

``` c++
vector<int> twoSum(vector<int>& nums, int target){
    vector<int> res{-1, -1};//初始化{-1,-1}
    int left = 0, right = nums.size() - 1;
    while(left < right){
        int sum = nums[left] + nums[right];
        if(sum == target){
            res[0] = left;
            res[1] = right;
            return res;
        }else if(sum < target){
            left++;//sum大一点
        }else if(sum > target){
            right--;//sum小一点
		}
    }
    return res;
}
```

#### 2Sum问题，返回元素的值

- 先对`nums`进行排序，再从两端开始找

``` c++
vector<int> twoSum(vector<int>& nums, int target){
    sort(nums.begin(), nums.end());
    int left = 0, right = nums.size() - 1;
    while(left < right){
        int sum = nums[left] + nums[right];
        if(sum == target){
            return {left,right};
        }else if(sum < target){
            left++;//sum大一点
        }else if(sum > target){
            right--;//sum小一点
		}
    }
    return {-1,-1};
}
```

#### 2Sum返回多对元素，且要求不重复

> `nums`中多对元素和为`target`，返回所有不重复的元素对
>
> ```
> 输入：nums=[1,2,1,2,2,3] target = 4
> 输出：[[1,3],[2,2]]
> ```

- sum == target 的分支，当`res`中加入结果后，lo和hi改变1的同时，还应跳过重复值

- 提高效率，再小于或大于target时做跳过操作【核心操作如下】

  - ```
    while(lo < hi && nums[lo] == left) lo++;//找到结果时，跳过重复值
    while(lo < hi && nums[hi] == right) hi--;
    ```

```c++
vector<vector<int>> twoSumTarget(vector<int>& nums, int target)}{
	sort(nums.begin(),nums.end());//开始前排序
    int lo = 0, hi = nums.size() - 1;
    while(lo < high){
        int left = nums[lo], right = nums[hi];//记录left，right
        int sum = left + right;
        if(sum > target)
            while(lo < hi && nums[hi] == right) hi--;
        else if(sum < target)
            while(lo < hi && nums[lo] == left) lo++;
        else if(sum == target){
            res.push_back({nums[lo],nums[high]});
            while(lo < hi && nums[lo] == left) lo++;//找到结果时，跳过重复值
            while(lo < hi && nums[hi] == right) hi--;
		}
    }
    return res;
}
```

#### [15. 三数之和](https://leetcode-cn.com/problems/3sum/)【3sum问题】

- 等价，对于每个`nums[i]`,求`target - nums[i]`的两数之和
- 改造2Sum函数，增加左指针开始的位置，为`i + 1`

```c++
class Solution {
public:
    vector<vector<int>> twoSumTarget(vector<int>& nums, int start, int target){  //左指针从start开始
        int lo = start, hi = nums.size() - 1;
        vector<vector<int>>res;
        while(lo < hi){
            int left = nums[lo], right = nums[hi];
            int sum = left + right;
            if(sum > target)
                while(lo < hi && nums[hi] == right) hi--;
            else if(sum < target)
                while(lo < hi && nums[lo] == left) lo++;
            else if(sum == target){
                res.push_back({right, left});
                while(lo < hi && nums[hi] == right) hi--;
                while(lo < hi && nums[lo] == left) lo++;
            }
        }
        return res;
    }

    vector<vector<int>> threeSum(vector<int>& nums) {
        int target = 0;
        sort(nums.begin(), nums.end());
        int n = nums.size();
        vector<vector<int>> res;
        //穷举3Sum的第一个数
        for(int i = 0; i < n; i++){
            //对 target - nums[i] 计算2Sum
            vector<vector<int>>tuples = 
            twoSumTarget(nums, i + 1, target - nums[i]);//注意，避免算自己，为i+1
            for(vector<int>& tuple : tuples){
                tuple.push_back(nums[i]);
                res.push_back(tuple);
            }
            //跳过第一个数字相同的情况 否则会重复
            while(i < n - 1 && nums[i] == nums[i + 1]) i++;
        }
        return res;
    }
    
};
```

#### [18. 四数之和](https://leetcode-cn.com/problems/4sum/)

```c++
class Solution {
public:
    vector<vector<int>> fourSum(vector<int>& nums, int target) {
        sort(nums.begin(),nums.end());
        int n = nums.size();
        vector<vector<int>> res;
        for(int i = 0; i < n; i++){
            vector<vector<int>> triples = threeSum(nums, i + 1, target - nums[i]);
            for(vector<int>& triple : triples){
                triple.push_back(nums[i]);
                res.push_back(triple);
            }  
            while(i < n - 1 && nums[i] == nums[i + 1]) i++;         
        }
        return res;
    }

    vector<vector<int>> twoSumTarget(vector<int>& nums, int start, int target){  //左指针从start开始
        int lo = start, hi = nums.size() - 1;
        vector<vector<int>>res;
        while(lo < hi){
            int left = nums[lo], right = nums[hi];
            int sum = left + right;
            if(sum > target)
                while(lo < hi && nums[hi] == right) hi--;
            else if(sum < target)
                while(lo < hi && nums[lo] == left) lo++;
            else if(sum == target){
                res.push_back({right, left});
                while(lo < hi && nums[hi] == right) hi--;
                while(lo < hi && nums[lo] == left) lo++;
            }
        }
        return res;
    }

    vector<vector<int>> threeSum(vector<int>& nums, int start, int target) {
        sort(nums.begin(), nums.end());
        int n = nums.size();
        vector<vector<int>> res;
        //穷举3Sum的第一个数
        for(int i = start; i < n; i++){//i从start开始
            //对 target - nums[i] 计算2Sum
            vector<vector<int>>tuples = 
            twoSumTarget(nums, i + 1, target - nums[i]);//注意，避免算自己，为i+1
            for(vector<int>& tuple : tuples){
                tuple.push_back(nums[i]);
                res.push_back(tuple);
            }
            //跳过第一个数字相同的情况 否则会重复
            while(i < n - 1 && nums[i] == nums[i + 1]) i++;
        }
        return res;
    }
    
};
```

#### [16. 最接近的三数之和](https://leetcode-cn.com/problems/3sum-closest/)

> 给定一个包括 n 个整数的数组 nums 和 一个目标值 target。找出 nums 中的三个整数，使得它们的和与 target 最接近。返回这三个数的和。假定每组输入只存在唯一答案。
>

- 和三数之和类似，保存最接近的数的和

``` c++
class Solution {
public:
    int threeSumClosest(vector<int>& nums, int target) {
        int n = nums.size();
        sort(nums.begin(), nums.end());
        int min_d = INT_MAX;
        int tuple, closet_v;
        for(int i = 0; i < n; i++){
            tuple = twoSumClosest(nums, i + 1, target - nums[i]);//注意目标是target - nums[i]
            if(tuple == INT_MAX)continue; //极值的处理
            if(abs(tuple + nums[i] - target) < min_d){
                min_d = abs(tuple + nums[i] - target);
                closet_v = tuple + nums[i];
                if(min_d == 0) return closet_v;
            }
        }
        return closet_v;
    }

    //找与target最接近的两数之和
    int twoSumClosest(vector<int>& nums, int start, int target){
        int n = nums.size();
        int lo = start, hi = n - 1;
        int min_d = INT_MAX; 
        int closet_v = INT_MAX;
        while(lo < hi){
            int left = nums[lo], right = nums[hi];
            int sum = left + right;
            if(sum > target){
                if(abs(sum - target) < min_d){
                    closet_v = left + right;
                    min_d = abs(sum - target);
                }
                 while(lo < hi && nums[hi] == right) hi--;
            }else if(sum < target){
                if(abs(sum - target) < min_d){
                    closet_v= left + right;
                    min_d = abs(sum - target);
                }
                while(lo < hi && nums[lo] == left) lo++;
            }else if(sum == target){
                return (left + right);
            }
        }
        return closet_v;
    }
};
```

#### [653. 两数之和 IV - 输入 BST](https://leetcode-cn.com/problems/two-sum-iv-input-is-a-bst/)

- 前序遍历 + 哈希set

``` c++
class Solution {
public:
    bool findTarget(TreeNode* root, int k) {
        unordered_set<int> s ;
        return find(root, k , s);     
    }

    bool find(TreeNode* root, int k, unordered_set<int>& s){
        if(root == nullptr)
            return false;
        if(s.count(k - root->val))
            return true;
        s.insert(root -> val);//将遍历节点加入集合
        return find(root->left, k ,s) || find(root->right, k ,s);
    }
};
```

