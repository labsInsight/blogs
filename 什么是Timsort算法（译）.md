本文翻译了文章[《什么是Timsort算法？》](https://note.youdao.com/)，对其中部分晦涩的地方按笔者的想法进行重写，并添加了部分案例，让读者更容易理解。
> Timsort是一种数据排序算法。Timsort认为现实世界中的数据集通常是部分已经排好序的，因此它的排序策略就是识别这些有序的子序列，然后使用merge和insert方法对它们做进一步的排序。就复杂性和稳定性而言，Timsort是最好的排序算法之一。     

与“冒泡”或“插入”排序不同，Timsort是由Tim Peters（此算法也以他的名字命名）于2002年发明的比较新的一个排序算法。此后，它一直是Python，OpenJDK 7和Android JDK 1.5中的标准排序算法。从下面这个图表，我们可以知道为什么Timsort这么流行：    

![image](https://www.infopulse.com/files/2015/08/timsort-algorythm-1st-screenshot.png)    

表格貌似提供了众多的排序算法，但真正合适的只有7种（平均和最坏时间复杂度均为O(n logn)），而在这其中又只有2种具有最佳稳定性。其中一个是比较经典的树排序，另一个就是Timsort。

Timsort算法的基本思想是认为现实世界中的待排序数组总是部分有序（包括升序或降序），这符合我们的现实状况。因此，Timsort在软件开发中总是领先于其他所有算法。
# 1,前言
首先，不要以为这里面有多复杂的数字原理。事实上，Timsort并不是一门全新的算法，它只是有效结合了现有的几个算法。该算法的机制述如下：
1. 使用特定的算法把输入数组拆分为子数组。
2. 每个子数组都使用简单的插入排序进行排序。
3. 使用归并排序Merge Sort将排序后的子数组合并为一个数组。当然，这里面有很多复杂的细节，我们稍后会一一道来。
# 2,算法
## 2.1,定义
- N：输入数组的长度
- run：输入数组中的有序子数组。同时，子数组必须是升级或严格降序：    
升序：a[i−1] ≤ a[i] ≤ a[i+1]   
严格降序：a[i−1] > a[i] > a[i+1]    
（笔者加）
- minrun：算法的第一步，就是将输入数组拆分为一个个runs。minrun是每一个run的最小长度，是根据输入数组长度N计算得来的。    

![image](https://www.infopulse.com/files/2015/08/timsort-algorythm-2nd-screenshot.png) 
## 步骤0：计算Minrun
minrun是由输入数组长度N决定的，计算minrun需要遵循以下原则：

1. minrun不应该太长，因为minrun内的数据需要进行插入排序，而插入排序对短数组才比较高效。
2. minrun也不应该太短，如果minrun越短，后面合并merge的次数就越多。
3. 如果N / minrun刚好是2的整数次幂（或接近），那合并过程就可以发挥最高效率。

作者实验表明，在minrun >  256时不满足原则1，在minrun  < 8时不满足原则2，最有效的长度在32到65之间。另外：如果N < 64，那么minrun = N，Timsort变成简单的归并排序。正常情况下，minrun计算算法非常简单：从N中取出前六个最高有效位，如果其余的最低有效位包含至少有一个标志位为1，则minrun再累加1。大概的代码如下所示：

```
int GetMinrun（int n）
    {
        int r = 0;  /* 如果最低有效位包含至少一个关闭位，则 变为1 */
        while（n >= 64）{
            r | = n＆1;
            n >> = 1;
        }
        return n + r;
    }
```
示例如下：    
- 189：101111 01，取前六个最高标志位为101111(47)，同时最后两位为01，所以 minrun 为47+1，n / minrun = 4 ，满足要求。
- 976：111101 0000，取前六个最高标志位为111101(61)，同时最后几位为0000，所以 minrun为61，n / minrun = 16，满足要求。   
（笔者加）
## 步骤1：拆分runs并排序
至此，我们已经知道输入数组的长度N并计算出minrun。下一阶段，我们根据minrun对数组拆分并排序：

1. 从数组的第一个位置开始进行处理。
2. 从数组第一个元素开始计算run（有序子序列）。一般来说，就是从当前元素，一直往后累加元素到run中；至于能累加多少元素，就要看实际情况了。如果run是升序的，那么下一元素要大于或等于前一元素（a [i] <= a [i + 1] <= a [i + 2] <= ... ）；如果run是严格降序的，即运行中的前一元素大于后一元素（a [i]> a [i + 1]> a [i + 2]> ...），则将run中的元素翻转（这里注意降序的部分必须是“严格”降序才能进行翻转。因为TimSort的一个重要目标是保持稳定性的稳定性。如果在这种情况下进行翻转这个算法就不稳定）。
3. 如果有序的run长度小于minrun，则继续将后面的元素添加到run中，直到长度大于等于minrun。
4. 将子序列run进行插入排序。由于此时run长度较小且已经部分有序，所以排序的效率会非常高。
5. 取子序列的下一个元素，继续查找下一个run。
6. 如此循环依次进行run的折分排序（2~4步），直到输入数组拆分完成。     

示例：
1. 假设输入数组为[3,8,12,21,27,7,3,12,8, 21，23...]，minrun为4；
2. 从第一个元素3往后找，一直到长度为minrun，即元素21，然后我们发现21后面的元素27和当前run可以组成有序子序列，所以我们会把27继续累加到当前run；至此，我们的第一个run [3,8,12,21,27]长度大于minrun；
3. 第一个run完成之后，从下一个元素7开始往后查找下一个run为[7,3,12,8]，长度等于minrun；  
（笔者加）
## 步骤2：合并
经过步骤1的处理，输入数组已经拆分为多个有序runs。如果输入数组中的数据足够随机数，则run的大小就越接近minrun；反之，如果输入数组内部越有序，则run的大小越容易超过minrun。现在，我们需要将这些run进行归并成有序的序列。在这个过程中，我们需要注意两个点：

1. 子序列进行归并的时候，子序列的长度不应该差别太大（这样可以更高效）。
2. 应该保持算法的稳定性，不要做非必要的移位（例如，两个相等的数字不应交换位置）。

这可以通过以下方式实现：
1. 创建一个空对栈。
2. 逐步将runs入栈。
3. 评估当前run是否和栈顶的runs合并。如果满足这两个条件，则进行合并：X > Y + Z，Y > Z （X，Y和Z是堆栈中的三个顶部runs）
4. 如果不满足其中一个条件，则Y与X、Z中的较小者合并。循环入栈，并调整满足以上两个条件，直到剩余runs为0。
5. 合并栈中所有runs，完成排序。       
 
在到达数据末尾时，Timsort反复将两次运行合并到堆栈顶部，直到只剩下一次完整数据，同时满足上面两个规则结束。
![image](https://www.infopulse.com/files/2015/08/timsort-algorythm-3rd-screenshot.png)
    
这样的条件设定，使得run的大小非常适合进行合并。例如：假定runs的大小依次为128、64、32、16、8、4、2和2（先忽略run.size ≥ minrun.size的要求）。在这种情况下，所有runs入栈过程中不需要任何合并，只需要依次入栈，最后将栈中元素执行7次均衡的归并即可。
## 步骤3：runs归并
在步骤2中，我们将两个runs合并为一个有序run。连续两个runs合并的时候，我们使用了额外的内存。

1. 创建一个临时temporary run，其大小为runs中较小的那个run。 
2. 将较短的run复制到temporary run。    
3. 分别从当前较长run的第一个元素以及temporary run的第一个元素，开始进行比较操作。
4. 接下来，依次比较较长run和temporary中的元素，然后将较小的元素写进新的排序数组中。
5. 重复步骤4，直到较长run或者是temporary中某一个数组为空。
6. 将剩余run的所有元素添加到新run的末尾。
![image](https://www.infopulse.com/files/2015/08/timsort-algorythm-4th-screenshot.png)   
## 步骤4：对合并排序的修改
步骤3的归并排序看起来貌似很完美。然而有一个例外：我们假设对以下两个数组进行归并：

A = {1,2,3，...，9999,10000}

B = {20000，20001，…。，29999，30000}

用刚才的步骤3也可以进行合并排序，但这意味着每一个元素的比较和移动，都需要和原数组进行10000次比较和10000次移动。Timsort为此提供了一种称为“galloping”的模式。galloping步骤如下：
1. 按照步骤3的流程，分别从两个runs的元素开始进行合并排序。
2. 当元素从较长run或者 temporary run取出移动时，我们会记录这个元素是来自哪个run的。
3. 假设一连续的元素都是来自同一个run（在这个案例中，我们限定连续的个数为7），那么我们就认定下一个移动元素很有可能也是来自同一个run。所以，我们就可以切换到galloping模式。找下一个移动元素的时候，我们可以通过二分搜索（每一个run都是有序的）定位移动元素，从而提高查找的效率。二分搜索的效率远高于线性查找。
4. 最后，我们可以忽略下个移动元素之后的数据（或者当前run已经结束），批量进行元素的移动（与移动单独的元素相比，效率更高）。
    
这个解释可能有点含糊，让我们看一下示例：A = {1、2、3 ，…，9999、10000 } B = {20000、20001，....，29999、30000}   
1. 在前7个迭代中，将run A的数字1、2、3、4、5、6和7与数字20000进行比较，发现20000之后，数字都比20000大。
2. 从下一次迭代开始，开启galloping模式：将20000与run A中的编号8、10、14、22、38，n + 2 ^ i，…，10000依次进行比较。这样的比较次数将远远少于10000。
3. 当run A为空时，我们就知道当前剩余元素都比run B小（当然也可能在中间停了下来），那么来自run A的数据直接并入合并数组末尾即可。    

> [原文链接](https://www.infopulse.com/blog/timsort-sorting-algorithm/)
