# **Java Basic Code Interview**  

# 1. Anagram
```
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
# 2. Count the Sequence of Character
```
String str = "Java";
Map<Character, Integer> mp  = new HashMap<>();

char[] char1 = str.toCharArray();
for(char c: char1){
    mp.put(c, mp.getOrDefault(c, 0) + 1);
}

mp.entrySet().stream().forEach((k) -> System.out.println(k.getKey() + " <> " +k.getValue()));
```
# 3. Find a Palindrome Integer
```
Integer val = 121;
int original = val;
int reverse = 0;

while(val > 0){ //iterating 3 times
    reverse = reverse * 10 + (val % 10); // reverse multiply by 10 + input module(%) by 10
    val /= 10; // input divided by 10
}

if(original == reverse){
    System.out.println("This number are Palindrome");
} else {
    System.out.println("This number are not Palindrome");
}
```
# 4. Reverse Integer
```
Integer val = 143;
int reverse = 0;

while(val > 0){ //iterating 3 times
    reverse = reverse * 10 + (val % 10); // reverse multiply by 10 + input module(%) by 10
    val /= 10; // input divided by 10
}
System.out.println("reverse :: " + reverse);
```
# 5. Find first missingPositiveNumber
```
int[] numbers = {1, 2, 6, 4, 1, 2};

// 1. Remove the duplicate elements in list using Set interface.
Set<Integer> findPositiveNumber = new HashSet<>();

// 2. Iterate the Array to store each value in Set object.
for (Integer i:numbers) {
    findPositiveNumber.add(i);
}

// 3. declare the variable is missingPositiveNumber with assign the value is 1.
int missingPositiveNumber = 1;

// 4. while condition apply to check the missingPositiveNumber is present in the Set object.
// If yes than increment the value in missingPositiveNumber
while(findPositiveNumber.contains(missingPositiveNumber)){
    missingPositiveNumber += 1;
}

System.out.println(findPositiveNumber);
// 5. finally print the result.
System.out.println("Find first missingPositiveNumber" +missingPositiveNumber);
```
# 6. Print Fibonacci series using recursion
```
public static int fibonaci(int n){
    if (n <= 1){
        return n;
    }
    return fibonaci(n-1) + fibonaci(n-2);
}

public static void main(String[] args) {
    int n = 10;

    for(int i=0;i<n;i++){
        System.out.print(fibonaci(i) + " ");
    }
}
```
# 7. Fruits Shop calculation
# Fruits shop has 2 banana - (each rs 15), 5 water melon - (each RS 40), 10 orange - (each rs 50), Sold 2 banana and again added 3 banana - (each rs 25)
```
Map<String, List<FruitsBean>> fruitsCost = new HashMap<>();
fruitsCost.put("watermelon", Arrays.asList(new FruitsBean("watermelon", 5, 40)));
fruitsCost.put("orange", Arrays.asList(new FruitsBean("orange", 10, 50)));
fruitsCost.computeIfAbsent("banana", k -> new ArrayList<>()).add(new FruitsBean("banana", 2, 15));
fruitsCost.computeIfAbsent("banana", k ->  new ArrayList<>()).add(new FruitsBean("banana", 3, 25));
List<FruitsBean> result = fruitsCost.values().stream().flatMap(List::stream).collect(Collectors.toList());
Map<String, Integer> finalres = result.stream().collect(Collectors.toMap(FruitsBean::getName, val -> val.getItems() * val.getPrice(), (r1,r2) -> r2 ));
System.out.println(finalres);
```
# 8. Create 10 Train object with 1 hour plus timezone.
```
LocalDateTime localDateTime = LocalDateTime.now();
for(int i=1;i<= 10;i++){
    localDateTime = localDateTime.plusHours(1);
    Instant instant = localDateTime.atZone(ZoneId.systemDefault()).toInstant();
    Train train = new Train("Train" + i, Date.from(instant));
    System.out.println("Object created: Train Name: "+ train.getName() + ", Departure Time: " + train.getDepatureTime());
}
public class Train {
    String name;
    Date depatureTime;
    // _constructor, getter method_
}
```
# 9. Reverse String
```
// Type 1
String str = "Java";
StringBuilder sb = new StringBuilder();
sb.append(str);
System.out.println("Reverse Print using String Builder :" + sb.reverse());

// Type 2
String reverseString = "";
char[] rString = str.toCharArray();
for(int i=rString.length-1;i>=0;i--){
    reverseString += String.valueOf(rString[i]);
}
System.out.println("Reverse Print using for loop :" + reverseString);
```
