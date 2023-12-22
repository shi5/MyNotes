---
date created: 2023-10-25 16:10
---

# torch&numpy

## torch

- torch.gather()
	- 从原tensor中获取指定dim和指定index的数据

### tensor

用n个固定的标量来作为下标，会使得结果比原始数据降低n个维数。

## numpy

- `numpy.stack(arrays, axis)`
	- arrays是需要堆叠的数组
	- axis是值对数组哪一维的数据进行堆叠
		- 如：`numpy.stack([a,b,c,d], axis)`，abcd是二维数组
			- axis = 0：堆叠的数据就是a,b,c,d
			- axis = 1: 堆叠的数据就是a,b,c,d中的每一行数据
			- axis = 2：堆叠的数据就是a,b,c,d中的每一行数据的每一个元素
