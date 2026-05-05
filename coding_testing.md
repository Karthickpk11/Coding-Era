### Optimum Solution Coding Test:
## Fruits Shop calculation    
Fruits shop has 2 banana - (each rs 15), 5 water melon - (each RS 40), 10 orange - (each rs 50), Sold 2 banana and again added 3 banana - (each rs 25)

```java
Map<String, List<FruitsBean>> fruitsCost = new HashMap<>();
fruitsCost.put("watermelon", Arrays.asList(new FruitsBean("watermelon", 5, 40)));
fruitsCost.put("orange", Arrays.asList(new FruitsBean("orange", 10, 50)));
fruitsCost.computeIfAbsent("banana", k -> new ArrayList<>()).add(new FruitsBean("banana", 2, 15));
fruitsCost.computeIfAbsent("banana", k ->  new ArrayList<>()).add(new FruitsBean("banana", 3, 25));
List<FruitsBean> result = fruitsCost.values().stream().flatMap(List::stream).collect(Collectors.toList());
Map<String, Integer> finalres = result.stream().collect(Collectors.toMap(FruitsBean::getName, val -> val.getItems() * val.getPrice(), (r1,r2) -> r2 ));
System.out.println(finalres);
```

### Wipro Coding Test:
## Find Anagram word

```java
String s1 = "listen";
String s2 = "silent";

char[] strChar = s1.toCharArray();
Arrays.sort(strChar);

char[] strChar2 = s2.toCharArray();
Arrays.sort(strChar2);

if(Arrays.equals(strChar, strChar2)){
    System.out.println("Anagram");
} else {
    System.out.println("Not Anagram");
}
```

### Equinix Coding Test:

Given an array of integers numbers and an integer target, return indices of the two numbers such that they add up to target.

You may assume that each input would have exactly one solution, and you may not use the same element twice.

You can return the answer in any order.

**Coding: 1**

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

**Coding: 2**

Given a string, return the first character that does not repeat.

If all characters repeat, return null.

Examples:

input: "swiss" ? output: "w"

input: "aabbcc" ? output: null

input: "abcabcde" ? output: "d"

FAANG-style expectation

```java

     String s1 = "swiss";
     Map<Character, Integer> charCount = new HashMap<>();

     char[] ch = s1.toCharArray();
     for(char c:ch){
         charCount.put(c, charCount.getOrDefault(c,0)+1);
     }
   
     Stream<Character> s = charCount.entrySet().stream().filter(e -> e.getValue() == 1).map(Map.Entry::getKey);
     s.findFirst().ifPresentOrElse(
             chr -> System.out.println("First character: " + chr),
             () -> System.out.println("Non Repeated Character not found!!")
     );

   Result:   
   w
```

Banking-style solution Using GroupingBy

```java
        String str = "swiss";

        Map<Character, Long> count = str.chars().mapToObj(c-> (char)c).collect(Collectors.groupingBy( c->c, Collectors.counting()));
        System.out.println(count);
        count.entrySet().stream().filter(c -> c.getValue() == 1).findFirst().ifPresentOrElse(
                c -> System.out.println("First character: " + c.getKey()),
                () -> System.out.println("Not Record found")
        );

```

```java
        String str = "swiss";

        str.chars().mapToObj(c -> (char) c).collect(Collectors.groupingBy(c -> c, Collectors.counting())).entrySet().stream().filter(s -> s.getValue() == 1).findFirst().ifPresentOrElse(c -> System.out.println("First character: " + c.getKey()), () -> System.out.println("Not Record found"));

```

### Collection operation

```java

        Supplier<List<Integer>> collList = () -> Arrays.asList(1,1,3,2,2,4,5,7,7);
         //Sorting
        List<Integer> sortingList = collList.get().stream().sorted().collect(Collectors.toList());
        System.out.println("Sorting list: " +sortingList);
         //RemoveDuplicate
        List<Integer> removeduplicate = collList.get().stream().sorted().distinct().collect(Collectors.toList());
        System.out.println("Remove duplicate : " +removeduplicate);
         //Find position of array value
        int targetValue = 9;

        List<Integer> inputLst = removeduplicate;
        List<String> storage = new ArrayList<>();

        for(int i=0;i<inputLst.size();i++){
            for(int j=i;j<inputLst.size();j++){
                int tmp = inputLst.get(i) + inputLst.get(j);
                if(tmp == targetValue){
                    storage.add("["+i+","+j+"]");
                }
            }
        }
        System.out.println("Find Indices : ");
        storage.stream().forEach(System.out::println);
         //Counting the repeated element 
        Map<Integer, Integer> counting = new HashMap<>();

        for(Integer val: sortingList){
            counting.put(val, counting.getOrDefault(val, 0) + 1);
        }
        System.out.println("Counting the repeated element : ");
        counting.entrySet().stream().forEach(res -> System.out.print("["+res.getKey() + "," + res.getValue()+"] "));
```

### Wipro Coding Test:

**Coding: 1**

Validate a string so that it accepts only numbers values. 

```java
        String input = "12345";

        if(input.matches("\\d+")){
            System.out.println("Valid number string");
        } else if(input.matches(".*\\s.*")) {
            System.out.println("Invalid: contains space");
        } else {
            System.out.println("InValid number string");
        }

```

**Coding: 2**

Print the String value in reveres format.

```java

        String str = "hello";
        StringBuilder sb = new StringBuilder();
        sb.append(str);
        System.out.println(sb.reverse());
```
