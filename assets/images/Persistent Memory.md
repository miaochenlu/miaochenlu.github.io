Undo+redo



### **Persistent Memory**



### A. Persistent Memory Write-order Control

* paragraph1

>  introduce write-order control

* paragraph2

> the problems logging faces

* paragraph3

> write order control method problems: clwb & memory barrier

### B. Why Undo+Redo Logging

* paragraph1

state the reason briefly

* part1: **Logging in persistent memory**: introduce undo and redo logging

* part2: **Benefits of undo+redo logging**
  * undo 

### C. Why Undo+Redo Logging in hardware

* software logging introduces extra instructions in the CPU pipeline
* increased NVRAM traffic
* conservative cache forced write-back
* risks of data persistence in multithreading





DHTM

### A. Hardware Transtional memory

* 
* commercial HTMs



I have read your paper "[Steal but No Force: Efficient Hardware Undo+Redo Logging for Persistent Memory Systems](http://cseweb.ucsd.edu/~jzhao/files/steal-no-force-hpca2018.pdf) ". I think it's brilliant but natural to use undo+redo logging in HTM.



