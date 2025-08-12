---
title: VScode开发STM32  # 标题（自动生成，可修改）
date: 2025-08-11 15:30:00  # 发布时间（自动生成，可修改）
tags: [STM32, VScode, CortexM4]  # 文章标签（可选，用逗号分隔）
categories: 开发环境  # 文章分类（可选，如 "技术"、"生活"）
description: VScode开发STM32  # 可选，用于SEO或摘要显示
---

# M4嵌入式开发-Vscode工具

Keil和IAR是较为常用的嵌入式工具（针对Cortex-M系列开发，典型代表意法半导体STM32、兆易的GD32等芯片）。

## Keil的优劣势：

-	1.**原生支持**：对 ARM Cortex-M 内核（M0/M3/M4/M7 等）的支持极其原生，编译器（ARMCC）针对 ARM 架构优化极佳，生成的代码效率高（尤其是中断响应、指令调度），适合资源受限的微控制器（如小容量 STM32F103）
-	2.**使用简单**：集成了 ULINK 调试器支持，可直接通过 SWD/JTAG 接口调试，调试信息（寄存器、内存、外设状态）与 ARM 内核深度绑定，对底层问题（如栈溢出、外设配置错误）的定位更直观。
-	3.**管理简单**：项目管理界面简洁，无需手动配置工具链路径、宏定义等（通过 “Device” 选择芯片后自动适配），配合芯片厂商的库（如 STM32 标准库）可快速搭建工程，对新手友好。


## IAR的优势：

-	1.**多芯片支持**：除 ARM 外，还完美支持 MSP430（TI）、AVR（Microchip）、RISC-V、PIC 等架构，尤其在 TI MSP430、NXP Kinetis 等芯片的开发中，支持度甚至优于 Keil。
-	2.**编译优化**：自带的 IAR C/C++ Compiler 以 “代码体积小、运行效率高” 著称，通过深度优化（如循环展开、指令重排）可显著降低资源占用（尤其适合 RAM/Flash 极小的芯片，如 MSP430F149）。在对功耗、实时性要求严格的场景（如工业传感器、汽车电子）中优势明显。
-	3.**管理效率**：对多文件、多模块的大型工程（如汽车 ECU 固件）支持更好，增量编译速度快，且内置静态分析工具（如代码复杂度检测），适合团队协作开发。


##  VScode的优劣势：

-	1.**跨平台**:支持 Windows、Mac、Linux 全平台，解决了 Keil（主要支持 Windows）、IAR（部分版本支持 Mac/Linux 但体验一般）的平台限制。对于需要在不同系统间切换的开发者（如兼顾 PC 端与嵌入式开发），或团队成员使用不同系统时，协作更顺畅。
-	2.**可扩展**：依赖丰富的插件生态（如C/C++、Cortex-Debug、PlatformIO、STM32CubeIDE插件等），可适配几乎所有主流芯片架构（ARM、RISC-V、AVR 等）和工具链（GCC、Clang 等）。
-	3.**低成本**：本身免费开源，配合开源工具链（如 ARM-GCC）可实现 “零成本” 开发，避免 Keil/IAR 的商业授权费用（尤其是大型项目）。
-	4。**多语言**：除嵌入式 C/C++ 外，还能无缝支持 Python（脚本开发）、JavaScript（上位机交互）等，适合需要 “嵌入式 + 上位机” 联合开发的场景（如物联网设备的固件与云平台对接）。

# 1.环境搭建
## 1.1 安装Vscode
-	[Vscode官网下载地址](https://code.visualstudio.com/Download)
-	[.Net更新](https://dotnet.microsoft.com/en-us/download) 可离线下载，也可在线安装
## 1.2 安装插件
-	基础插件：`chinese`、`C/C++`、`C/C++ Themes`、`Git`等
-	专用插件：`Cortex-Debug`、`Embedded IDE`
## 1.3 配置工具
-	语法检查：`cppcheck`
-	标准编译工具链：`GNU Arm EMbedded Toolchain`
-	调试工具：`OpenOCD Programmer`
-	STM32独有工具：`STM32 Cube Programer`(可选，未使用)

## 1.4 配置环境
-	`ARMCC5`：配置`ARMCC`的绝对路径
-	`ARMCC6`：配置`ARMCLANG`的绝对路径
-	`Axf To Elf`配置有效
# 2.Keil项目移植

## 2.1 代码迁移
-	导入项目，选择`MDK Arm`
## 2.2 环境Jlink
```
{
    // 使用 IntelliSense 了解相关属性。 
    // 悬停以查看现有属性的描述。
    // 欲了解更多信息，请访问: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [

        {
            "name": "Arm Debugger",
            "type": "arm-debugger",
            "request": "launch",
            "serialNumber": "${command:device-manager.getSerialNumber}",
            "program": "${command:arm-debugger.getApplicationFile}",
            "cmsisPack": "${command:cmsis-csolution.getTargetPack}",
            "deviceName": "${command:cmsis-csolution.getDeviceName}",
            "processorName": "${command:cmsis-csolution.getProcessorName}"
        },
        {
            "cwd": "${workspaceFolder}",
            "executable": "./project_keil/gd32_project/build/dio_board/dio_for_gd32_board.elf",
            "name": "Debug with JLink",
            "request": "launch",
            "type": "cortex-debug",
            "device": "GD32F407ZG",
            "runToEntryPoint": "main",
            "showDevDebugOutput": "raw",
            "servertype": "jlink",
            "svdFile": "./startup/GD32F4xx/GD32F4xx.svd",
			// "serialNumber": "601023607" // 如果有多个J - Link，填写此项目对应的J - Link序列号
        }
    ]
}
```
# 3.其他问题

## Vscode突然无法编译或者下载程序
以前能编译下载，某天更新后不能下载

-	关闭Vscode的自动更新功能，VScode`1.102.3`可以正常编译和下载。VScode`1.103.2`不能正常编译和下载
-	关闭`use task to build`可以编译，但不能下载 

