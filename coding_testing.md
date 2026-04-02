### Equinix Coding Test:

Given an array of integers numbers and an integer target, return indices of the two numbers such that they add up to target.

You may assume that each input would have exactly one solution, and you may not use the same element twice.

You can return the answer in any order.

**Question:**

Example 1:
Input: nums = [11,2,15,7], target = 9
Output: [1,3]
Explanation: Because nums[1] + nums[3] == 9, we return [1, 3].

Example 2:
Input: nums = [3,2,4], target = 6
Output: [1,2]

Solution:
✅ Use looping to solve the Two Sum problem.
```java

   int[] nums =  {11,2,15,7}; //{3,2,4};
   int target =  9; //6;
   List<String> result = new ArrayList<>();

    for (int i=0;i<nums.length;i++){
        for(int j=0;j<nums.length;j++){
            int temp = nums[i] + ((i!=j) ? nums[j] : 0);
            if(temp == target && result.isEmpty()){
                System.out.println(nums[i]  +","+ nums[j]);
                result.add(i+","+j);
            }
        }
    }
    if(result.isEmpty()){
         System.out.println("Target matching value not found!");
     } else {
         System.out.println(result);
     }

   Result:   
   This array of element [2,7] equal to target values ; Indices :[1,3]
```

✅ This is the classic Two Sum problem. The most efficient way is to use a `HashMap` to store numbers and their indices while iterating.

```java

   public static int[] twoSum(int[] numbers, int target) {
        HashMap<Integer, Integer> map = new HashMap<>();
        
        for (int i = 0; i < numbers.length; i++) {
            int complement = target - numbers[i];
            
            if (map.containsKey(complement)) {
                return new int[] { map.get(complement), i };
            }
            
            map.put(numbers[i], i);
        }
        
        return new int[] {}; // no solution found
    }

   //main
   int[] result = twoSum(numbers, target);
   if (result.length == 2) {
      System.out.println("This array of element ["+numbers[result[0]]+","+numbers[result[1]]+"] equal to target values ; Indices :["+result[0]+","+result[1]+"]");
   } else {
      System.out.println("No solution found");
   }

   Result:   
   This array of element [2,7] equal to target values ; Indices :[1,3]
```
