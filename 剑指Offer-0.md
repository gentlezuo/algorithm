# 剑指Offer

### 1 最小栈
题目：定义栈的数据结构，请在该类型中实现一个能够得到栈中所含最小元素的min函数（时间复杂度应为O（1））。   
分析：使用两个栈，一个用来存所有的值，另一个存递减的值；获取最小值直接从minStack中获取；删除时需要判断是否两个栈都要删除

代码：
~~~
import java.util.Stack;

public class Solution {

    
    public Stack<Integer> s1=new Stack<>();
    public Stack<Integer> s2=new Stack<>();

    public void push(int node) {
        s1.push(node);
        if(s2.isEmpty()||s2.peek()>=node){
            s2.push(node);
        }
    }

    public void pop() {
        if(s1.peek().equals(s2.peek())){
            s2.pop();
        }
        s1.pop();
    }

    public int top() {
        return s1.peek();
    }

    public int min() {
        return s2.peek();
    }
}
~~~

### 2 栈的压入、弹出顺序
题目：输入两个整数序列，第一个序列表示栈的压入顺序，请判断第二个序列是否可能为该栈的弹出顺序。假设压入栈的所有数字均不相等。例如序列1,2,3,4,5是某栈的压入顺序，序列4,5,3,2,1是该压栈序列对应的一个弹出序列，但4,3,5,1,2就不可能是该压栈序列的弹出序列。（注意：这两个序列的长度是相等的）
分析：使用一个栈模拟入栈出栈，先压入栈，判断弹出的值是否在popA中。

代码：
~~~
import java.util.*;

public class Solution {
    public boolean IsPopOrder(int [] pushA,int [] popA) {
      Stack<Integer> stack=new Stack<>();
        int j=0;
        for(int i=0;i<pushA.length;i++){
            stack.push(pushA[i]);
            while(!stack.isEmpty()&&j<popA.length&&stack.peek()==popA[j]){
                stack.pop();
                j++;
            }
        }
        return stack.isEmpty() ? true:false;
    }
}
~~~

### 3 层次遍历二叉树
题目：从上往下打印出二叉树的每个节点，同层节点从左至右打印。  
分析：层次遍历，从左往右，使用队列
代码:
~~~
public class Solution {
    public ArrayList<Integer> PrintFromTopToBottom(TreeNode root) {
        ArrayList<TreeNode> res=new ArrayList<>();
        ArrayList<Integer> list=new ArrayList<>();
        if(root==null){
            return list;
        }
        res.add(root);
        list.add(root.val);
        int i=0;
        while(!res.isEmpty()){
            TreeNode temp=res.get(i);
            res.remove(i);
            if(temp.left!=null){
                res.add(temp.left);
                list.add(temp.left.val);
            }
            if(temp.right!=null){
                res.add(temp.right);
                list.add(temp.right.val);
            }
            
        }
        return list;
    }
}
~~~

### 4 二叉搜索树后序便利
题目：输入一个整数数组，判断该数组是不是某二叉搜索树的后序遍历的结果。如果是则输出Yes,否则输出No。假设输入的数组的任意两个数字都互不相同。
分析：根据二叉搜索树的性质，根据递归判断。对于一个序列S，最后一个元素是x （也就是根），如果去掉最后一个元素的序列为T，那么T满足：T可以分成两段，前一段（左子树）小于x，后一段（右子树）大于x，且这两段（子树）都是合法的后序序列。
代码：
~~~
public class Solution {
    public boolean VerifySquenceOfBST(int [] sequence) {
        if(sequence==null||sequence.length==0){
            return false;
        }
        return help(sequence,0,sequence.length-1);

    }

    private boolean help(int[] sequence, int st, int end) {
        if(st>=end){
            return true;
        }
        int i=st;
        //判断前半段
        for (; i < end; i++) {
            if(sequence[i]>sequence[end]){
                break;
            }
        }
        //判断后半段
        for (int j = i; j <end ; j++) {
            if (sequence[j]<sequence[end]){
                return false;
            }
        }
        //递归判断
        return help(sequence,st,i-1)&&help(sequence,i,end-1);

    }
}
~~~

### 5 二叉树中和为某一值的路径
题目：输入一颗二叉树的跟节点和一个整数，打印出二叉树中结点值的和为输入整数的所有路径。路径定义为从树的根结点开始往下一直到叶结点所经过的结点形成一条路径。(注意: 在返回值的list中，数组长度大的数组靠前)  
分析：回溯法
代码：
~~~
import java.util.ArrayList;
/**
public class TreeNode {
    int val = 0;
    TreeNode left = null;
    TreeNode right = null;

    public TreeNode(int val) {
        this.val = val;

    }

}
*/
public class Solution {
    ArrayList<ArrayList<Integer>> all=new ArrayList<>();
    ArrayList<Integer> path=new ArrayList<>();
    
    public ArrayList<ArrayList<Integer>> FindPath(TreeNode root,int target) {
        if(root==null){
            return all;
        }
        path.add(root.val);
        target-=root.val;
        if(target==0&&root.left==null&&root.right==null){
            all.add(new ArrayList<>(path));
        }
        FindPath(root.left,target);
        FindPath(root.right,target);
        path.remove(path.size()-1);
        return all;
    }
}
~~~

### 6 字符串的排列
题目：输入一个字符串,按字典序打印出该字符串中字符的所有排列。例如输入字符串abc,则打印出由字符a,b,c所能排列出来的所有字符串abc,acb,bac,bca,cab和cba。   
输入描述:  
输入一个字符串,长度不超过9(可能有字符重复),字符只包括大小写字母。   
分析：使用回溯法   
代码：
~~~

