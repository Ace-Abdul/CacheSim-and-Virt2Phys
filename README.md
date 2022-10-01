# Overview
## cachesim.c
The simulator takes the following input parameters on the command line: name of the
file holding the loads and stores, cache size (not including tags or valid bits) in kB, associativity, and theblock size in bytes. The replacement policy is always LRU. For example,
“cachesim tracefile 1024 4 32” simulates a cache that is 1024kB (=1MB), 4-way setassociative, has 32-byte blocks, and uses LRU replacement. This cache will be processing the loads and
stores in the file called tracefile.

Important Assumptions: Addresses are 24-bits (3 bytes), and thus addresses range from 0 to 224
-1 (i.e.,there is 16MB of address space). The machine is byte-addressed and big-endian. The cache size, associativity, block size, and access size will all be powers of 2. Cache size will be no larger than 2MB,
block size will be no larger than 1024 bytes, and no access will be larger than the block size. No cache access will span multiple blocks (i.e., each cache access fits within a single block).


All cache blocks are initially invalid. All cache misses are satisfied by the main memory (and you must track the values written through to memory in case they are subsequently loaded). If a block has never been written before, then its value in main memory is zero. The cache is write-through and write-no-allocate.

### Calling syntax
The program will be called cachesim, and will have the following calling syntax:
 ./cachesim \<trace-file> \<cache-size-kB> \<associativity> \<block-size>

Arguments:
* \<trace-file>: Filename of the memory access trace file.
* \<cache-size-kB>: Total capacity of the cache, kilobytes (kB). A power of two between 1 and 2048.
* \<associativity>: The set associativity of the cache, AKA the number of ways. A power of two.
* \<block-size>: The size of the cache blocks, in bytes. A power of two between 2 and 1024.

## virt2phys
This program ranslates virtual addresses to physical addresses by means of a page table loaded from a file, per the specifications below.
### Calling syntax

 ./virt2phys \<page-table-file> \<virtual-address>
Arguments:
* \<page-table-file>: File containing page table
* \<virtual-address>: Virtual address to translate (in hex).
Page table file format
The format of the page table file is a whitespace-delimited sequence of numbers (hint: this is an indicator
that you can get by with nothing but fscanf()). The format is as follows:


\<address-bits> \<page-size>

\<ppn>  \(physical page number for virtual page 0; set to -1 if invalid)

\<ppn> (physical page number for virtual page 1; set to -1 if invalid)

 ...

\<ppn> (physical page number for virtual page N-1, where N is the number of virtual pages; set to -1 if invalid)


All values above are decimal integer numbers. The fields above are:


* \<address-bits>: The word size of the system, in bits. Will be ≤ 24.
* \<page-size>: Page size of the virtual memory system, in bytes.
* \<ppn>: A physical page number in the page table. Set to -1 for invalid. The number of ppn values will be
equal to the number of virtual pages, which can be computed from the above two fields.


Files fed to the program should meet the following rules:
* Files must be of valid format and fully specify the page table content.
* The number of address bits must at most 24.
* The configuration must always make sense (e.g., page size less than the size of memory, page size apower of 2, etc.).
* Numbers must always be appropriate, e.g. no ppn should ever be larger than the maximum possible ppn.