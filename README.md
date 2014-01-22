Oving2
======

Oving2

Go

package main

import (
    . "fmt" // Using '.' to avoid prefixing functions with their package names
    . "runtime" // This is probably not a good idea for large projects...
    . "time"
)

var i = 0

func adder(c chan int) {
	for x := 0; x < 1000000; x++ {
		<- c
		i++
		c <- 1
		}
	}

func subtract(c chan int) {
	for x := 0; x < 1000000; x++ {
		<- c
		i--
		c <- 1
		}
	}


func main() {
	GOMAXPROCS(NumCPU()) // I guess this is a hint to what GOMAXPROCS does...
	c := make(chan int,1)
	c <- 1	
	go adder(c) // This spawns adder() as a goroutine
	go subtract(c)
	for x := 0; x < 50; x++ {
		Println(i)
	}
    // No way to wait for the completion of a goroutine (without additional syncronization)
    // Well come back to using channels in Exercise 2. For now: Sleep
	Sleep(1000*Millisecond)
	Println("Done:", i);
}


Python

# Python 3.3.3 and 2.7.6
# python helloworld_python.py

from threading import Thread, Lock

i = 0
lock = Lock()


def adder():
# In Python you import a global variable, instead of exporting it when you declare it
	
    	global i
    
    
# In Python 2 this generates a list of integers (which takes time),
# while in Python 3 this is an iterable (which is much faster to generate).
# In python 2, an iterable is created with xrange()

    	for x in range(0, 1000000):
		lock.acquire()
        	i += 1
		lock.release()

        
def subtractor():
	
	global i
	for x in range(0,1000000):
		lock.acquire()
		i -= 1
		lock.release()



def main():
	    adder_thr = Thread(target = adder)
	    subtractor_thr = Thread(target = subtractor)
	    adder_thr.start()    
	    subtractor_thr.start()
	    
	    for x in range(0, 50):
		print(i)
	    adder_thr.join()
	    subtractor_thr.join()
	    
	    print("Done: " + str(i))


main()


C

#include <pthread.h>
#include <stdio.h>

int i = 0;
pthread_mutex_t lock;

// Note the return type: void*
void* adder(){
	
	for(int x = 0; x < 1000000; x++){
		pthread_mutex_lock(&lock);
        	i++;
		pthread_mutex_unlock(&lock);
	}
	return NULL;
}

void* subtract(){
	for(int x = 0; x < 1000000; x++){
		pthread_mutex_lock(&lock);
		i--;
		pthread_mutex_unlock(&lock);
	}
	return NULL;
}


int main(){
	pthread_mutex_init(& lock, NULL);
	pthread_t adder_thr;
	pthread_t subtract_thr;
	pthread_create(&adder_thr, NULL, adder, NULL);
	pthread_create(&subtract_thr, NULL, subtract, NULL);
	for(int x = 0; x < 50; x++){
		printf("%i\n", i);
	}

    
	pthread_join(adder_thr, NULL);
	pthread_join(subtract_thr, NULL);
	printf("Done: %i\n", i);
	pthread_mutex_destroy(&lock);
	return 0;    
}
