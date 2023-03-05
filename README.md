## Starve_free_readers_writers 
Pseudocode for starve free readers writers problem

## Proposed Solution
I assumed the struct semaphore to support a FIFO queue to maitain the list of waiting processes. Every proecss will first wait on turn semaphore and each will get executed in the order they come.

``` cpp
// Pseudocode :

semaphore turn = 1,wr_mutex = 1,rd_mutex = 1;
int read_cnt = 0;

//structure of reader's process:

    wait(turn);  //waiting for its turn for execution
    wait(rd_mutex); //ensuring only one read process gets the chance to update read_cnt
    read_cnt++;
    if(rd_cnt==1){
        wait(wr_mutex); //first reader will ask for resource access for readers
    }
    signal(rd_mutex); //releasing access for updation of read count
    
    /*reading is performed*/

    wait(rd_mutex); //ensuring only one read process gets the chance to update read_cnt
    read_cnt--;
    if(rd_cnt==0){
        signal(wr_mutex); //freeing the resources for writers to write
    }
    signal(rd_mutex);   //releasing access for updation of read count


//structure of writers process:

    wait(turn); //waiting for its turn for execution
    wait(wr_mutex); //waiting if at present some reader is reading
    
    //signal(turn) can be written here too as any process that will be removed from turn semaphore's list 
    // will wait on wr_mutex as wr_mutex will still not be released by the writing process 

    /*writing is performed*/
    /*critical section*/

    signal(turn); //Signalling the next reader or writer process in turn queue to wakeup
    signal(wr_mutex); //releasing the resource for the other reader or writer process
    
```
## Criterias met by the pseudocode to serve as a solution to the critical section problem

### Mutual Exclusion
The rw_mutex ensures that only one reader process gets the chance to update the read count variable and the wr_mutex ensures that only one category(either reader or the writer process) get the chance to acess the resources or the critical section.
### Progress
As any process that wants to enter into the critical section gets into the queue maintained by turn semahphore instead of some other process setting a turn variable to that process, progress is ensured as any process if in critical section has to the signal the turn variable upon leaving. 
### Bounded Waiting
Since the turn semaphore maintains a FIFO queue it ensures that whichever process comes first is executed first thus bounding the time a process needs to wait before execution

## Deadlock
Also it can be seen that there is no condition of cyclic waiting in this case so no deadlock can occur.

## Reference
- Abraham Silberscahtz, Peter B Galvin ,Gerg Gagne - Operating System Concepts
- [Wikipedia](https://en.wikipedia.org/wiki/Readers%E2%80%93writers_problem)
