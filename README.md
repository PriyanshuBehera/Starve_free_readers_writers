## Starve_free_readers_writers 
Pseudocode for starve free readers writers problem
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
    wait(wrt); //waiting if at present some reader is reading
    
    //signal(turn) can be written here too as any process that will be removed from turn semaphore's list 
    // will wait on wrt as wrt will still not be released by writing process 

    /*writing is performed*/
    /*critical section*/

    signal(turn);
    signal(wrt);
    
```
