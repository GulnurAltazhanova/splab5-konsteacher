# Lab5 - race condition and critical section


### Q1

```
./x86.py -p loop.s -t 1 -i 100 -R dx
...
   dx          Thread 0         
    0   
   -1   1000 sub  $1,%dx
   -1   1001 test $0,%dx
   -1   1002 jgte .top
   -1   1003 halt
```

---
1. __Can you figure out what the value of `%dx` will be during the run?__  
_`%dx` will be `-1`._

### Q2
```
./x86.py -p loop.s -t 2 -i 100 -a dx=3,dx=3 -R dx -c
...
   dx          Thread 0                Thread 1         
    3   
    2   1000 sub  $1,%dx
    2   1001 test $0,%dx
    2   1002 jgte .top
    1   1000 sub  $1,%dx
    1   1001 test $0,%dx
    1   1002 jgte .top
    0   1000 sub  $1,%dx
    0   1001 test $0,%dx
    0   1002 jgte .top
   -1   1000 sub  $1,%dx
   -1   1001 test $0,%dx
   -1   1002 jgte .top
   -1   1003 halt
    3   ----- Halt;Switch -----  ----- Halt;Switch -----  
    2                            1000 sub  $1,%dx
    2                            1001 test $0,%dx
    2                            1002 jgte .top
    1                            1000 sub  $1,%dx
    1                            1001 test $0,%dx
    1                            1002 jgte .top
    0                            1000 sub  $1,%dx
    0                            1001 test $0,%dx
    0                            1002 jgte .top
   -1                            1000 sub  $1,%dx
   -1                            1001 test $0,%dx
   -1                            1002 jgte .top
   -1                            1003 halt
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
   dx          Thread 0                Thread 1         
    3   
    2   1000 sub  $1,%dx
    2   1001 test $0,%dx
    2   1002 jgte .top
    3   ------ Interrupt ------  ------ Interrupt ------  
    2                            1000 sub  $1,%dx
    2                            1001 test $0,%dx
    2                            1002 jgte .top
    2   ------ Interrupt ------  ------ Interrupt ------  
    1   1000 sub  $1,%dx
    1   1001 test $0,%dx
    1   1002 jgte .top
    2   ------ Interrupt ------  ------ Interrupt ------  
    1                            1000 sub  $1,%dx
    1                            1001 test $0,%dx
    1                            1002 jgte .top
    1   ------ Interrupt ------  ------ Interrupt ------  
    0   1000 sub  $1,%dx
    0   1001 test $0,%dx
    0   1002 jgte .top
    1   ------ Interrupt ------  ------ Interrupt ------  
    0                            1000 sub  $1,%dx
    0                            1001 test $0,%dx
    0                            1002 jgte .top
    0   ------ Interrupt ------  ------ Interrupt ------  
   -1   1000 sub  $1,%dx
   -1   1001 test $0,%dx
   -1   1002 jgte .top
    0   ------ Interrupt ------  ------ Interrupt ------  
   -1                            1000 sub  $1,%dx
   -1                            1001 test $0,%dx
   -1                            1002 jgte .top
   -1   ------ Interrupt ------  ------ Interrupt ------  
   -1   1003 halt
   -1   ----- Halt;Switch -----  ----- Halt;Switch -----  
   -1                            1003 halt
```

This makes the interrupt interval quite small and random; use different seeds with `-s` to see different interleavings.

---
1. __Does the frequency of interruption change anything about this program?__   
_Frequency affect the interleavings, i.e. the frequency of interleavings, but not the result, because the two threads don't have_  ___critical sections.___ Also the amount of interrupts is different. The first program contains 11 inyerapt and the second programm contains 13 interrupts.

### Q4
```
./x86.py -p looping-race-nolock.s -t 1 -M 2000
...
 2000          Thread 0         
    0   
    0   1000 mov 2000, %ax
    0   1001 add $1, %ax
    1   1002 mov %ax, 2000
    1   1003 sub  $1, %bx
    1   1004 test $0, %bx
    1   1005 jgt .top
    1   1006 halt
                        1003 halt
```
---
### Q5
```
./x86.py -p looping-race-nolock.s -t 2 -a bx=3 -M 2000
...
```
---
1. __Do you understand why the code in each thread loops three times?__  
_Yes I understand why each thread loops three times, because after each loob minus from %bx 1. In the begining %bx is equal 3._

2. __What will the final value of x be?__  
_The final value of x is 6, because each thread share data to the same register_

### Q6
```
./x86.py -p looping-race-nolock.s -t 2 -M 2000 -i 4 -r -s 0
...
```

---
1. __Can you tell, just by looking at the thread interleaving, what the final value of x will be?__  
_The final value of x is equal 2._

2. __Does the exact location of the interrupt matter?__  
_No, because OS own make interrupts_

3. __Where can it safely occur?__  
_When thread make move the memory to register and stores content into memory in the same step_

4. __Where does an interrupt cause trouble?__  
_When the thread interrupt on the movement memory to register and after adding registers_

5. __In other words, where is the critical section exactly__  
_In other words critical section exactly when threat interrupted without movement result to memory_

### Q7
```
./x86.py -p looping-race-nolock.s -a bx=1 -t 2 -M 2000 -i 1
...
```

---
1. __What about when you change -i 2, -i 3, etc.?__  
_When i changet -i 2, -i 3 I saw that there is diferents values of x, because thread make movement register to the memory before interrupt and ntxt threat take this value after first thread._

2. __For which interrupt intervals does the program give the “correct” final answer?__  
_The "correct final answers program give with -i 3"_

### Q8
```
./x86.py -p looping-race-nolock.s -a bx=1 -t 2 -M 2000 -i 1
...
```
---
1. __What interrupt intervals, set with the -i flag, lead to a “correct” outcome?__  
_I set -i 3 and lead to correct answers with two threads it will be 200. And the same result i achievemt when set i number is divide by 3_

2. __Which intervals lead to surprising results?__  
_The diferents intervals give me the other results, the i 1 and i 2 give me the result 100 it like only one thread works"_


### Q9
```
./x86.py -p wait-for-me.s -a ax=1,ax=0 -R ax -M 2000
...
```
---
1. __How should the code behave?__  
_The thread 0 test the %ax register with 1 next check the equal expression and next move the 1 to the memory. The thread 1 test %ax register and 0 next check the equal expression next move the memory 2000 to the another register %cx next test the %cx if the value is not equal. The program load in one memory 2000_

2. __How is value at location 2000 being used by the threads?__  
_The thread 0 move $1 to the memory 2000. The thread 1 loads from memory 2000 to the register %cx"_

### Q10
```
./x86.py -p wait-for-me.s -a ax=0,ax=1 -R ax -M 2000
...
```
---
1. __How do the threads behave?__  
_Multiple threads don't affect our calculations because there is no race condtion.The thread 1 compare register %ax and 1 then check equal condition and move 1 to the memory 2000_

2. __What is thread 0 doing?__  
_The thread 0 load memory to register %cx. Compared value and register %cx, jumped if value is not equal._

3. __How would changing the interrupt interval (e.g., -i 1000, or perhaps to use random intervals) change the trace outcome?__  
_Trace outcome is not changing_

4. __Is the program efficiently using the CPU?__  
_No, using register and memory_

