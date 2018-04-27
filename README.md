# Matrix-Multiplication-Hadoop
Simple Program to multiply two matrices using Hadoop MapReduce
  
Program executed on SDSC Comet Cluster, using XSEDE Login (Special thanks to professor and XSEDE for making this possible)

### Input File Format:
i,j,value  
.  
.  
.  
i,j,value

### Output File Format (Resultant Multiplied Matrix):
i,j,value  
.  
.  
.  
.  
i,j,value  
  
Input Files with two capacities are provided.
1) something-small.txt (To check the program in local mode)
2) something-large.txt (To check the program in distributed mode)

### Build and Run

Use *sbatch* to queue jobs in cluster 

1) Using __.build__ file to compile the program
2) Using __.local.run__ files to test the program in local mode.
3) Using __.distr.run__ files to test the program in distributed mode.

### Program Stages

#### Stage 1

Map the input matrices line by line and emits the matrix element.
Use a reducer to multiply value for same indices.

#### Stage 2

Use the output from the Stage 1 Reducer and pass along the same input to the Stage 2 reducer, where all the values having same index pairs are summed up to get the final output value.

#### Pseudocode:

First Map-Reduce job:  
map(key,line) =&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;// mapper for matrix M
  split line into 3 values: i, j, and v  
  emit(j,new Elem(0,i,v))

map(key,line) =             // mapper for matrix N  
  split line into 3 values: i, j, and v  
  emit(i,new Elem(1,j,v))  

reduce(index,values) =  
  A = all v in values with v.tag==0  
  B = all v in values with v.tag==1  
  for a in A  
     for b in B  
         emit(new Pair(a.index,b.index),a.value*b.value)  
Second Map-Reduce job:  
map(key,value) =  // do nothing  
  emit(key,value)  

reduce(pair,values) =  // do the summation  
  m = 0  
  for v in values  
    m = m+v  
  emit(pair,pair.i+","+pair.j+","+m)  
