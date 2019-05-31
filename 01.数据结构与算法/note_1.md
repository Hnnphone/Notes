# 数据结构与算法

## No.1 冒泡排序

### 一般冒泡排序的写法
```
function BubbleSort(arr) {
    for (var i = 0; i < arr.length - 1; i++) { // 确定排序趟数
		for (var j = 0; j < arr.length - 1 - i; j++) { // 确定比较次数，随趟数递减
			if (arr[j] > arr[j + 1]) {
				// 交换值
				var temp = arr[j];
				arr[j] = arr[j + 1];
				arr[j + 1] = temp;
			}
			console.log('第' + j + '次比较');
		}
		console.log('第' + i + '趟排序结果为：' + arr.join(' '));
	}
}
```

### 优化一：在交换的地方加一个标记，如果那一趟排序没有交换元素，说明这组数据已经有序，不用再继续下去。
```
function BubbleSort1(arr) {
	for (var i = 0; i < arr.length - 1; i++) { // 确定排序趟数
		var flag = true; // 判断每一趟下去是否有交换
		for (var j = 0; j < arr.length - 1 - i; j++) { // 确定比较次数，随趟数递减
			if (arr[j] > arr[j + 1]) {
				// 交换值
				var temp = arr[j];
				arr[j] = arr[j + 1];
				arr[j + 1] = temp;

				flag = false; // 加入标记
			}
			console.log('第' + j + '次比较');
		}
		if (flag) { // 如果没有交换过元素，则已经有序
			return;
		}
		console.log('第' + i + '趟排序结果为：' + arr.join(' '));
	}
}
```

### 优化二: 可以记下最后一次交换的位置，后边没有交换，必然是有序的，然后下一次排序从第一个比较到上次记录的位置结束即可。
```
function BubbleSort2(arr) {
	var pos = arr.length - 1;
	for (var i = 0; i < arr.length - 1; i++) { // 确定排序趟数
		var flag = true;
		for (var j = 0; j < pos; j++) { // 确定比较次数，随趟数递减
			if (arr[j] > arr[j + 1]) {
				// 交换值
				var temp = arr[j];
				arr[j] = arr[j + 1];
				arr[j + 1] = temp;

				flag = false; // 加入标记
				pos = j; // 记录最后一次交换的位置
			}
			console.log('第' + j + '次比较');
		}
		if (flag) { // 如果没有交换过元素，则已经有序
			return;
		}
		console.log('第' + i + '趟排序结果为：' + arr.join(' '));
	}
}
```

### 优化三: 大致思想就是一次排序可以确定两个值，正向扫描找到最大值交换到最后，反向扫描找到最小值交换到最前面。
```
function BubbleSort3(arr) {
    //...
}
```

## No.2 选择排序

简单理解 选择排序就是 从一个未知数据空间，选取数据之最 放到一个新的空间。
```
function selectionSort(arr) {
	for (var i = 0; i < arr.length - 1; i++) { // 趟数
		var minIndex = i;
		for (var j = i + 1; j < arr.length; j++) {
			if (arr[j] < arr[minIndex]) { // 寻找最小的数
				minIndex = j; // 将最小数的索引保存
			}
		}
		// 交换值
		var temp = arr[i];
		arr[i] = arr[minIndex];
		arr[minIndex] = temp;

		console.log('第' + i + '趟排序结果为：' + arr.join(' '));
	}
}
```

## No.3 快速排序

快速排序是一种分治思想，即在每一趟挑选一个基准元素（pivot），并让其它比它大的元素移动到数组的一边，比它小的元素移动到数组的另一边，从而把数组拆解成了两个部分。

如何移动元素，具体有两种方法：
1. 挖坑法
2. 指针交换法

具体实现如下：
```
function quickSort(arr, startIndex, endIndex) {
	// 递归结束条件：startIndex 大于等于 endIndex 的时候，表明数组不可分割
	if (startIndex >= endIndex) {
		return;
	}

	// 得到基准元素位置
	var pivotIndex = partition(arr, startIndex, endIndex);
	// 根据基准元素，分成两部分递归排序
	quickSort(arr, startIndex, pivotIndex - 1);
	quickSort(arr, pivotIndex + 1, endIndex);
}

// 采用指针交换法得出基准元素位置
function partition(arr, startIndex, endIndex) {
	// 取第一个位置的元素作为基准元素
	var pivot = arr[startIndex];
	var left = startIndex;
	var right = endIndex;

	while (left != right) {
		// 控制 right 指针比较并左移
		while (left < right && arr[right] > pivot) { //否则，待交换
			right--;
		}
		// 控制 left 指针比较并右移
		while (left < right && arr[left] <= pivot) { //否则，待交换
			left++;
		}
		// 交换 left 和 right 指向的元素
		if (left < right) {
			var p = arr[left];
			arr[left] = arr[right];
			arr[right] = p;
		}
	}

	// pivot和指针重合点交换
	var p = arr[left];
	arr[left] = arr[startIndex];
	arr[startIndex] = p;

	return left;
}
```

