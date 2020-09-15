# 1.找出数组中的两数之和

给一个数组和一个和,从数组中找出两个数加起来等于这个和

```java
class Solution {
    public int[] twoSum(int[] nums, int target) {

        int[] index = new int[2];

            for(int i = 0;i<nums.length;i++){
                for(int j = i+1;j<nums.length;j++){
                    if(nums[i]+nums[j]==target){
                        index[0] = i;
                        index[1] = j;
                        return index;
                    }
                }
            }
            return index;
    }
    
}
```



# 2.从数组中找出和为指定值的元素所有集合，不可重复

给定一个数组 candidates 和一个目标数 target ，找出 candidates 中所有可以使数字和为 target 的组合。

candidates 中的每个数字在每个组合中只能使用一次。

```java
class Solution {
    //存放的结果
    List<List<Integer>> list = new ArrayList<>();
    List<Integer> path = new ArrayList<>();
    public List<List<Integer>> combinationSum2(int[] candidates, int target) {
        Arrays.sort(candidates);
        dfs(candidates,target,0);
        return list;
        
    }
    public void dfs(int []candidates,int target,int index){

           //判断目标数减去后是否满足条件
        if(target==0){
            //将空的list放入结果
                list.add(new ArrayList<>(path));
                //推出当前循环并将结果返回
                return;
        }


        for(int i=index;i<candidates.length;i++){
            //candiates当前索引的值 小于目标值才    放入
            if(candidates[i]<=target){
                //当这个索引和下一个索引相等（重复时），跳过
                if(i>index&&candidates[i]==candidates[i-1]){
                    continue;
                }


                //将该值加入一个path中
                path.add(candidates[i]);
                //递归，目标数为减去加入path的值，1.判断加入后是否满足
                //2.不满足，循环找出在该index存在情况下是否可能有满足的情况
                dfs(candidates,target-candidates[i],i+1);
                //上面递归的条件没有满足，即存在该index是情况下不可能满足条件，就移除该index
                path.remove(path.size()-1);
            }
        }
    }
}
```

# 3.找出所有相加之和为 n 的 k 个数的组合。组合中只允许含有 1 - 9 的正整数，并且每种组合中不存在重复的数字。

```java
public List<List<Integer>> combinationSum3(int k, int n) {
        dsf(k,n,1);
        return list;
    }

    public void dsf(int k,int n,int index){
        //判断放入path之后是否满足条件
        if(path.size() == k&&n==0){
            //new ArrayList<>(path)很重要,相当于创建了一个path的副本,path之后的增删操作已经和list无关了
            list.add(new ArrayList<>(path));

            return;
        }
        else{
            if(path.size() <k){
                for(int i = index;i<=9;i++){

                    path.add(i);
                    //注意点:这边的index要写成i+1,而不是index+1,这样可能会有重复
                    dsf(k,n-i,i+1);
                    path.remove(path.size()-1);
                }
        }
        }
    }
```

总结;判断时已经放入元素，所以只要长度相等和目标数为0就行。

循环时：先添加，后递归，最后删除，递归成功就返回，递归失败就删除。