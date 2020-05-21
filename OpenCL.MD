
#define PROGRAM_FILE "my_test_kernel.cl"
#define KERNEL_FUNC "my_test_kernel"
#define MAX_SOURCE_SIZE (0x100000)

#include <stdio.h>
#include <stdlib.h>
#include <math.h>

#ifdef MAC
#include <OpenCL/cl.h>
#else
#include <CL/cl.h>
#endif
#include<iostream>
#include<cstring>
using namespace std;

#pragma warning(disable: 4996)  



int main(int argc, char* argv[])
{

	int loops = 10000;
	int mask0  = 0xFF;
	int mask1 = 0;
	int mask2 = 0;


	// Length of vectors
	unsigned int n = 256 * 10000;

	// Host input vectors
	int *h_a;
	int *h_b;
	// Host output vector
	int *h_c;

	// Device input buffers
	cl_mem d_a;
	cl_mem d_b;
	// Device output buffer
	cl_mem d_c;

	cl_platform_id cpPlatform;        // OpenCL platform
	cl_device_id device_id;           // device ID
	cl_context context;               // context
	cl_command_queue cmdqueue;           // command queue
	cl_program program;               // program
	cl_kernel kernel;                 // kernel

	// Size, in bytes, of each vector
	size_t bytes = n * sizeof(int);

	// Allocate memory for each vector on host
	h_a = (int*)malloc(bytes);
	h_b = (int*)malloc(bytes);
	h_c = (int*)malloc(bytes);

	// Initialize vectors on host
	int i;
	for (i = 0; i < n; i++)
	{
		//h_a[i] = 0;
		if (i & 1)
			h_a[i] = 0x5A5A5A5A;
		else
			h_a[i] = 0xA5A5A5A5;
		h_b[i] = 0;
	}

	size_t globalSize, localSize;
	cl_int err;

	// Number of work items in each local work group
	localSize = 256;

	// Number of total work items - localSize must be devisor
	globalSize = ceil(n / (float)localSize)*localSize;

	// Bind to platform
	err = clGetPlatformIDs(1, &cpPlatform, NULL);

	// Get ID for the device
	err = clGetDeviceIDs(cpPlatform, CL_DEVICE_TYPE_GPU, 1, &device_id, NULL);

	// Create a context  
	context = clCreateContext(0, 1, &device_id, NULL, NULL, &err);

	// Create a command queue 
	cmdqueue = clCreateCommandQueue(context, device_id, 0, &err);

	// Create the compute program from the source buffer
	char *kernelSource;
	size_t kernelSize;
	{
		FILE *kernelFile;
		
		

		kernelFile = fopen(PROGRAM_FILE, "r");

		if (!kernelFile) {

			fprintf(stderr, "No file named my_test_kernel.cl was found\n");

			exit(-1);

		}
		kernelSource = (char*)malloc(MAX_SOURCE_SIZE);
		kernelSize = fread(kernelSource, 1, MAX_SOURCE_SIZE, kernelFile);
		fclose(kernelFile);
	}
	
	//printf("%s\n", kernelSource);
	//printf("%d\n", strlen(kernelSource));
	program = clCreateProgramWithSource(context, 1,
		(const char **)& kernelSource, &kernelSize, &err);

	// Build the program executable 
	clBuildProgram(program, 0, NULL, NULL, NULL, NULL);

	// Create the compute kernel in the program we wish to run
	kernel = clCreateKernel(program, "my_test_kernel", &err);

	// Create the input and output arrays in device memory for our calculation
	d_a = clCreateBuffer(context, CL_MEM_READ_ONLY, bytes, NULL, NULL);
	d_b = clCreateBuffer(context, CL_MEM_READ_ONLY, bytes, NULL, NULL);
	d_c = clCreateBuffer(context, CL_MEM_WRITE_ONLY, bytes, NULL, NULL);

	// Write our data set into the input array in device memory
	err = clEnqueueWriteBuffer(cmdqueue, d_a, CL_TRUE, 0,
		bytes, h_a, 0, NULL, NULL);
	err |= clEnqueueWriteBuffer(cmdqueue, d_b, CL_TRUE, 0,
		bytes, h_b, 0, NULL, NULL);

	// Set the arguments to our compute kernel
	err = clSetKernelArg(kernel, 0, sizeof(cl_mem), &d_a);
	err |= clSetKernelArg(kernel, 1, sizeof(cl_mem), &d_b);
	err |= clSetKernelArg(kernel, 2, sizeof(cl_mem), &d_c);
	err |= clSetKernelArg(kernel, 3, sizeof(int), &loops);
	err |= clSetKernelArg(kernel, 4, sizeof(int), &mask0);
	err |= clSetKernelArg(kernel, 5, sizeof(int), &mask1);
	err |= clSetKernelArg(kernel, 6, sizeof(int), &mask2);

	for (int i=0; i < 100; i++)
	{
		// Execute the kernel over the entire range of the data set  
		err = clEnqueueNDRangeKernel(cmdqueue, kernel, 1, NULL, &globalSize, &localSize,
			0, NULL, NULL);

		// Wait for the command queue to get serviced before reading back results
		clFinish(cmdqueue);
	}

	// Read the results from the device
	clEnqueueReadBuffer(cmdqueue, d_c, CL_TRUE, 0,
		bytes, h_c, 0, NULL, NULL);

	//Sum up vector c and print result divided by n, this should equal 1 within error
	int sum = 0;
	//for (i = 0; i < n; i++)
	//	sum += h_c[i];
	//printf("final result: %f"n", sum / n);

	// release OpenCL resources
	clReleaseMemObject(d_a);
	clReleaseMemObject(d_b);
	clReleaseMemObject(d_c);
	clReleaseProgram(program);
	clReleaseKernel(kernel);
	clReleaseCommandQueue(cmdqueue);
	clReleaseContext(context);

	//release host memory
	free(h_a);
	free(h_b);
	free(h_c);
	free(kernelSource);

	return 0;
}


//Mask0 ==FF;
//Mask1 == 00;
//mask2 == 00
__kernel void test_clock_tree(__global int * A, __global int *B, __global int *C,int loop, int mask0, int mask1, int mask2)
{
		__local int ldata[1024];
		int local_id = get_local_id(0);
		int gid0 =get_global_id(0);
		int gid = gid0 & mask1;
		
		
		for(int i=0; i < 1024; i+=256){
				ldata[local_id+i] = 0;
		}
		
		for(int i= 0; i < loop; i++){		
			 if(( i&mask0) == local_id) {
				 ldata[local_id] += A[gid + (i&mask1)];
			 }					 
		}	
		
		if(mask2 & local_id){
			B[gid0] = ldata[local_id];	
		}
		
	
}




// mask == 0;
//~90+VGPR+27INST
__kernel void test_load_vgpr(__global int * A, __global int *B, __global int *C,int loop, int mask0, int mask1, int mask2)
{
		uint gid = get_global_id(0) << 4;
	  uint localid = get_global_id(0) ;
	  uint localid2 = get_global_id(0) >> 10;
	
	
	  int16 data2 = 0;		
		__global int *point = A + localid2;
		for(int i=0; i < 10000; i++)
		{		
			int16 data = vload16(0, point); 			
			data2 = max(data,data2);	
			point += i & mask1;
			point+=1;
		}


		// uint bstore = data.s0 & data.s4 & data.s8 & data.b & data2.b ;				
		if(localid & mask2 ) {		
			vstore16(data2, 0, B + gid );
		}
}

