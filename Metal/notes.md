Beginners guide to Metal kernels, from GPU MODE

## Motivation

I have an M2 mac book air, my specif mac book is configured as follows:

- CPU - 8 cores, 4 performace and 4 efficiency cores
- GPU - 10 cores

> _Theoretical flow count with CPU only_

Assume we are able to parallelize the application and use every single core.
The high-end performance cores can deliver about 4.3 flops per cycle.
The efficiency cores can deliver 1.5 flops per cycle. The clock speed is approximately
3.5 GHz.
(4.3 + 1.5) * 3.5 * 4 = 81.2 GFLOPS = 0.0812 TFLOPS

> _Theoretical flow count with GPU only_

Apple claims around 3.6 teraflops, but in some places it states that it can go up to 6.8 teraflops. Either way, the GPU seems to be a lot faster, so I should really learn how to program a GPU.

We're basically writing meta shaders. It is code that runs on GPUs, and it pretty similar to CUDA kernels
MTLBuffer is GPU memeory. it is actually shared with the CPU (cause apple silicon just hits like that)
These things are usually called in objective C or swift. fuck swift for now, I wanna use objective C
Documentation is shakey at best, but sometimes this stuff gets dispateched to apple's neural engine? but idk

> How does the unified memeory model in CUDA compare to the memeory model in metal?

in NVIDIA's orin, it is similar to apple where memeory is physically shared. In a traditional setup where you have an x86 chip and a GPU, they are physically seperate devices
The GPU device is a bus master (capable of direct memeory access) so the GPU and CPU can map portions of their memory onto the other, kind of like a pointer (I think).
This is called host pinned memory, so CPU can see memory allocated on the GPU but with big performance costs. GPU cahe and PCU cache is never synced tho.

The benefit of dedicated memeory for CPU and GPU is that you guarentee your full bandwidth. In apple silicon there is the case that if CPU is reading a lot of memory, there isn't much bandwidth left of the GPU

> So if we're bottlenecked on reading memory bandwidth, why bother dispatching to the GPU?

GPUs typically have higher throughput. The specific numbers change if you have a base chip, the pro chip, or the ultra chip. But they have higher throughput, so it's still beneficial to try and do reads up using a GPU than a CPU.
Also, depending on the application, the developers expect it to throttle CPU memory reads to give the GPU enough bandwidth to operate.

### how to profile metal kernels?

sadly xcode.

## time to speed up gemV

how do we speed it up?
- use SIMD. 2x 3x speed up, use float 4
- use matrix transforms, so set type to float4x4, float3x4, etc. this is a data type that the architecture is optimized for?
