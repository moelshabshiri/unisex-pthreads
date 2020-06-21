# Unisex Bathroom Synchronization Problem

Unisex bathroom problem solved using pthreads, mutex locks and condition variables. Written in C

## Synchronization: Mutex Locks

Mutex Locks were used in my code in order to prevent different threads from
accessing critical sections of the code at the same time. Their purpose in the code is to
avoid the mutual exclusion problem since this program is multi-threaded that happens
concurrently. Therefore mutexes are important so that only one thread can perform
modification on shared values, such as the values of men and women currently in the
bathroom.
There are two mutexes declared, one for each gender. This is because each
thread from each gender has access to the value of the number of people or threads
currently in the bathroom from their gender.
In the functions man_wants_to_enter() and woman_wants_to_enter(), the locks
exist so that each thread would be able to individually access the bathroom. This means
that each thread will be able to increment the bathroom variable without the variable
being simultaneously accessed by another thread. In the critical code there are also
constraints that have to be met therefore the thread has to wait, this will be discussed in
the next section.
Similarly, in the functions man_leaves() and woman_leaves(), what is in the
critical section is the decrimentation of the number of each gender in the bathroom
depending on the function. It is important to keep that part of the code in the critical
section so that one thread at a time can perform this function

## Synchronization: Conditional Variables
Conditional variables are used in the program so that one of the threads could be
blocked or wait until another thread signals it to continue. If the male gender threads are
taken as an example, in order for a man to enter the bathroom, there must be two
criterias that have to be met. One is that there are no females in the bathroom, the
second one is that there are no more than 2 men in there, assuming that there can be a
maximum of 3 people at a time in the bathroom. As a result conditional variables are
used in this case. When at least one of these criterias are not met, the thread is put on
hold until it gets a signal from another thread which would serve each criteria and
unblock the thread. One of the signals comes to male threads is when there is less than
3 men currently in the bathroom:
```
if (men < 3) pthread_cond_signal(&ManCon);
```
The other thread comes from the women_leave() function, so when one woman leaves
the bathroom, it checks whether or not there are any women left. If there aren't any
women left in the bathroom, a male thread that was kept on hold is unlocked and
signaled to continue:
```
if (women == 0)
{
pthread_mutex_lock(&ManMx);
pthread_cond_signal(&ManCon);
unblockedpthread_mutex_unlock(&ManMx);
}
```
