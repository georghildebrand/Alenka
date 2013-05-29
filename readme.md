## Welcome to Alenka - GPU database engine

### How to build?

Download [Alenka](https://github.com/antonmks/Alenka/archive/master.zip)

Download [CUDPP](http://code.google.com/p/cudpp) library 

Download [ModernGpu](https://github.com/NVlabs/moderngpu) library

Install CUDPP and ModernGPU into Alenka directory

Run Makefile or Makefile.win


### What is this?

This is a GPU based database engine written to use vector based processing and high bandwidth of modern GPUs

### Features :

  *  Vector-based processing  
    CUDA programming model allows a single operation to be applied to an entire set of data at once.
	
  * Smart compression  
    Ultra fast compression and decompression performed directly inside GPU.
	
  * Column-based storage  
    Minimize disk I/O by only accessing the relevant data.

  * Data skipping  
    Better performance without indexes.	

### How to use it ?

##### Step 1 - Load the data from text files into data files ( {n} indicates a position in a file)

`O := LOAD 'orders' BINARY AS (o_orderkey{1}:int, o_custkey{2}:int, o_orderdate{5}:int, o_shippriority{8}:int);`

` C := LOAD 'customer' BINARY AS (c_custkey{1}:int, c_mktsegment{7}:varchar(10));`

` L := LOAD 'lineitem' BINARY AS (l_orderkey{1}:int,  price{6}:decimal, discount{7}:decimal, shipdate{11}:int);`

##### Step 2 - Filter data

` OFI := FILTER O BY o_orderdate < 19950315;`

` CF := FILTER C BY c_mktsegment == "BUILDING";`

` LF := FILTER L BY shipdate > 19950315;`

##### Step 3 - Join data

` OLC := SELECT o_orderkey AS o_orderkey, o_orderdate AS o_orderdate,`
` o_shippriority AS o_shippriority, price AS price, discount AS discount`
` FROM LF JOIN OFI ON l_orderkey = o_orderkey `
` JOIN CF ON o_custkey = c_custkey;`

##### Step 4 - Group data


` F := SELECT o_orderkey AS o_orderkey1, o_orderdate AS orderdate1, `
`o_shippriority AS priority,  SUM(price*(1-discount)) AS sum_revenue,
 COUNT(o_orderkey) AS cnt`  
`FROM OLC GROUP BY o_orderkey, o_orderdate, o_shippriority;`	


##### Step 5 - Order data


`RES := ORDER F BY sum_revenue DESC, orderdate1 ASC;`


##### Step 6 - Save the results 


`STORE RES INTO 'results.txt' USING ('|') LIMIT 10;`



---
