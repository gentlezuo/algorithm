# 剑指Offer

### 11 最小排列的数
题目：输入一个正整数数组，把数组里所有数字拼接起来排成一个数，打印能拼接出的所有数字中最小的一个。例如输入数组{3，32，321}，则打印出这三个数字能排成的最小数字为321323。  
分析：排序  
代码：
~~~java
import java.util.ArrayList;
import java.util.Comparator;
public class Solution {
    public String PrintMinNumber(int [] numbers) {
        ArrayList<Integer> list=new ArrayList<>();
        for(int a:numbers){
            list.add(a);
        }
        list.sort(new Comparator<Integer>() {
            @Override
            public int compare(Integer o1, Integer o2) {
                String s1=String.valueOf(o1);
                String s2=String.valueOf(o2);
                
                return (s1+s2).compareTo(s2+s1);
            }
        });
        StringBuilder sb=new StringBuilder();
        for (int a:list){
            sb.append(a);
        }
        return sb.toString();
    }
}
~~~

### 12丑数
题目：把只包含质因子2、3和5的数称作丑数（Ugly Number）。例如6、8都是丑数，但14不是，因为它包含质因子7。 习惯上我们把1当做是第一个丑数。求按从小到大的顺序的第N个丑数。   
分析：丑数=2^i*3^j*5^k  ，所以维护一个丑数队列，每次从里面找一个相应的数*2或3或5   
代码：
~~~java
public class Solution {
    public int GetUglyNumber_Solution(int index) {
        if(index<1){
            return 0;
        }
        int[] uglys=new int[index];
        uglys[0]=1;
        int i2=0;
        int i3=0;
        int i5=0;
        for (int i = 1; i <index ; i++) {
            int temp=Math.min(2*uglys[i2],Math.min(3*uglys[i3],5*uglys[i5]));
            if(temp==uglys[i2]*2){
                i2++;
            }
            if(temp==uglys[i3]*3){
                i3++;
            }
            if (temp==uglys[i5]*5){
                i5++;
            }
            uglys[i]=temp;
        }
        return uglys[index-1];

    }
}
~~~
### 13 第一个只出现一次的字符
题目：在一个字符串(0<=字符串长度<=10000，全部由字母组成)中找到第一个只出现一次的字符,并返回它的位置, 如果没有则返回 -1（需要区分大小写）.  
分析：计数，判断  
代码：
~~~java

public class OneTimeFirstNum {
    public int FirstNotRepeatingChar(String str) {
        int[] count = new int[128];
        for (char c : str.toCharArray()) {
            count[c]++;
        }
        //int res = str.length();
        /*for (int i = 0; i < 128; i++) {
            if (count[i] == 1) {
                res = Math.min(str.indexOf((char) (i)), res);
            }
        }*/
        for (int i = 0; i <str.length() ; i++) {
            if (count[str.charAt(i)]==1){
                return i;
            }
        }

        /*return res == str.length() ? -1 : res;*/
        return -1;
    }
}
~~~