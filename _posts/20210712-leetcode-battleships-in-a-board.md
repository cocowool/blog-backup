---
title: Leetcode:Battleships in a board
date: 2021-07-12 08:23:34
tags:
keywords: leetcode
description: Leetcode 题目解答 Battleships in a board。
---

### 题目要求

Given an 2D board, count how many battleships are in it. The battleships are represented with '**X**'s, empty slots are represented with '**.**'s. You may assume the following rules:
* You receive a valid board, made of only battleships or empty slots.
* Battleships can only be placed horizontally or vertically. In other words, they can only be made of the shape 1xN (1 row, N columns) or Nx1 (N rows, 1 column), where N can be of any size.
* At least one horizontal or vertical cell separates between two battleships - there are no adjacent battleships.

### 解题思路

注意这个题目里限定了甲板上存在的一定是符合规范的战舰，也就是不会存在相邻或者拐弯的情况。有了这个限定，解题就比较容易，遍历一下数组，我们只需要找到战舰起始点，就可以统计出来战舰的熟练。刚开始，我没有理解透这个限定，用了DFS的方法也能完成题目。

### 参考题解

```python
class Solution:
    def countBattleships(self, board):
        """
        :type board: List[List[str]]
        :rtype: int
        """
        count = 0
        rowStop = 0
        columnStop = 0
        x = 0
        y = 0
        rowSize = len(board)
        columnSize = len(board[0])
                
        #print(self.findEnd(board,0,3) )   
        
        #return count
        
        while(x < rowSize):
            while(y < columnSize):
                # DFS 算法
                # r = self.findEnd(board,x,y)
                # if(r != False):
                #    print("board[" + str(x) + "][" + str(y) + "]")
                    # print( "Value = " + board[x][y])                    
                #    count = count + r

                # 只考虑起始点的解法
                if( board[x][y] == "X"):
                    if( ( y == 0 or board[x][y-1] != "X") and ( x == 0 or board[x-1][y] != "X")  ):
                        count = count + 1
                y = y + 1
            y = 0
            x = x + 1
                
        return count

    def findEnd(self, board, x, y, direction = ""):
        if( x >= len(board) or y >= len(board[x])):
            return -1
        
        # print( "board[" + str(x) + "][" + str(y) + "], len(board) = " + str(len(board)) + ", len(board[x]) = " + str(len(board[x])))
        # print(board[x][y])

        RightEnd = None
        DownEnd = None
        if( direction == "R"):
            if(board[x][y] == "X"):
                if(y+1 == len(board[x])):
                    return -1
                return self.findEnd(board,x,y+1,"R")
            elif(board[x][y] == "."):
                return y
        elif( direction == "D"):
            if(board[x][y] == "X"):
                if(x+1==len(board)):
                    return -1
                return self.findEnd(board,x+1,y,"D")
            elif(board[x][y] == "."):
                return x
        else:
            if(board[x][y] == "X"):
                RightEnd = self.findEnd(board,x,y+1, "R")
                DownEnd = self.findEnd(board,x+1,y, "D")
                # print( "RightEnd = " + str(RightEnd) + ", DownEnd = " + str(DownEnd))
            elif(board[x][y] == "."):
                RightEnd = y
                DownEnd = x
    
        # Check Up
        if(DownEnd != x and (x-1 >= 0)):
            if(board[x-1][y] == "X"):
                return False
        
        # Check Left
        if(RightEnd != y and (y-1 >= 0)):
            if(board[x][y-1] == "X"):
                return False
                
        # Combine Both Right and Down
        if( RightEnd == y and DownEnd == x ):
            return False
        elif( RightEnd >= y + 1 and DownEnd >= x + 1 ):
            return 1
        elif( RightEnd == -1 and DownEnd == -1):
            return 1
        elif( RightEnd == -1 and DownEnd >= x + 1):
            return 1
        elif( RightEnd >= y + 1 and DownEnd == -1):
            return 1
        else:
            return False
```

参考资料：
1、[图的基本算法（BFS和DFS）](https://www.jianshu.com/p/70952b51f0c8)
2、[邻接矩阵](https://baike.baidu.com/item/邻接矩阵/9796080?fr=aladdin)
3、[Battleships in a board](https://leetcode.com/problems/battleships-in-a-board/description/)