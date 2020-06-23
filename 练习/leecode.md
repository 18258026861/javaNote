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

