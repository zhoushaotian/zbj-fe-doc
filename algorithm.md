# 算法
## 1.数组中重复的数字
### 描述
在一个长度为 n 的数组里的所有数字都在 0 到 n-1 的范围内。数组中某些数字是重复的，但不知道有几个数字是重复的，也不知道每个数字重复几次。请找出数组中任意一个重复的数字。例如，如果输入长度为 7 的数组 {2, 3, 1, 0, 2, 5}，那么对应的输出是第一个重复的数字 2。
要求复杂度为 O(N) + O(1)，也就是时间复杂度 O(N)，空间复杂度 O(1)。因此不能使用排序的方法，也不能使用额外的标记数组。牛客网讨论区这一题的首票答案使用 nums[i] + length 来将元素标记，这么做会有加法溢出问题。
### 解
```js
/**
 * 关键在于此数组中的所有数字都在0-n-1范围内且数组长度为n，也就是指如果升序排列这个数组对应位置的元素值应该等于索引
 * 每次遍历到一个位置就将这个元素i放到第i个位置
 * 若遍历到某个位置发现该位置的元素对应位置的元素值相等则说明该元素已经存在
 */
function sort(arr) {
    for(let arr_i = 0; arr_i < arr.length; arr_i++) {
        while(arr[arr_i] !== arr_i) { // 当元素不在对应位置的时候交换
            if(arr[arr_i] === arr[arr[arr_i]]) {
                return arr[arr_i];
            }// 当对应位置已经有元素的时候
            let temp = arr[arr_i];
            arr[arr_i] = arr[temp];
            arr[temp] = temp;
        }
    }
    return -1;
}
```
## 2.二维数组中数字查找
### 描述
在一个二维数组中，每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序。请完成一个函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数。
```
Consider the following matrix:
[
  [1,   4,  7, 11, 15],
  [2,   5,  8, 12, 19],
  [3,   6,  9, 16, 22],
  [10, 13, 14, 17, 24],
  [18, 21, 23, 26, 30]
]

Given target = 5, return true.
Given target = 20, return false.
```
### 解
```js
/**
* 对每个数而言在它左边的数都比它小，在它下面的数都比它大。从右上角的数开始，每次遍历的时候比较这个数
* 若目标数比它小说明他在这个数左边
* 反之在下边, 有点像是二分查找
* 时间复杂度 O(M+N)
*/
function find(arr, target) {
    let rows = arr.length;
    let cols = arr[0].length;
    let r = 0;
    let c = cols - 1;
    while(r < rows && c >= 0) {
        if(arr[r][c] === target) {
            return true
        }else if(arr[r][c] > target) {
            c--;
        }else {
            r++;
        }
    }
    return false;
}
```
## 3.从头到尾打印链表
### 描述
输入链表的第一个节点，从尾到头反过来打印出每个结点的值。
### 解
```js
/**
1. 使用栈
先遍历整个链表，每次遍历的时候就向数组中push节点的值，遍历完成之后再利用数组出栈，直到数组为空
时间复杂度O(n) 空间复杂度O(n)
2. 使用头插法
先创建一个空节点，然后遍历原链表，每次遍历到一个节点就将该节点插到之前的空节点之前
*/
function reveseNodeList(headNode) {
    let result = [];
    let point = headNode;
    while(point !== null) {
        result.push(point.value);
        point = point.next;
    }
    for(i = result.length; i > 0; i = result.length) {
        console.log(result.pop());
    }
}

function headInsert(headNode) {
    let newHead = {
        value: null,
        next: null
    };
    let point = headNode;
    let temoPoint = null
    while(point !== null) {
        tempPoint = point.next; // 保存该指针指向节点的下一个节点
        newHead.value = point.value; // 将该节点的值赋给新建空节点
        point.next = newHead; // 将该节点指向新建空节点
        newHead = point; // 将该节点视为新建头节点
        point = tempPoint; // 将指针指向原节点的下一个节点
    }
    return newHead;
}
```
## 4. 重建二叉树
### 描述
根据二叉树的前序遍历和中序遍历的结果，重建出该二叉树。假设输入的前序遍历和中序遍历的结果中都不含重复的数字。
```
preorder = [3,9,20,15,7]
inorder =  [9,3,15,20,7]
```
### 解
前序遍历的第一个元素总是二叉树的根节点，而根据这个根节点在中序遍历的数组中可以将二叉树的左右子树找出，然后递归这个过程，直到遍历完整个数组
```js
/**
 * preL: 当前子树在前序遍历中的起始位置
 * preR: 当前子树在前序遍历中的结束位置
 * inL: 当前子树在中序遍历中的起始位置
 * inR: 当前子树在中序遍历中的结束位置
 * */
function rebuildTree(inorder, preorder, preL, preR, inL, inR) {
    if(preL > preR) { // 当前序遍历数组中左子树根节点的索引大于右子树的索引则遍历结束
        return null;
    }
    let curNode = {
        value: preorder[preL],
        left: null,
        right: null
    }
    let rootIndex = inorder.indexOf(preorder[preL]); // 找出根元素在中序遍历数组中的索引
    let curTreeArr = rootIndex - inL; // 当前左子树的长度
    curNode.left = rebuildTree(inorder, preorder, preL + 1, preL + curTreeArr, inL, inL + curTreeArr - 1);
    curNode.right = rebuildTree(inorder, preorder, preL + curTreeArr + 1, preR, inL + curTreeArr + 1, inR);
    return curNode;
}
```
## 5.二叉树的下一个节点
### 描述
给定一个二叉树和其中的一个结点，请找出中序遍历顺序的下一个结点并且返回。注意，树中的结点不仅包含左右子结点，同时包含指向父结点的指针。
### 解
```js
/**
此节点存在两种情况，第一种情况，当该节点的右子树为空的时候，那么在中序遍历中该节点的下一节点则为第一个左连接指向该节点的父节点，第二种情况，当此节点的右子树不为空，那么该节点的下一节点则为右子树的最左节点
*/
function findInorderNextNode(targetNode, head) {
    if(targetNode.right === null) { // 右子树为空 
        while(targetNode.parent !== null) {
            let parentNode = targetNode.parent;
            if(parentNode.left === targerNode) {
                return parentNode;
            }
            targetNode = targetNode.parent;
        }
    }else {
        while(targetNode.left !== null) {
            targetNode = targerNode.left;
        }
        return targetNode;
    }
}
```