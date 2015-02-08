---
title: about netif_rx 
date: '2015-02-08'
description: 关于netif_rx的代码
categories:
- binary
tags:
- linux
- network
---

由于历史原因，在之前的项目中，用的network stack一直是自研的，和linux原生的stack完全不兼容。渐渐的这种不兼容的痛已经透彻全身，一直想去动但是又动弹不得。马上又要研发新品了，这回觉得是该动一动了。于是开始学习linux原生的做法（之前搞了这么久其实不熟悉linux的实现。。），完全替换不可能，但是融合一下总可以把！

关键字：netif_rx, RPS, backlog, softirq, SMP

netif_rx的过程就不梳理了，只对自己的疑问和找到的答案做个记录。

### netif_rx 把skb怎么了？ ###

在RPS宏打开时，首先`get_rps_cpu`根据skb选择处理的core，然后`enqueue_to_backlog`把skb放到core对应的skb queue中，并且置位softirq，调度处理之。

### backlog的处理是怎么调度的？ ###

关于`net_rx_action`的softirq是怎么激活的，找了半天最后其实就在眼皮底下的`enqueue_to_backlog`里。写法不够直观，所以看了几遍才理解了。

``` c
static int enqueue_to_backlog(struct sk_buff *skb, int cpu,
			      unsigned int *qtail)
{
	struct softnet_data *sd;
	unsigned long flags;

	sd = &per_cpu(softnet_data, cpu);

	local_irq_save(flags);

	rps_lock(sd);
	if (skb_queue_len(&sd->input_pkt_queue) <= netdev_max_backlog) {
		if (skb_queue_len(&sd->input_pkt_queue)) { // ----- 队列是空的时候才会从这里跳到激活，否则只是做入队操作
enqueue:
			__skb_queue_tail(&sd->input_pkt_queue, skb);
			input_queue_tail_incr_save(sd, qtail);
			rps_unlock(sd);
			local_irq_restore(flags);
			return NET_RX_SUCCESS;
		}

		/* Schedule NAPI for backlog device
		 * We can use non atomic operation since we own the queue lock
		 */
		if (!__test_and_set_bit(NAPI_STATE_SCHED, &sd->backlog.state)) {
			if (!rps_ipi_queued(sd))  // ----- 这里置位，激活softirq
				____napi_schedule(sd, &sd->backlog);
		}
		goto enqueue;
	}

	sd->dropped++;
	rps_unlock(sd);

	local_irq_restore(flags);

	atomic_long_inc(&skb->dev->rx_dropped);
	kfree_skb(skb);
	return NET_RX_DROP;
}
```

也就是说在第一个skb入队前激活softirq，到softirq实际调度之前的时间里，入队的报文也会一并处理。这是为了提高性能做的处理，把报文聚合了。也就是说上行报文在每个core上会积攒到下一次softirq调度为止（按照以往经验，无线的话最多也就十几个报文把）。

顺便看了下 softirq 是怎么调度的。重点是`irq_exit`函数。每次IRQ处理函数结束后就会调用它，从而调度softirq。另外，softirq是预先定义好的10种，而tasklet是其中的两种，这个之前没去了解过。原来如此单纯啊，瞬间不高大上了。有个疑问是，如果没有中断上来，softirq的时效性如何保证啊？没有深究。。

### CONFIG_RPS 是什么？ ###

> Symbol: RPS [=y]  
>  Type  : boolean  
>  Prompt: RPS  
>    Defined at net/Kconfig:240  
>    Depends on: NET [=y] && SMP [=y] && SYSFS [=y] && USE_GENERIC_SMP_HELPERS [=y]  
>    Location:  
>      -> Networking support (NET [=y])  
>        -> Networking options

RPS 依赖于 SMP，多核下对报文流进行分配和加速的功能。通过hash将报文流分配到固定的core上，以提高多核的利用率，以及提高cache的命中率。

### SMP 下的 IRQ有什么区别？ ###
SMP下使用 APIC，可以配置irq产生到特定core的几率。`cat /proc/interrupts` 可以看到每个终端在各个core上的次数：

    $ cat /proc/interrupts 
               CPU0       CPU1       CPU2       CPU3       
      0:         50          0          0          0   IO-APIC-edge      timer
      1:      30098          0          0          0   IO-APIC-edge      i8042
      8:          0          0          0          0   IO-APIC-edge      rtc0
      9:          0          0          0          0   IO-APIC-fasteoi   acpi
     12:       8113          0          0          0   IO-APIC-edge      i8042
     14:          0          0          0          0   IO-APIC-edge      ata_piix
     15:      50474          0          0          0   IO-APIC-edge      ata_piix
     16:         71          0          0      33906   IO-APIC-fasteoi   eth1
     17:      25732          0          0          0   IO-APIC-fasteoi   eth2
     19:         98          0      63721          0   IO-APIC-fasteoi   eth0

如何控制 IRQ亲和性，`IRQ Affinity`，网上有很多资料。
