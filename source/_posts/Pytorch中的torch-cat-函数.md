title: Pytorch中的torch.cat()函数
author: wangjiansen
tags:
  - Pytorch
  - 深度学习
categories:
  - 深度学习
date: 2019-02-09 11:53:00
---
cat是concatnate的意思：拼接，联系在一起。
- - - 
先说cat( )的普通用法
如果我们有两个tensor是A和B，想把他们拼接在一起，需要如下操作：
~~~python
C = torch.cat( (A,B),0 )  #按维数0拼接（竖着拼）
C = torch.cat( (A,B),1 )  #按维数1拼接（横着拼）
~~~

~~~python
>>> import torch
>>> A=torch.ones(2,3)    #2x3的张量（矩阵）                                     
>>> A
tensor([[ 1.,  1.,  1.],
        [ 1.,  1.,  1.]])
>>> B=2*torch.ones(4,3)  #4x3的张量（矩阵）                                    
>>> B
tensor([[ 2.,  2.,  2.],
        [ 2.,  2.,  2.],
        [ 2.,  2.,  2.],
        [ 2.,  2.,  2.]])
>>> C=torch.cat((A,B),0)  #按维数0（行）拼接
>>> C
tensor([[ 1.,  1.,  1.],
         [ 1.,  1.,  1.],
         [ 2.,  2.,  2.],
         [ 2.,  2.,  2.],
         [ 2.,  2.,  2.],
         [ 2.,  2.,  2.]])
>>> C.size()
torch.Size([6, 3])
>>> D=2*torch.ones(2,4) #2x4的张量（矩阵）
>>> C=torch.cat((A,D),1)#按维数1（列）拼接
>>> C
tensor([[ 1.,  1.,  1.,  2.,  2.,  2.,  2.],
        [ 1.,  1.,  1.,  2.,  2.,  2.,  2.]])
>>> C.size()
torch.Size([2, 7])
~~~
其次，cat还可以把list中的tensor拼接起来。
比如：

~~~python
>>> x = torch.Tensor([ [1],[2],[3] ])
>>> x1 = [ x*2 for i in range(1,4) ]
>>> x.shape
torch.Size([3,1])
>>> len(x1)
3
>>> x1
[tensor( [[2.],
		  [4.],
          [6.]] ),tensor( [[2.],
          [4.],
          [6.]] ),tensor( [[2.],
          [4.],
          [6.]] )]
>>> x2 = torch.cat( (x1),1 )
>>> x2
tensor([[2.,2.,2.],
		[4.,4.,4.],
        [6.,6.,6.]])
>>> type(x1)
list
~~~
上面的代码可以合成一行来写：
~~~python
>>> x2 = torch.cat( [x*2 for i in range(1,4)],1 )
>>> x2
tensor([[2.,2.,2.],
		[4.,4.,4.],
        [6.,6.,6.]])
~~~