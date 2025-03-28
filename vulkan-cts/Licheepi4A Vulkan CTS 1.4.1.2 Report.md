# Licheepi4A Vulkan CTS 1.4.1.2 Report

## Test Environment

* OS：Debian GNU/Linux 12（bookworm）
* Board：Licheepi 4A （16GB + 128GB）
* Download link：https://pan.baidu.com/s/1xH56ZlewB6UOMlke5BrKWQ
* Reference document：https://github.com/KhronosGroup/VK-GL-CTS/

## Build CTS

### Requirement

```shell
sudo apt install -y git mesa-common-dev cmake gcc g++ python3-lxml pkg-config libpng-dev libvulkan-dev wget ninja-build
```

### Download sources

```shell
wget https://github.com/KhronosGroup/VK-GL-CTS/archive/refs/tags/vulkan-cts-1.4.1.2.tar.gz

tar xvf vulkan-cts-1.4.1.2.tar.gz
```

### Patch

Replace the zlib link and SHA-256 hash at **external/fetch_sources.py**, line number **306**.

```
cd VK-GL-CTS-vulkan-cts-1.4.1.2/
    SourcePackage(
       # "https://github.com/madler/zlib/releases/download/v1.2.13/zlib-1.2.13.tar.gz",
       # "b3a24de97a8fdbc835b9833169501030b8977031bcb54b3b3ac13740f846ab30",
        "https://zlib.net/fossils/zlib-1.3.1.tar.gz",
        "9a93b2b7dfdac77ceba5a558a580e74667dd6fede4585b91eefb60f03b72df23",
        "zlib"),

```

### Get Source code

```shell
python3 external/fetch_sources.py
```

### Build

```shell
mkdir build && cd build
cmake .. -GNinja -DCMAKE_BUILD_TYPE=Debug
ninja -j4
```

## Run Vlukan CTS

```shell
cd external/vulkancts/modules/vulkan/
./deqp-vk --deqp-caselist-file=../../../../../external/vulkancts/mustpass/main/vk-default.txt \
--deqp-log-images=disable \
--deqp-log-shader-sources=disable
```

## Result

```
Test run totals:
  Passed:        5742/6605 (86.9%)
  Failed:        454/6605 (6.9%)
  Not supported: 409/6605 (6.2%)
  Warnings:      0/6605 (0.0%)
  Waived:        0/6605 (0.0%)
Test run was ABORTED!
```

## Cause

* ABORTED

```shell
  ResourceError (vk.createComputePipelines(device, pipelineCache, 1u, pCreateInfo, pAllocator, &object): VK_ERROR_OUT_OF_HOST_MEMORY at vkRefUtil.cpp:45)
```

Indicates that **Vulkan** encountered a `VK_ERROR_OUT_OF_HOST_MEMORY` (**out of host memory**) error when calling `vkCreateComputePipelines()` to create a compute pipeline.

```shell
dEQP-VK.api.object_management.multithreaded_per_thread_device.merged_pipeline_cache
#针对 多线程设备管理与合并的管线缓存（merged_pipeline_cache） 进行测试挂掉了
```

---

* GPU status

```shell
sudo cat /sys/kernel/debug/pvr/gpu00/debug_dump | grep State

Services State: OK
RGX Device State: Active
RGX Power State: OFF
RGX FW State: OK (HWRState 0x00000001: HWR OK;) 
# 固件状态正常
RGX FW Power State: RGXFWIF_POW_OFF (APM enabled: 1490 ok, 8 denied, 0 non-idle, 55 retry, 0 other, 1553 total. Latency: 10 ms)
RGX FW OS 0 - State: active; Freelists: Ok; Priority: 0; MTS off;
Device System Power State: ON

sudo cat /sys/kernel/debug/pvr/status	#检查error reg
Driver Status:   OK

Device ID: 0:128
Firmware Status: OK
Server Errors:   0
HWR Event Count: 0
CRR Event Count: 0
SLR Event Count: 0
WGP Error Count: 0
TRP Error Count: 0
FWF Event Count: 0
APM Event Count: 1554
GPU Utilisation: 0%
```

Conclusion: **The GPU did not crash.**

---

* Kernel information

```shell
dmesg -T | grep -iE "oom|out of memory|kill|gpu|pvrsrvkm|vdma"

[Wed Mar 19 06:01:54 2025] (NULL device *): failed to find vdmabuf_reserved_memory node
# GPU内存预留失败
[Wed Mar 19 06:01:54 2025] virtio-vdmabuf: carveout bufs setup failed -22
# DMA缓冲区分配失败
[Wed Mar 19 06:01:55 2025] misc vhost-vdmabuf: failed to find vdmabuf_reserved_memory node
[Wed Mar 19 06:01:55 2025] misc vhost-vdmabuf: vhost-vdmabuf: carveout bufs setup failed -22
# 虚拟化DMA错误
[Wed Mar 19 06:01:55 2025] misc vhost-vdmabuf: vhost-vdmabuf: init successfully
[Wed Mar 19 06:01:55 2025] [WLAN_RFKILL]: Enter rfkill_wlan_init
[Wed Mar 19 06:01:55 2025] [WLAN_RFKILL]: Enter rfkill_wlan_probe
[Wed Mar 19 06:01:55 2025] [WLAN_RFKILL]: can't find rockchip,grf property
[Wed Mar 19 06:01:55 2025] [WLAN_RFKILL]: wlan_platdata_parse_dt: wifi_chip_type = rtl8723ds
[Wed Mar 19 06:01:55 2025] [WLAN_RFKILL]: wlan_platdata_parse_dt: wifi power will enabled while kernel starting and keep on.
[Wed Mar 19 06:01:55 2025] [WLAN_RFKILL]: wlan_platdata_parse_dt: wifi power controled by gpio.
[Wed Mar 19 06:01:55 2025] [WLAN_RFKILL]: wlan_platdata_parse_dt: The ref_wifi_clk not found !
[Wed Mar 19 06:01:55 2025] [WLAN_RFKILL]: rfkill_wlan_probe: init gpio
[Wed Mar 19 06:01:55 2025] [WLAN_RFKILL]: rockchip_wifi_power: 1
[Wed Mar 19 06:01:55 2025] [BT_RFKILL]: rfkill_get_bt_power_state: rfkill-bt driver has not Successful initialized
[Wed Mar 19 06:01:55 2025] [WLAN_RFKILL]: wifi turn on power. -1
[Wed Mar 19 06:01:55 2025] [WLAN_RFKILL]: Exit rfkill_wlan_probe
[Wed Mar 19 06:01:55 2025] [BT_RFKILL]: Enter rfkill_rk_init
[Wed Mar 19 06:01:55 2025] [BT_RFKILL]: bluetooth_platdata_parse_dt: uart_rts_gpios is no-in-use.
[Wed Mar 19 06:01:55 2025] [BT_RFKILL]: bluetooth_platdata_parse_dt: clk_get failed!!!.
[Wed Mar 19 06:01:55 2025] [BT_RFKILL]: bt_default device registered.
[Wed Mar 19 06:02:02 2025] [BT_RFKILL]: bt turn on power
[Wed Mar 19 06:02:03 2025] [drm] Initialized pvr 1.17.6210866 20170530 for ffef400000.gpu on minor 1
[Wed Mar 19 06:02:18 2025] [BT_RFKILL]: bt shut off power

```

Conclusion: **The GPU driver encountered an issue while allocating a DMA buffer.**



