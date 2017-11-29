# Lab5 - race condition and critical section

This lab is the homework of [Chapter 26](http://www.cs.wisc.edu/~remzi/OSTEP/threads-intro.pdf)

Format your answers neatly and submit.

### Q1

```
./x86.py -p loop.s -t 1 -i 100 -R dx
...
dx Thread 0
0
-1 1000 sub $1,%dx
-1 1001 test $0,%dx
-1 1002 jgte .top
-1 1003 halt
```

---
1. __Can you figure out what the value of `%dx` will be during the run?__
_`%dx` will be `-1`._

### Q2
```
./x86.py -p loop.s -t 2 -i 100 -a dx=3,dx=3 -R dx -c
...
dx Thread 0 Thread 1
3
2 1000 sub $1,%dx
2 1001 test $0,%dx
2 1002 jgte .top
1 1000 sub $1,%dx
1 1001 test $0,%dx
1 1002 jgte .top
0 1000 sub $1,%dx
0 1001 test $0,%dx
0 1002 jgte .top
-1 1000 sub $1,%dx
-1 1001 test $0,%dx
-1 1002 jgte .top
-1 1003 halt
3 —-— Halt;Switch —-— —-— Halt;Switch —-—
2 1000 sub $1,%dx
2 1001 test $0,%dx
2 1002 jgte .top
1 1000 sub $1,%dx
1 1001 test $0,%dx
1 1002 jgte .top
0 1000 sub $1,%dx
0 1001 test $0,%dx
0 1002 jgte .top
-1 1000 sub $1,%dx
-1 1001 test $0,%dx
-1 1002 jgte .top
-1 1003 halt
```
---
1. __What values will `%dx` see? Run with the `-c` flag to see the answers.__
_`%dx` decrements by 1 after each iteration, until it get equal `-1`._

2. __Does the presence of multiple threads affect anything about your calculations? Is there a race condition in this code?__
_Multiple threads don't affect our calculations because there is no race condtion. The threads are executed without interleavings because the threads complete before an interrupt occurs._

### Q3

```
./x86.py -p loop.s -t 2 -i 3 -r -a dx=3,dx=3 -R dx
...
dx Thread 0 Thread 1
3
2 1000 sub $1,%dx
2 1001 test $0,%dx
2 1002 jgte .top
3 —--— Interrupt —--— —--— Interrupt —--—
2 1000 sub $1,%dx
2 1001 test $0,%dx
2 1002 jgte .top
2 —--— Interrupt —--— —--— Interrupt —--—
1 1000 sub $1,%dx
1 1001 test $0,%dx
1 1002 jgte .top
2 —--— Interrupt —--— —--— Interrupt —--—
1 1000 sub $1,%dx
1 1001 test $0,%dx
1 1002 jgte .top
1 —--— Interrupt —--— —--— Interrupt —--—
0 1000 sub $1,%dx
0 1001 test $0,%dx
0 1002 jgte .top
1 —--— Interrupt —--— —--— Interrupt —--—
0 1000 sub $1,%dx
0 1001 test $0,%dx
0 1002 jgte .top
0 —--— Interrupt —--— —--— Interrupt —--—
-1 1000 sub $1,%dx
-1 1001 test $0,%dx
-1 1002 jgte .top
0 —--— Interrupt —--— —--— Interrupt —--—
-1 1000 sub $1,%dx
-1 1001 test $0,%dx
-1 1002 jgte .top
-1 —--— Interrupt —--— —--— Interrupt —--—
-1 1003 halt
-1 —-— Halt;Switch —-— —-— Halt;Switch —-—
-1 1003 halt
```

This makes the interrupt interval quite small and random; use different seeds with `-s` to see different interleavings.

---
1. __Does the frequency of interruption change anything about this program?__
_Frequency affect the interleavings, i.e. the frequency of interleavings, but not the result, because the two threads don't have_ ___critical sections.___

### Q4

### Q5

### Q6

### Q7

### Q8

### Q9

### Q10

```
./x86.py -p wait-for-me.s -a ax=0,ax=1 -R ax -M 2000
...
```
---

1. __How do the threads behave?__

2. __What is thread 0 doing?__

3. __How would changing the interrupt interval (e.g., -i 1000, or perhaps to use random intervals) change the trace outcome?__

4. __Is the program efficiently using the CPU?__