## No.4 反转数组

```
function reverseArr(arr) {
	// 遍历，依据交换次数
	var len = arr.length;
	for (var i = 0; i < len / 2; i++) {
		var temp = arr[len - 1 - i];
		arr[len - 1 - i] = arr[i];
		arr[i] = temp;
	}

	return arr;
}
```

## No.5 数组去重

方式一，indexOf 方法，注意并不支持 IE8
```
function dr1(arr) {
	// 声明一个空的新数组，用来保存不重复数据
	var temp = [];
	for (var i = 0; i < arr.length; i++) {
		if (temp.indexOf(arr[i]) == -1) {
			temp.push(arr[i]);
		}
	}

	return temp;
}
```

方式二，思路：取原数组中 最大下标的重复值 放入到新数组内
```
function dr2(arr) {
	var temp = [];
	for (var i = 0; i < arr.length; i++) {
		for (var j = i + 1; j < arr.length; j++) {
			if (arr[i] === arr[j]) {
				i++; // 有重复，当前值不可取，外层循环下标右移
				j = i; // j 重置
			}
		}
		temp.push(arr[i]);
	}
	
	return temp;
}
```

## No.6 判断回文

```
function checkHuiWen(input) {
	if (typeof input !== 'string') return false;
	return input.split('').reverse().join('') === input;
}
```

## No.7 数组的全排列 - 深度优先搜索 - 回溯、穷举

```
var visited = new Array(8); // 标记节点的访问状态

function dfs(arr, depth, newArr) {
	if (depth === arr.length) {
		// 搜索到底部, 输出当前排列数据
		console.log(newArr);
		return;
	}
	for (var i = 0; i < arr.length; i++) {
		if (!visited[i]) {
			visited[i] = true;
			newArr.push(arr[i]);
			dfs(arr, depth + 1, newArr); // 深度递归
			newArr.pop(); // 回退
			visited[i] = false; // 修改状态
		}
	}
}
```

## No.8 01背包问题 - 动态规划

### 问题
现在有一个背包，容量为 m，然后有 n 个货物，重量分别为 w1,w2,w3...wn，每个货物的价值是 v1,v2,v3...vn，w 和 v 没有任何关系，求背包能装下的最大价值？
提示语：放与不放、矩阵、二维数组。

### 思路
假设 货物1 重量为 W1，价值为 V1，则只需分别计算出剩余货物在 m 容量中的最大价值f1和 (m-W1 容量中的最大价值 + V1)f2，最后比较f1、f2即可，以此类推...
```
// 公式推导 max{f(i-1, j), f(i-1, j - Wi) + Vi}，j>= Wi ∩ i > 0 
// 其中 i 表示可选货物递增， j 表示容量递增
function knapsack(weights, values, m) {
	var n = weights.length; // 货物总数量，可选货物数递增
	var f = new Array(n);
	// 构建二维数组
	for (var i = 0; i < n; i++) {
		f[i] = [];
	};
	for (var i = 0; i < n; i++) {
		for (var j = 0; j <= m; j++) { // 容量递增
			if (i === 0) { // 第一行
				f[i][j] = j < weights[i] ? 0 : values[i] // 当容量大于等于第一个货物的重量时，最大价值恒等于 货物1 的价值
			} else {
				if (j < weights[i]) { // 若当前容量装载不下当前货物，则取原最优值
					f[i][j] = f[i - 1][j] // 等于之前的最优值
				} else {
					// 在选择当前货物与不选择之间作判断
					f[i][j] = Math.max(f[i - 1][j], f[i - 1][j - weights[i]] + values[i])
				}
			}
		}
	}
	return f[n - 1][m]; // 矩阵右下角值即为最优值
}
```

## No.9 扩展 - 背包问题（物品可分割，贪心算法）

- 最优量度准则：性价比高的物体优先
- rate: values[i]/weights[i]