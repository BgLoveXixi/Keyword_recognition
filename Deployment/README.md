# 基于ARM Cortex-M板上的关键字识别部署。
在微控制器上部署经过训练的关键词识别模型的第一步是量化，在[Quant_guide.md）里进行了描述。该目录包含示例代码和使用[mbed-cli]（https://github.com/ARMmbed/mbed-cli）和[CMSIS-NN]在任何Cortex-M板上运行量化DNN模型的步骤（https： //github.com/ARM-software/CMSIS_5）库。它还包括将KWS模型集成到带有板载麦克风的Cortex-M开发板上的示例，以演示实时音频数据上的关键词识别。

## 获取CMSIS-NN库并安装mbed-cli
下载[CMSIS-5]（https://github.com/ARM-software/CMSIS_5）库，它包含Cortex-M的优化神经网络内核。
```
cd Deployment
git clone https://github.com/ARM-software/CMSIS_5.git
```
安装[mbed-cli]（https://github.com/ARMmbed/mbed-cli）及其python依赖项。
```庆典
pip install mbed-cli
```
## 构建并运行一个简单的KWS示例
在此示例中，KWS推断对通过.h文件提供的音频数据运行。
首先创建一个新项目并安装在安装mbed-cli后第一次创建项目时提示的任何python依赖项。
```
mbed new kws_simple_test --mbedlib
```
获取所需的mbed库以进行编译。
```
cd kws_simple_test
mbed deploy
```
编译mbed板的代码（例如NUCLEO \ _F411RE）。
```
mbed compile -m NUCLEO_F411RE -t GCC_ARM --source。 \
  --source ../Source/KWS --source ../Source/NN --source ../Source/MFCC \
  --source ../Source/local_NN --source ../Examples/simple_test \
  --source ../CMSIS_5/CMSIS/NN/Include --source ../CMSIS_5/CMSIS/NN/Source \
  --source ../CMSIS_5/CMSIS/DSP/Include --source ../CMSIS_5/CMSIS/DSP/Source \
  --source ../CMSIS_5/CMSIS/Core/Include \
  --profile ../release_O3.json -j 8
```
将二进制文件（.bin）复制到电路板上（确保板子可以被检测到并安装了驱动）。打开一个串行终端（例如putty或minicom）并在屏幕上看到最终的分类输出。
```
cp ./BUILD/NUCLEO_F411RE/GCC_ARM/kws_simple_test.bin / media / <user> / NODE_F411RE /
sudo minicom
```
## 在[STM32F746NG开发套件]上运行KWS对现场音频的示例（http://www.st.com/en/evaluation-tools/32f746gdiscovery.html）
此示例在使用STM32F746NG发现套件上的板载麦克风捕获的实时音频上运行关键词识别示例。在对具有多个噪声源的实况音频数据执行关键词识别时，通常在指定窗口上对输出进行平均以生成平滑预测。平均窗口长度和检测阈值（对于每个关键字也可以是不同的）是确定整体关键字定位准确性和用户体验的两个关键参数。
```
mbed new kws_realtime_test  - 仅限创建
cd kws_realtime_test
cp ../Examples/realtime_test/mbed_libs/*.lib。
mbed deploy
mbed compile -m DISCO_F746NG -t GCC_ARM \
  --source . --source ../Source --source ../Examples/realtime_test \
  --source ../CMSIS_5/CMSIS/NN/Include --source ../CMSIS_5/CMSIS/NN/Source \
  --source ../CMSIS_5/CMSIS/DSP/Include --source ../CMSIS_5/CMSIS/DSP/Source \
  --source ../CMSIS_5/CMSIS/Core/Include \
  --profile ../release_O3.json -j 8
cp ./BUILD/DISCO_F746NG/GCC_ARM/kws_realtime_test.bin / media / <user> / DIS_F746NG /
```
## 使用gcc和make在[FRDM-K64F]（https://os.mbed.com/platforms/FRDM-K64F/）上构建示例
要构建此示例，请克隆CMSIS_5存储库，然后执行`make`。此示例是通过从[mbed在线编译器]（https://os.mbed.com/compiler/）导出一个简单的hello-world示例并编辑Makefile以合并关键字定位示例所需的源文件来创建的。
```
cd Deployment
＃克隆CMSIS_5存储库（如果尚未完成）
git clone https://github.com/ARM-software/CMSIS_5.git
cd examples / simple_test_k64f_gcc
make -j 8
＃将二进制文件复制到设备
cp ./BUILD/simple_test_k64f_gcc.bin / media / <user> / DAPLINK /
```
**注意：**提供的示例使用MFCC特征提取的浮点运算，但应该可以将它们转换为定点运算，以便在没有专用浮点单元的微控制器上进行部署。
