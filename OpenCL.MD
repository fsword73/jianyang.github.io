/* Created with CodeXL */

// mask == 0 always ==> 0 to force access same data
// VLoad: 4X (2VGR read + 4X VGPR return) == 24 VGPR access 
//4X address: 8 insts,    12 VGPRs 
// Point += i&mask : 3 Inst, 9 VGPR access
// Max == 48x VGPR read/write, 16 insts
// Total: ~93 VGRs read/write, ~27 insts at most
// loop 100 times
__kernel void OpenCL_VGPR_read_write(__global int * src, uint mask, int loop, __global int *dest )
{
		uint gid = get_global_id(0) << 4;
	  uint localid = get_global_id(0) ;
	  uint localid2 = get_global_id(0) >> 10;
	
	
	  int16 data2 = 0;		
		__global int *point = src + localid2;
		for(int i=0; i < loop; i++)
		{		
			int16 data = vload16(0, point); 			
			data2 = max(data,data2);	
			point += i & mask;
		}


		// uint bstore = data.s0 & data.s4 & data.s8 & data.b & data2.b ;				
		if(localid & 0xFF000 ) {		
			vstore16(data2, 0, dest + gid );
		}
}
