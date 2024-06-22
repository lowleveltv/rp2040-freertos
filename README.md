
# Gettings started
This is a written expleanation of the [video tutorial](https://www.youtube.com/watch?v=5pUY7xVE2gU) on how to get started with FreeRTOS on the Raspberry Pi Pico

This uses [picosdk](https://github.com/raspberrypi/pico-sdk/tree/6a7db34ff63345a7badec79ebea3aaef1712f374) (SDK 1.5.1) and [FreeRTOS](https://github.com/FreeRTOS/FreeRTOS-Kernel/tree/V11.0.1) (V11.0.1)
## 1. Clone the Repo
```bash
git clone https://github.com/lowlevellearning/rp2040-freertos
cd rp2040-freertos
```

## 2. Adding necessary libraries
This can be done manually ([2.2](#22-adding-the-necessary-libaries-manually2.2)) or by using git submodules ([2.1](#))

### 2.1 Adding the necessary libaries using git submodules
```bash
git submodule update --init --recursive
export PICO_SDK_PATH=$PWD/lib/pico-sdk
```

### 2.2 Adding the necessary libaries manually
We just clone the repositories into the main folder, just like in the video

#### 2.2.1 Add PICO-SDK
This is dependend on your setup. If you have the PICO-SDK installed, you may need to add the path to the `PICO_SDK_PATH` to point to the location of the SDK.

```bash
export PICO_SDK_PATH=/path/to/pico-sdk
```

If you don't have the SDK installed, you can clone it from the official repo.
```bash
git clone https://github.com/raspberrypi/pico-sdk
export PICO_SDK_PATH=$PWD/pico-sdk
```

You may also need to install the toolchain. You can find the instructions for that in [original video](https://www.youtube.com/watch?v=JhajoAyP8e4) or in the [getting-started PDF](https://datasheets.raspberrypi.com/pico/getting-started-with-pico.pdf)


#### 2.2.2 Adding FreeRTOS
```bash
git clone https://github.com/FreeRTOS/FreeRTOS-Kernel
cd FreeRTOS-Kernel
git checkout V11.0.1
cd ..
```

## 3. Build the project
```bash
cd build
rm -rf * # This nukes the entire build folder, to have a clean slate
cmake ..
# -j4 means that it will use 4 threads to compile, this can be modified to your liking
make -j4 
```

## 4. Flash to the Pico
Unplug the Pico, press and hold the BOOTSEL button, plug the Pico back in, release the BOOTSEL button. The Pico should now appear as a USB drive on your computer. By copying a file to this drive you can flash the program to the Pico.

This can be done via drag and drop or:
```bash
cp blink.uf2 </path/to/pico>
```
(were `</path/to/pico>` is the path to the USB drive)

----
# FREE-RTOS Issue

Currently (Jun 22, 2024) freertos is not working correctly with the rpi pico since release V11.1.0.

When trying to build the project will get this error
```
error: #error configENABLE_MPU must be defined in FreeRTOSConfig.h. Set configENABLE_MPU to 1 to enable the MPU or 0 to disable the MPU
```

when doing so by adding `#define configENABLE_MPU 0` or `#define configENABLE_MPU 1` to the `FreeRTOSConfig.h` file, the following error will appear in multiple freertos related files:
```
tasks.c:(privileged_functions+0xecc): undefined reference to `vClearInterruptMask'
```

This issue can be avoided by using and older version of FreeRTOS: V11.0.1 (commit: 0240cd5)

You can just checkout the correct version by running:
```bash
git checkout 0240cd5
```

This will checkout the correct version of FreeRTOS. You can now build the project as usual.