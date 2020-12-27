- copyOf 

  - 源码：

  ```java
  	public static int[] copyOf(int[] original, int newLength) {
      	int[] copy = new int[newLength];
      	System.arraycopy(original, 0, copy, 0,
      	        Math.min(original.length, newLength));
      	return copy;
      } 
  ```
  - 功能：将original数组的前newLength位复制存入一个新的数组中，返回这个数组

    original: 需要复制的原数组

    newLength:复制到新数组的长度  

- 使用实例：  

```java
int[] origin = {0,1,2,3,4,5,6};
int[] dest = Arrays.copyOf(origin,4);
System.out.println(Arrays.toString(dest))

OUT : [0,1,2,3]
```

此时dest为origin[0]~origin[2]的复制

- copyOfRange  

  - 源码：

  ```
      public static int[] copyOfRange(int[] original, int from, int to) {
          int newLength = to - from;
          if (newLength < 0)
              throw new IllegalArgumentException(from + " > " + to);
          int[] copy = new int[newLength];
          System.arraycopy(original, from, copy, 0,
                  Math.min(original.length - from, newLength));
          return copy;
      }
  ```

  - 功能：将original数组的从 下标为from~下标为to(不包含original[to])的子数组复制存入一个新的数组中，返回这个数组，新数组length为to - from

  - 使用示例：  

  ```
  int[] origin = {0,1,2,3,4,5,6};
  int[] dest = Arrays.copyOf(origin,1,4)
  System.out.println(Arrays.toString(dest))
  
  OUT : [1,2,3]
  ```

-  equals  

  - 源码  

    ```
    
    ```

    

