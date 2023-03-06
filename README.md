# Starve_free_readers_writers

## Problem discription
Classical problem of synchronization(Readers - Writers) where no category of process either reader or writer should starve.

## Proposed Solution
I assumed the struct semaphore to support a FIFO queue to maintain the list of waiting processes.Here, the turn semaphore is common to both reader and writer processes and any process coming will have to call wait(turn) ensuring every process is executed in the order they come. Also the mutex for resource access (res_mutex) is common to both reader and writer processes. Here reading can be performed in parallel but not writing and when a writer process is writing no reader process can read and vice-versa

# Pseudocode for starve free readers writers problem

``` cpp
// Pseudocode :
// Initializing the values of the three semaphores required in the solution
semaphore turn = 1,res_mutex = 1,rd_mutex = 1;

//initial value of reading processes
int read_cnt = 0;

//structure of reader's process:

    wait(turn);  //waiting for its turn for execution
    wait(rd_mutex); //ensuring only one reader process gets the chance to update read count variable
    read_cnt++;     //updating read count variable
    if(rd_cnt==1){
        wait(res_mutex); //first reader will ask for resource access for readers
    }
    signal(turn);       //Signalling the next reader or writer process in turn queue to wakeup if reader process it will wait on rd_mutex and if writer it will wait on
                        //res_mutex
    signal(rd_mutex); //releasing access for updation of read count
    
    /*reading is performed*/

    wait(rd_mutex); //ensuring only one read process gets the chance to update read count variable
    read_cnt--;     //updating read count variable
    if(rd_cnt==0){
        signal(res_mutex); //freeing the resources for writers to write if some writer process(es) is waiting on res_mutex after being removed from turn semaphore
    }
    signal(rd_mutex);   //releasing access for updation of read count


//structure of writers process:

    wait(turn); //waiting for its turn for execution
    wait(res_mutex); //waiting if at present some reader is reading
    
    //signal(turn) can be written here too as any process that will be removed from turn semaphore's list 
    // will wait on res_mutex as res_mutex wouldn't have been released by the writing process 

    /*writing is performed*/
    /*critical section*/

    signal(turn); //Signalling the next reader or writer process in turn queue to wakeup
    signal(res_mutex); //releasing the resource for the other reader or writer process
    
```
## Explanation of the pseudocode

Any process which comes have to call wait(turn) so they execute in the order they come and when that process completes it has to call signal(turn) giving chance to next process in the queue. Reader process trying to update the readcount variable should do it atomically. The rw_mutex ensures this. Now if there is a reader process it will ask for the exclusive rights of the resources through res_mutex for reader processes to read and when the reader processes in the queue (those came before any of writer process) will complete they will release the res_mutex for the writer process. Similarly,when the turn for writer process comes, it will acquire the resource access for itself and the writers processes before any reader process and signalling it when the writers process in the consecutive list ends.

## Criterias met by the pseudocode to serve as a solution to the critical section problem

### Mutual Exclusion
The rw_mutex ensures that only one reader process gets the chance to update the read count variable and the res_mutex ensures that only one category(either reader or the writer process) get the chance to acess the resources or the critical section.
### Progress
As any process that wants to enter into the critical section gets into the queue maintained by turn semahphore instead of some other process setting a turn variable for that process, progress is ensured, and also any process in critical section has to the signal the turn variable on leaving. 
### Bounded Waiting
Since the turn semaphore maintains a FIFO queue it ensures that whichever process comes first is executed first thus bounding the time a process needs to wait before execution

## Deadlock
Also it can be seen that there is no condition of cyclic waiting in this case so no deadlock can occur.

## Reference
- Abraham Silberscahtz, Peter B Galvin ,Gerg Gagne - Operating System Concepts
- [Wikipedia](https://en.wikipedia.org/wiki/Readers%E2%80%93writers_problem)