import java.util.ArrayList;
import java.util.Collections;

public class PaiLie {
    public ArrayList<String> Permutation(String str) {
        ArrayList<String> res=new ArrayList<>();
        if(str==null||str.length()==0){
            return res;
        }
        help(res,0,str.toCharArray());
        Collections.sort(res);
        return res;

    }

    private void help(ArrayList<String> res, int st,char[] cs) {
        if(st==cs.length-1){
            String temp=new String(cs);
            if (!res.contains(temp)){
                res.add(temp);
            }
            return;
        }
        for (int i = st; i <cs.length ; i++) {
            swap(cs,st,i);
            help(res,st+1,cs);
            swap(cs,st,i);

        }


    }

    private void swap(char[] cs, int j, int i) {
        char c=cs[i];
        cs[i]=cs[j];
        cs[j]=c;
    }

}
~~~

### 7 超过数组长度一半的数
题目：数组中有一个数字出现的次数超过数组长度的一半，请找出这个数字。例如输入一个长度为9的数组{1,2,3,2,2,2,5,4,2}。由于数字2在数组中出现了5次，超过数组长度的一半，因此输出2。如果不存在则输出0。  
分析：计数法，最后判断该数的次数是否大于length/2
代码：
~~~


public class MoreMidNumber {
    public int MoreThanHalfNum_Solution(int[] array) {
        int count = 1, res = array[0];
        for (int i = 1; i < array.length; i++) {
            if (array[i] == res) {
                count++;
            } else {
                if (count > 1) {
                    count--;
                } else if (count == 1) {
                    res = array[i];
                }
            }
        }
        count = 0;
        for (int a : array) {
            if (a == res) {
                count++;
            }
        }
        return count > (array.length >>> 1) ? res : 0;
    }
}

~~~
### 8 最小的k个数
题目：输入n个整数，找出其中最小的K个数。例如输入4,5,1,6,2,7,3,8这8个数字，则最小的4个数字是1,2,3,4,。  
分析：维护一个堆；排序；k次遍历   
代码：
~~~

import java.util.ArrayList;
import java.util.Comparator;
import java.util.PriorityQueue;

public class MinKNum {
    public ArrayList<Integer> GetLeastNumbers_Solution(int [] input, int k) {
        ArrayList<Integer> res=new ArrayList<>();
        if (k==0||input==null||input.length==0||k>input.length){
            return res;
        }
        PriorityQueue<Integer> heap=new PriorityQueue<>(k, new Comparator<Integer>() {
            @Override
            public int compare(Integer o1, Integer o2) {
                return o2.compareTo(o1);
            }
        });
        for (int i = 0; i <input.length ; i++) {
            if (heap.size()<k){
                heap.offer(input[i]);
            }else if(input[i]<heap.peek()){
                heap.poll();
                heap.offer(input[i]);
            }
        }
        res.addAll(heap);
        return res;


    }
}
~~~

### 9 连续子数组最大和
题目：HZ偶尔会拿些专业问题来忽悠那些非计算机专业的同学。今天测试组开完会后,他又发话了:在古老的一维模式识别中,常常需要计算连续子向量的最大和,当向量全为正数的时候,问题很好解决。但是,如果向量中包含负数,是否应该包含某个负数,并期望旁边的正数会弥补它呢？例如:{6,-3,-2,7,-15,1,2,2},连续子向量的最大和为8(从第0个开始,到第3个为止)。给一个数组，返回它的最大连续子序列的和，你会不会被他忽悠住？(子向量的长度至少是1)   
分析：动态规划max=(arr[i],max+arr[i])     
代码:
~~~
public int FindGreatestSumOfSubArray(int[] array) {
        int res=array[0],max=array[0];
        for (int i = 1; i <array.length ; i++) {
            max=Math.max(array[i],max+array[i]);
            res=Math.max(res,max);
        }

        return res;

    }
~~~

### 10 i的个数
题目：求出1~13的整数中1出现的次数,并算出100~1300的整数中1出现的次数？为此他特别数了一下1~13中包含1的数字有1、10、11、12、13因此共出现6次,但是对于后面问题他就没辙了。ACMer希望你们帮帮他,并把问题更加普遍化,可以很快的求出任意非负整数区间中1出现的次数（从1 到 n 中1出现的次数）。   
分析：暴力法   
按位求：[借鉴](https://www.nowcoder.com/profile/777651/codeBookDetail?submissionId=1503231)   
如果当前位为1，则该位出现1的个数与高位和低位有关；高+低      
如果当前为为0，则该位出现1的个数与高位有关；     高
如果当前位为2-9，则该位出现1的个数与高位有关；   高+i*10
代码：
~~~

public class CountOfOne {
    /*public int NumberOf1Between1AndN_Solution(int n) {
        int res=0;
        while (n>0){
            char[] s=String.valueOf(n).toCharArray();
            for (int i = 0; i <s.length ; i++) {
                if(s[i]=='1'){
                    res++;
                }
            }
            n--;
        }
        return res;
    }*/

    public int NumberOf1Between1AndN_Solution(int n) {

        int res=0,i=1;
        while (n/i!=0){
            int curr=n/i%10;
            int low=n-n/i*i;
            int high=n/(i*10);
            if(curr==0){
                res+=high*i;
            }else if(curr==1){
                res+=high*i+low+1;

            }else {
                res+=(high+1)*i;
            }
            i*=10;
        }
        return res;
    }

}

~~~