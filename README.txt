# Parallel-Programming
Performance Comparison Of Sorting Algorithms

OpenMPI was used as a Message Passing Interface(MPI) to parallelize the sorting algorithms. The algorithms were parallelized by 
dividing the data equally into all the processors.The initialization of the process takes place in the root node. Root node(id=0) takes the array size from command argument given by the user and creates an array using the rand () function in C as below:

array= rand () % (max_num + 1 - min_num) + min_num;

Minimum number and maximum number decides the range of elements in the array.

The overall sorting process requires nodes of the format 2n for maximum efficiency. Hence the nodes over 2n remain are the non-active nodes. The root node divides and distributes the complete array equally amongst 2n nodes.

Then all the nodes including the root node individually perform the sorting for their data set. Sorting technique used (quick, merge or radix sort) is specified by the user in the command line argument as:

mpirun -n <number_of_nodes> <executive_file_name> <sorting_technique{Q,M,R}> <printing/not printing data{P,N}> <size_of_array>

The string used to specify the sorting technique is compared using the function strcmp() as:

if (strcmp(<string_argument>, <string_sort>) == 0)
{sorting technique}

and the respective sorting technique is used in all the nodes. The strcmp() function requires including of <string.h> library.
Once all the arrays are individually sorted in their respective nodes, they are merged. Amongst active nodes, every even node sends its data to odd node where the merging of sorted arrays takes place. Merging takes place keeping the sorting order intact. The number of active nodes is now reduced to half. The process of sending and merging repeats for the remaining active nodes. At every merging step the number of active nodes reduces to half and that is why the number of nodes required for the overall sorting algorithm is required to be of form 2n. The process continues till all the data is merged into the root node.
However, during merging there are lots of chances for one process to run faster than the other process which can lead to incorrect data message sending and receiving. Hence, all the data needs to be synchronously sent, which is done by using MPI_Ssend(). MPI_Ssend() will not return until matching receive is posted, or a handshake between send and receive takes place. Also, it likely gives best performance since this MPI implementation can completely avoid buffering data which is done when using MPI_Send().
Finally, the sorted arrays are printed by the root node. To calculate the overall time by the process, clocks were placed in the root node since the first step of creating array and final step of printing array in the overall sorting algorithm takes place in the root node. The difference in the end clock and start clock gives the time of the process.
