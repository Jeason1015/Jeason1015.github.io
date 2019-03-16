title: Pytorch中的squeeze()和unsqueeze()函数
author: wangjiansen
tags:
  - 深度学习
  - Pytorch
  - ''
categories:
  - 深度学习
date: 2019-02-14 22:37:00
---
在numpy库中，经常会出现“秩为1的一维数组”（come from 吴恩达的深度学习，目前还没有搞清楚numpy中如此设计的意图）。比如：

~~~python
>>> a = torch.rand(3)
>>> a.shape
torch.Size([3])
~~~
注意这里的a的shape是[3] ，既不是 [1,3] 也不是 [3,1]。这就说明它既不是行向量也不是列向量，只是一个数组。

但是我们可以用squeeze（）和unsqueeze（）对其进行操作，比如：
~~~python
>>> a = torch.rand(3)
>>> a.shape
torch.Size([3])
>>> b = a.unsqueeze(1)
>>> b.shape
torch.Size([3,1])
>>> c = a.unsqueeze(0)
>>> c.shape
torch.Size([1,3])
~~~
在对这两个函数讲解之前，我们先统一一下定义:
~~~python
>>> x.shape
torch.Size([a,b,c])
~~~
这里我们说a是第一个维度（表示第一个维度的数量是a），b是第二个维度（表示第一个维度的数量是b），c是第三个维度（表示第三个维度的数量是c）。

python中很多函数都是这样表示**“维度”**这个概念的,比如numpy.stack()。

简而言之，unsqueeze（arg）是增添第arg个维度为1，以插入的形式填充。比如：
~~~python
>>> a = torch.rand(1,3)
>>> a.shape
torch.Size([1,3])
>>> b = a.unsqueeze(1)
>>> b.shape
torch.Size([1,1,3])
>>> b
tensor([[[0.6289,0.0814,0.9177]]])
>>> a
tensor([[0.6289,0.0814,0.9177]])
>>> c = a.unsqueeze(0)
>>> c.shape
torch.Size([1,1,3])
>>> d = a.unsqueeze(2)
>>> d.shape
torch.Size([1,3,1])
~~~
相反，squeeze（arg）是删除第arg个维度(如果当前维度不为1，则不会进行删除)，比如：
~~~python
>>> d.shape
torch.Size([1,3,1])
>>> e = d.squeeze(0)
>>> e.shape
torch.Size([3,1])
>>> e
tensor([[0,6289],
		[0.0814],
        [0.9177]])
>>> f = d.squeeze(1)
>>> f.shape
torch.Size([1,3,1])
~~~

最后，奥哈哈，情人节快乐～～！！！**(\*^__^\*) ……**