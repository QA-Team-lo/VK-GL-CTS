# MilkV Megrez Vulkan CTS 1.3.8.1 Report

## Test Environment

* OS：RockOS
* Board：MilkV Megrez
* Download link:
  * [Release Vulkan CTS 1.3.8.1 · KhronosGroup/VK-GL-CTS](https://github.com/KhronosGroup/VK-GL-CTS/releases/tag/vulkan-cts-1.3.8.1)
* Reference document：https://github.com/KhronosGroup/VK-GL-CTS/

## Building CTS

### Requirements

```shell
sudo apt install -y git mesa-common-dev cmake gcc g++ python3-lxml pkg-config libpng-dev libvulkan-dev wget ninja-build
```

### Get resource

```shell
wget https://github.com/KhronosGroup/VK-GL-CTS/archive/refs/tags/vulkan-cts-1.3.8.1.tar.gz
tar xvf vulkan-cts-1.3.8.1.tar.gz
cd VK-GL-CTS-vulkan-cts-1.3.8.1
```

### Patch

```shell
vim external/fetch_sources.py
# Edit zlib link to: https://zlib.net/fossils/zlib-1.2.13.tar.gz
```

### Compile

```shell
mkdir build && cd build
cmake .. -GNinja -DCMAKE_BUILD_TYPE=Debug
ninja -j4
```

## Running Vulkan CTS

```shell
cd external/vulkancts/modules/vulkan/
./deqp-vk --deqp-caselist-file=../../../../../external/vulkancts/mustpass/main/vk-default.txt \
--deqp-log-images=disable \
--deqp-log-shader-sources=disable
```

## Result

Test result: MilkV Megrez failed to complete the test

### Issues

* ABORTED

Test case: `./deqp-vk --deqp-caselist=dEQP-VK.spirv_assembly.instruction.compute.image_sampler.imagefetch.sampled_image.optypeimage_mismatch_rgba8ui.depth_property.depth --deqp-log-images=disable --deqp-log-shader-sources=disable`

```shell
Test case 'dEQP-VK.spirv_assembly.instruction.compute.image_sampler.imagefetch.sampled_image.optypeimage_mismatch_rgba8ui.depth_property.depth'..
deqp-vk: compiler/spvcompiler/LLVMWriter.cpp:15524: UFGENTypeSpecifier {anonymous}::GetUFGENImageTypeSpecifier(const spvcompiler::SpvTypeImage*): Assertion `samplerFormatIdx == 0' failed.
Aborted
```

* Firmware Issue

Test case: `./deqp-vk --deqp-caselist=dEQP-VK.reconvergence.subgroup_uniform_control_flow_elect.compute.nesting3.7.43 --deqp-log-images=disable --deqp-log-shader-sources=disable`

```shell
===== Mon Mar 24 07:49:02 PM CST 2025 =====
Driver Status:   OK

Device ID: 0:128
Firmware Status: DEAD (Asserted)
Server Errors:   0
HWR Event Count: 1
CRR Event Count: 0
SLR Event Count: 0
WGP Error Count: 0
TRP Error Count: 0
FWF Event Count: 0
APM Event Count: 0
MemoryUsageKMalloc                     1831168
MemoryUsageKMallocMax                 36852800
MemoryUsageVMalloc                     2408448
MemoryUsageVMallocMax                 80117760
MemoryUsageAllocPTMemoryUMA            4296704
MemoryUsageAllocPTMemoryUMAMax      1127059456
MemoryUsageVMapPTUMA                   4251648
MemoryUsageVMapPTUMAMax               91734016
MemoryUsageAllocPTMemoryLMA                  0
MemoryUsageAllocPTMemoryLMAMax               0
MemoryUsageIORemapPTLMA                      0
MemoryUsageIORemapPTLMAMax                   0
MemoryUsageAllocGPUMemLMA                    0
MemoryUsageAllocGPUMemLMAMax                 0
MemoryUsageZombieGPUMemLMA                   0
MemoryUsageZombieGPUMemLMAMax                0
MemoryUsageAllocGPUMemUMA            606085120
MemoryUsageAllocGPUMemUMAMax        3334651904
MemoryUsageZombieGPUMemUMA             4816896
MemoryUsageZombieGPUMemUMAMax        275644416
MemoryUsageAllocGPUMemUMAPool          3223552
MemoryUsageAllocGPUMemUMAPoolMax     125239296
MemoryUsageMappedGPUMemUMA/LMA        57724928
MemoryUsageMappedGPUMemUMA/LMAMax    610660352
MemoryUsageDmaBufImport                8294400
MemoryUsageDmaBufImportMax             8294400
MemoryUsageDmaBufZombie                      0
MemoryUsageDmaBufZombieMax                   0
```

* FAILED

Failed test cases:

```shell
dEQP-VK.pipeline.monolithic.spec_constant.graphics.vertex.basic.float16_2
dEQP-VK.pipeline.monolithic.spec_constant.graphics.vertex.basic.float_2_packed
dEQP-VK.pipeline.monolithic.spec_constant.graphics.fragment.basic.float16_2
dEQP-VK.pipeline.monolithic.spec_constant.graphics.fragment.basic.float_2_packed
dEQP-VK.pipeline.monolithic.spec_constant.graphics.tess_control.basic.float16_2
dEQP-VK.pipeline.monolithic.spec_constant.graphics.tess_control.basic.float16_2_packed
dEQP-VK.pipeline.monolithic.spec_constant.graphics.tess_eval.basic.float16_2
dEQP-VK.pipeline.monolithic.spec_constant.graphics.tess_eval.basic.float16_2_packed
dEQP-VK.pipeline.monolithic.spec_constant.graphics.geometry.basic.float16_2
dEQP-VK.pipeline.monolithic.spec_constant.graphics.geometry.basic.float16_2_packed
dEQP-VK.pipeline.monolithic.spec_constant.graphics.compute.basic.float16_2
dEQP-VK.pipeline.monolithic.spec_constant.graphics.compute.basic.float16_2_packed

dEQP-VK.pipeline.monolithic.timestamp.calibrated.calibration_test
[Device Domain] Device expected timestamp differs 120166400 nanoseconds (expect value &lt;= 100000000)
```
