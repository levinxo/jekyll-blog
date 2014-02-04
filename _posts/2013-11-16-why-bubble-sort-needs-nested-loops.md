---
author: levin
comments: true
date: 2013-11-16 16:48:26+08:00
layout: post
title: "为什么冒泡排序需要两次循环"
categories:
- 算法
tags:
- algorithm
---

下面是一个冒泡排序的算法：<!-- more -->

{% highlight c %}

    #include <stdio.h>
    
    #define LEN 10
    
    typedef int ele_type;
    
    void sort(ele_type *a, int length){
        int i, j;                           /*执行1次*/
        ele_type tmp;                       /*执行1次*/
        for (i=0; i<length-1; i++){         /*执行n-1次*/
            for (j=length-1; j>i; j--){     /*由n-1次递减到1次*/
                if (*(a+j-1) > *(a+j)){     /*执行1次*/
                    tmp = *(a+j);           /*执行1次*/
                    *(a+j) = *(a+j-1);      /*执行1次*/
                    *(a+j-1) = tmp;         /*执行1次*/
                }
            }
        }
    }
    
    int main(void){
        ele_type a[LEN] = {93, 20, 67, 85, 2, 55, 32, 76, 11, 43};
        int i;
        sort(a, LEN);
        for (i=0; i<LEN; i++){
            printf("%d\t", *(a+i));
        }
        return 0;
    }

{% endhighlight %}

我们可以发现在
[冒泡排序]({{ site.url_finder }}http://zh.wikipedia.org/wiki/%E5%86%92%E6%B3%A1%E6%8E%92%E5%BA%8F)
里数组循环了两次。

这是为什么呢？

从上面的`sort`函数的最外层循环看起。

第一次外层循环，`i=0`，然后开始第一次内层循环，此时`j=length-1`，那么`*(a+j)`指向数组的最后一个元素。
此元素和前一位元素相比，如果前一位大于此元素，那么交换他们两个的位置，则此元素向前移了一位。
然后第二次内层循环，倒数第二个元素继续上面的步骤，直到数组的第一个元素和第二个元素相比较，
此时数组的第一个元素就是整个数组里最小的那个元素。第一次外层循环完毕。

第二次外层循环，`i=1`，再开始第一次内层循环，接着同上，由于数组第一个元素已经是最小的了，
所以一直对比到第二个和第三个元素，此时数组第二大的元素产生了，并排在了第二位。

从上面可以看出，每次外层循环完毕，总会将一个元素排好，那么只需要`length-1`次
(为什么不是`length`次呢？因为当倒数第二个元素排好以后，最后一个元素也排好了)外层循环。
内层循环遍历至上一次外层循环完毕排好的那个元素之后的位置就跳出，进入下一个外层循环。

*总的一句话*：每次外层循环排好一个元素的位置，共`length-1`次；
每次内层循环比较相邻两个元素并移动符合条件的元素向前，比较到上一次外层循环排好的元素位置为止。

冒泡排序的时间复杂度：

        T(n) = 1+1+(n-1)*(n-1)+1+1+1+1
        T(n) = O(n^2)

即使是已经排好序的数组，冒泡排序也会进行`O(n^2)`的比较次数，对于项目比较多的数组来说是很没有效率的。
