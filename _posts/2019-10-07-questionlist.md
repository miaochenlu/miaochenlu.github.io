---
title: "Question List"
tags: question
key: page-Q

---



# Q1

baseband基带和passband通带的区别是啥

<br/>

# Q2

What is the minimum bandwidth needed to achieve a data rate of B bits/sec if the signal is transmitted using NRZ and Manchester encoding? Explain your answer.

#### Answer

Bandwith:H 

- NRZ

  一个时钟周期可以传输2bits, 所以$H=B/2$

- Manchester encoding

  一个时钟周期只能传输1bit,所以$H=B$

我的问题是：Manchester encoding的离散等级

$$dataRate=2Hlog_2V$$

$$H=B\Rightarrow V=\sqrt{2}$$？



疑问解答：

> $log_2V$这个式子是用来表示一个symbol代表了多少bits




