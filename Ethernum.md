# 1  [Crypto++ 5.6.0 Benchmarks](https://cryptopp.com/benchmarks.html)
# 2  [OpenCL Cryptographics Libarry](https://martin.preisler.me/wp-content/uploads/2015/06/OpenCL-Cryptographic-Library.pdf)
# 3  [Ethernum Overclocking guide](https://zhuanlan.zhihu.com/p/352462987)
# 5 Ethernum uses GPU mining only: 

## The OpenCL Kernel Shows following histogram of Insts ( total 8477 insts). V_XOR & V_AND is the key to performance. 



V_ADD3_U32 , 1

V_ADD_U32 , 164

**V_ALIGNBIT_B32 , 864**

V_ALIGNBYTE_B32 , 64

**V_AND_B32 , 804**

V_BFI_B32 , 1

V_CMP_EQ_I32 , 1

V_CMP_GE_U32 , 66

V_CNDMASK_B32 , 100

V_CVT_F32_U32 , 1

V_CVT_U32_F32 , 1

V_LSHL_ADD_U32 , 164

V_LSHLREV_B32 , 3

V_LSHRREV_B32 , 1

**V_MOV_B32 , 258**

V_MUL_F32 , 1

V_MUL_HI_U32 , 33

**V_MUL_LO_U32 , 577**

V_RCP_F32 , 1

V_READFIRSTLANE_B32 , 1

V_SUBREV_U32 , 33

V_SUB_U32 , 33

V_SWAP_B32 , 1

**V_XOR_B32 , 3809**

S_ADD_U32 , 65

S_AND_B32 , 33

S_AND_B64 , 33

S_BARRIER , 161

S_BRANCH , 19

S_CBRANCH_SCC0 , 19

S_CMP_GT_U32 , 3

S_CMP_LE_I32 , 16

S_CMP_LG_I32 , 1

S_CMP_LG_U32 , 2

S_CSELECT_B32 , 2

S_CSELECT_B64 , 1

S_ENDPGM , 1

S_LOAD_DWORD , 22

S_LOAD_DWORDX2 , 17

S_MOV_B32 , 6

S_MOVK_I32 , 25

S_MUL_HI_U32 , 2

S_MUL_I32 , 2

S_NOP , 7

S_SUB_I32 , 3

S_SUB_U32 , 2

S_WAITCNT , 440

BUFFER_LOAD_DWORDX4 , 132

BUFFER_STORE_DWORDX4 , 4

DS_READ2_B64 , 132

DS_READ_B32 , 160

DS_WRITE2_B64 , 132

DS_WRITE_B32 , 32

POSSBILE PATTERN:  "AND then XOR", XOR_XOR_XOR,  MUL_XOR

