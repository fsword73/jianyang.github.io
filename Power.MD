# [Robust Power Estimation and Simultaneous Switching Noise Prediction Methods Using Machine Learnin](https://developer.download.nvidia.com/video/gputechconf/gtc/2019/presentation/s9661-nvidia-nsight-graphics-getting-the-most-from-your-vulkan-applications.pdf)

# [VoltaGE droop solver](https://research.nvidia.com/sites/default/files/pubs/2019-05_A-Fine-Grained-GALS//fine.grained.gals_.async_.camera_with_darpa.pdf)
# [Clock Drop of  IBM Power](https://ieeexplore.ieee.org/document/7870452)

# [Pausible async FIFO](https://research.nvidia.com/sites/default/files/pubs/2015-05_A-Pausible-Bisynchronous/pausible.fifo_.async_.15.final_.pdf)

# [Local shared Memory Power Reduction]
### Split into 2 parts: Computer Shader Based & Graphics Based 
### Single Port
### Lower Voltage/Freuency : Half Frequency
### Clock Gating most part of access

POWER = V * V * F * A * C

V == PROCESS, DESIGN,  LIB  VOLTAGE DROP
A == THREADS, CULL, INST, RAM(REG, CACHE, CONST, I, D)
C == LIB, DESIGN, PD, BEST FIT,
CLOCK TREE

DETECT CLOCK TREE Vs Peak PoweR
ACTIVE THREADS = LOCALID&MASK == MASK2 

ADDRESS = GID + LOCAL_ADRESS + i & MASK3 == 0
SHARED MEMORY ==  [ACTIVE THREADS + i]]  == DATA
only 1/64 Threads active. 
It almost == DECODE + CLOCK TREE

INST DECODE POWER = MASK ==0; 
SAME CODE, MASK all to ZERO 

IF(LCOALID & MASK5)
  output[gid] = local_data[lid];


CPU == PREDICTION, ADDRESSER LOAD/WRITE, INTEGER.FLOAT, SIMD, REG(RAM)




