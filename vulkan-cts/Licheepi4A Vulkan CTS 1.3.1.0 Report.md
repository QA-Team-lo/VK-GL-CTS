# Licheepi4A Vulkan CTS 1.3.1.0 Report

## Test Environment

* OS：Debian GNU/Linux 12（bookworm）
* Board：Licheepi 4A （16GB + 128GB）
* Download link:
  * [Release Vulkan CTS 1.3.1.0 · KhronosGroup/VK-GL-CTS](https://github.com/KhronosGroup/VK-GL-CTS/releases/tag/vulkan-cts-1.3.1.0)
  * https://pan.baidu.com/s/1xH56ZlewB6UOMlke5BrKWQ
* Reference document：https://github.com/KhronosGroup/VK-GL-CTS/

## Building CTS

### Requirements

```shell
sudo apt install -y git mesa-common-dev cmake gcc g++ python3-lxml pkg-config libpng-dev libvulkan-dev wget ninja-build
```

### Get resource

```shell
wget https://github.com/KhronosGroup/VK-GL-CTS/archive/refs/tags/vulkan-cts-1.3.1.0.tar.gz
tar xvf vulkan-cts-1.3.1.0.tar.gz
cd VK-GL-CTS-vulkan-cts-1.3.1.0
python3 external/fetch_sources.py
```

### patch

* Add head file

```shell
for file in \
    /home/sipeed/VK-GL-CTS-vulkan-cts-1.3.1.0/external/amber/src/src/descriptor_set_and_binding_parser.h \
    /home/sipeed/VK-GL-CTS-vulkan-cts-1.3.1.0/external/amber/src/include/amber/shader_info.h \
    /home/sipeed/VK-GL-CTS-vulkan-cts-1.3.1.0/external/amber/src/include/amber/recipe.h \
    /home/sipeed/VK-GL-CTS-vulkan-cts-1.3.1.0/external/vulkancts/modules/vulkan/spirv_assembly/vktSpvAsmFloatControlsTests.cpp
do
    sed -i '1a #include <cstdint>' "$file"
done
```

* Modify the following content at line 302 in `external/fetch_sources.py`.

```shell
	SourcePackage(
	#"http://zlib.net/zlib-1.2.11.tar.gz",
	    "https://zlib.net/fossils/zlib-1.3.1.tar.gz",
        "zlib-1.3.1.tar.gz",
	#	"c3e5e9fdd5004dcb542feda5ee4f0ff0744628baf8ed2dd5d66f8ca1197cb1a1",
	    "9a93b2b7dfdac77ceba5a558a580e74667dd6fede4585b91eefb60f03b72df23",
        "zlib"),
```

### compile

```shell
mkdir build && cd build
cmake .. -GNinja -DCMAKE_BUILD_TYPE=Debug
ninja -j4
```

## run CTS

```shell
cd external/vulkancts/modules/vulkan/
./deqp-vk --deqp-caselist-file=../../../../../external/vulkancts/mustpass/master/vk-default.txt --deqp-log-images=disable --deqp-log-shader-sources=disable
```

## Result

```shell
DONE!

Test run totals:
  Passed:        333718/1068487 (31.2%)
  Failed:        9/1068487 (0.0%)
  Not supported: 734760/1068487 (68.8%)
  Warnings:      0/1068487 (0.0%)
  Waived:        0/1068487 (0.0%)
```