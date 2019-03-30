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