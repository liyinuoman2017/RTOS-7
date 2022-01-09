# RTOS-7

**嵌入式实时操作系统7——任务优先级表**

## 1.任务优先级表用途
优先级表用于指示在对应的优先级下是否存在就绪的任务，操作系统内核总是在优先级表中的最高优先级中选择任务执行，就绪表是动态更新的。
举个例子让大家理解一下任务优先级表：驾校学车。驾校里有车，教练，学员，我们假设驾校教练只有一辆车，进入车内练习的人就是处在运行状态，在旁边排队等待的学员就处在就绪状态。由于报名费不一样，学员分为VIP等级和普通等级，教练让学员上车时优先考虑VIP等级学员。

![在这里插入图片描述](https://img-blog.csdnimg.cn/be73b561e2524f64a82e8fa09e84628f.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGl5aW51bzIwMTc=,size_19,color_FFFFFF,t_70,g_se,x_16)

教练为学员做了一张排队表：
![在这里插入图片描述](https://img-blog.csdnimg.cn/27324664918d4cd79449711a23f33d34.png)
教练首先判断是否有VIP等级学员，当有VIP等级学员时，等级表中的VIP等级数值为1，教练会在VIP学员中选择一个让其上车练习，**只有当VIP等级学员都练完放弃练习了**，等级表中的VIP等级数值为0。教练才会判断是否有普通等级学员，当有普通等级学员时，等级表中的普通等级数值为1，教练会在普通等级学员中选择一个让其上车练习。

![在这里插入图片描述](https://img-blog.csdnimg.cn/f228e69b0438442988e2e75669696dc8.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/43d97fca0a0c4a799f8516d243838ae9.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGl5aW51bzIwMTc=,size_17,color_FFFFFF,t_70,g_se,x_16)

> 根据描述我们得出优先级表有两个特点：
>  1、**优先级表指示在对应的优先级下是否存在就绪的任务。**
> 2、**任务状态会动态更新，优先级表也会随之动态更新。**

## 2.任务优先级表的实现

任务优先级表用于指示在对应的优先级下是否存在就绪的任务，优先级表每一个元素用于指示对应的优先级下是否有就绪的任务。
因此使用静态数组的方法实现一个任务优先级表，C语言实现如下：

![在这里插入图片描述](https://img-blog.csdnimg.cn/fdc8b51ae2d74fb78c012d9031dccad7.png)

任务优先级表和任务的关系框图如下：

![在这里插入图片描述](https://img-blog.csdnimg.cn/1a9b6fc0be394ad78eb9612c843d6e3b.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGl5aW51bzIwMTc=,size_19,color_FFFFFF,t_70,g_se,x_16)

任务优先级表的数组中下标最大的元素task_priority_table[15]代表最高优先级,task_priority_table[0]代表最低优先级，任务优先级表有以下三个操作：
1、优先级置1。
2、优先级清0。
3、查找最高优先级。

C语言实现优先级置1：

![在这里插入图片描述](https://img-blog.csdnimg.cn/8adccd6fb9cb484cba1ac9da455b0fb1.png)
C语言实现优先级清0：

![在这里插入图片描述](https://img-blog.csdnimg.cn/147f8aded30a49e48c6a7e5c5a695bff.png)

C语言实现查找最高优先级：

![在这里插入图片描述](https://img-blog.csdnimg.cn/4bcd2fdbea30499faa415f75c467dfca.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGl5aW51bzIwMTc=,size_19,color_FFFFFF,t_70,g_se,x_16)

使用**静态数组**的方法可以实现一个就绪表，有没有更高效的方法呢？
答案时有的，使用**BIT位表**的方法可以实现一个优先级表，并且更加高效，C语言实现如下：

![在这里插入图片描述](https://img-blog.csdnimg.cn/80a3eb4eaee34ee9bcc16a7a0e8f5630.png)

一个u16类型的数据包含16个bit位，每个bit位可以对应一个优先级，设最高bit位（第15位）为最高优先级，最低bit位（第0位）为最低优先级，任务优先级表和任务的关系框图如下：

![在这里插入图片描述](https://img-blog.csdnimg.cn/c0fdd76d17cb46aba15c72d114d88dab.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGl5aW51bzIwMTc=,size_19,color_FFFFFF,t_70,g_se,x_16)

C语言实现优先级置1：

![在这里插入图片描述](https://img-blog.csdnimg.cn/6207df0a48ca4ad9ab4631b62865a420.png)

C语言实现优先级置0：

![在这里插入图片描述](https://img-blog.csdnimg.cn/713720b8e21a4aaabc34ea894cd648ab.png)

刚刚提到了BIT位表的方法更加高效，这种方法的高效体现再查找最高优先级操作中，有一些CPU支持计算前导零数量指令CLZ,使用计算前导零数量指令CLZ可以加速查找最高优先级操作中，实现查找最高优先级：

![在这里插入图片描述](https://img-blog.csdnimg.cn/94a84e60f57b46ee9fbad9ead9abca71.png)

由于查找最高任务优先级的操作在操作系统内核操作中非常频繁，因此使用**BIT位表和计算前导零数量指令的组合可以提高运行效率**。


## 3.FreeRTOS任务优先级表分析
FreeRTOS源码中与任务优先级表相关的代码如下：

![在这里插入图片描述](https://img-blog.csdnimg.cn/2c47c43ac59a43f48398fed9323c4485.png)

FreeRTOS中使用一个32位的uxTopReadyPriority作为**任务优先级BIT位表**。uxTopReadyPriority中每一个bit位对应一个优先级，最高bit位（第31位）为最高优先级，最低bit位（第0位）为最低优先级。每一个bit位为1时代表该任务优先级中有就绪任务，bit位为0时代表该任务优先级中无就绪任务。

uxTopReadyPriority任务优先级表有三个操作：优先级置1，优先级清0，查找最高优先级。

![在这里插入图片描述](https://img-blog.csdnimg.cn/b9d7bc8254d648499f83f9711e7ebb20.png)

```c
portRESET_READY_PRIORITY 
portRECORD_READY_PRIORITY 
portGET_HIGHEST_PRIORITY
```
其中查找最高优先级使用了_clz计算前导零数量指令。

**优先级置1策略**
当有任务加入就绪表时，将相应的bit位置1，不用考虑该优先级下的就绪任务总数量，因为加入一个任务后，该优先级下的就绪任务总数量必然大于等于1。
优先级置1的函数调用流程如下：

```c
prvAddTaskToReadyList -> 
taskRECORD_READY_PRIORITY -> 
portRECORD_READY_PRIORITY
```
**优先级清0策略**
当有任务从就绪表移除时，需要先将该优先级下的就绪任务总数量减一，如果该优先级下的就绪任务总数量为0时，此时需要将该优先级清0。代码如下：

```c
/*	就绪表中移除当前任务 */
if( uxListRemove( &( pxCurrentTCB->xStateListItem ) ) == ( UBaseType_t ) 0 )
{
	/*	优先级表对应位清0 */
	portRESET_READY_PRIORITY( pxCurrentTCB->uxPriority, uxTopReadyPriority ); 
else
{
	mtCOVERAGE_TEST_MARKER();
}
```
**查找最高优先级**
当需要进行任务切换时，需要查找最高优先级，函数调用流程如下：

```c
taskSELECT_HIGHEST_PRIORITY_TASK ->portGET_HIGHEST_PRIORITY
```
FreeRTOS任务优先级表和就绪表的关系框图如下：

![在这里插入图片描述](https://img-blog.csdnimg.cn/63f3fce282474f71b61c484417b1122d.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGl5aW51bzIwMTc=,size_19,color_FFFFFF,t_70,g_se,x_16)

## 4.任务优先级表的更新
前文描述了任务优先级表的三种操作：优先级置1，优先级清0，查找最高优先级。那么这里就产生了以下问题：
哪些情况下会对任务优先级表进行操作？任务优先级表何时更新？
参考FreeRTOS源码，优先级表的操作更新点如下（清0操作会判断该优先级下的就绪任务总数量）：


**xTaskCreate**
xTaskCreate的作用是创建一个任务，该系统函数的调用流程如下：

```c
xTaskCreate ->
prvAddNewTaskToReadyList  -> 
prvAddTaskToReadyList  ->
taskRECORD_READY_PRIORITY  ->
 portRECORD_READY_PRIORITY
```

就绪表变化：任务插入到就绪表对应优先级列表的尾部，对应的优先级位置1。



**vTaskDelete**
vTaskDelete的作用是删除一个任务，该系统函数的调用流程如下：

```c
vTaskDelete -> 
taskRESET_READY_PRIORITY -> 
portRESET_READY_PRIORITY
```

就绪表变化：任务从就绪表对应优先级列表中移除，对应的优先级位清0。


**vTaskSuspend**
vTaskSuspend的作用是暂停一个任务，该系统函数的调用流程如下：

```c
vTaskSuspend -> 
taskRESET_READY_PRIORITY-> 
portRESET_READY_PRIORITY
```

就绪表变化：任务从对应优先级列表中移除，对应的优先级位清0。

**vTaskResume**
vTaskResume的作用是恢复一个任务，该系统函数的调用流程如下：

```c
vTaskResume -> 
prvAddTaskToReadyList -> 
portRECORD_READY_PRIORITY
```

就绪表变化：任务插入到就绪表中对应优先级列表的尾部，对应的优先级位置1。

**vTaskPrioritySet**
vTaskPrioritySet的作用是改变一个任务优先级，该系统函数的调用流程如下：

```c
vTaskPrioritySet ->
uxListRemove -> 
prvAddTaskToReadyList -> 
portRECORD_READY_PRIORITY
```

就绪表变化：任务从就绪表中的当前优先级列表中移除，并将任务插入到设定的优先级列表的尾部，对应的优先级位置1。

**vTaskDelay**
vTaskDelay的作用是将当前任务从就绪表中移动到等待表中，该系统函数的调用流程如下：

```c
vTaskDelay -> 
taskRESET_READY_PRIORITY -> 
portRESET_READY_PRIORITY
```

就绪表变化：任务从就绪表中移除，并将任务插入到等待表中，对应的优先级位清0。

**xQueueSemaphoreTake**
xQueueSemaphoreTake的作用是当前任务等待一个信号，该系统函数的调用流程如下：

```c
xQueueSemaphoreTake ->  
vListInsert -> 
prvAddCurrentTaskToDelayedList -> 
portRESET_READY_PRIORITY
```

就绪表变化：将当前任务插入到挂起中，并当前任务从就绪表中移除，最后将当前任务插入等待表中，对应的优先级位清0。

**xQueueGenericSend**
xQueueSemaphoreTake的作用是发生一个信号，该系统函数的调用流程如下：

```c
xQueueGenericSend -> 
xTaskRemoveFromEventList-> 
uxListRemove -> 
prvAddTaskToReadyList -> 
portRECORD_READY_PRIORITY
```

就绪表变化：将移除挂起表中的第一个任务，并该任务插入到就绪表中中对应优先级列表的尾部，对应的优先级位置1。

## 5.源码

```c
typedef unsigned long UBaseType_t;

	PRIVILEGED_DATA static volatile UBaseType_t uxTopReadyPriority 		= tskIDLE_PRIORITY;  

	/* Remove the task from the ready list before adding it to the blocked list
	as the same list item is used for both lists. */
	if( uxListRemove( &( pxCurrentTCB->xStateListItem ) ) == ( UBaseType_t ) 0 )
	{
		/* The current task must be in a ready list, so there is no need to
		check, and the port reset macro can be called directly. */
		portRESET_READY_PRIORITY( pxCurrentTCB->uxPriority, uxTopReadyPriority ); /*lint !e931 pxCurrentTCB cannot change as it is the calling task.  pxCurrentTCB->uxPriority and uxTopReadyPriority cannot change as called with scheduler suspended or in a critical section. */
	}
	else
	{
		mtCOVERAGE_TEST_MARKER();
	}

	#define portRECORD_READY_PRIORITY( uxPriority, uxReadyPriorities ) ( uxReadyPriorities ) |= ( 1UL << ( uxPriority ) )
	#define portRESET_READY_PRIORITY( uxPriority, uxReadyPriorities ) ( uxReadyPriorities ) &= ~( 1UL << ( uxPriority ) )

	/*-----------------------------------------------------------*/

	#define portGET_HIGHEST_PRIORITY( uxTopPriority, uxReadyPriorities ) uxTopPriority = ( 31UL - ( uint32_t ) __clz( ( uxReadyPriorities ) ) )
```

> <font color=red>**未完待续…

  实时操作系统系列将持续更新
  
创作不易希望朋友们点赞，转发，评论，关注。
  
您的点赞，转发，评论，关注将是我持续更新的动力
  
作者：李巍
  
Github：liyinuoman2017**
  
