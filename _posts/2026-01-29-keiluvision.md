---
title: Keil 
author: Raymond
date: 2026-01-29
category: Tips
tags:
 - STM32
 - Keil µVision
layout: post
---

# ARM Keil µVision: AC5 to AC6 Porting & Debugging Guide

[cite_start]This document summarizes the process of porting an ARM Keil µVision project from ARM Compiler 5 (AC5) to ARM Compiler 6 (AC6)[cite: 1, 2, 3]. [cite_start]Successful testing was completed on January 24, 2026[cite: 55, 59].

---

## 1. Overview
* [cite_start]**Target Hardware**: STM32F103RCT6 based Self-balancing Robot Driver Board[cite: 52, 221].
* [cite_start]**Objective**: Migrate to AC6 to avoid commercial license requirements associated with AC5[cite: 215].
* [cite_start]**Key Result**: Resolved the single-side motor rotation issue and established real-time debugging via ST-Link v2[cite: 486, 646].



---

## 2. Porting Procedure (AC5 → AC6)

### 2.1 CMSIS and Environment Cleanup
* [cite_start]**Delete Legacy Files**: Remove `system_stm32f10x.c`, `core_cm3.c`, and `startup_stm32f10x_hd.s` from the existing CMSIS folder[cite: 64, 72, 73, 74].
* [cite_start]**Configure RTE**: Navigate to **Project > Manage > Run-Time Environment**[cite: 64].
    * [cite_start]**CMSIS > CORE**: Select Version 5.5.0[cite: 90, 91].
    * [cite_start]**Device > Startup**: Select the System Startup for STM32F1xx series[cite: 120, 122].

### 2.2 Target Options Configuration
* [cite_start]**Target Tab**: Set the ARM Compiler to **Version 6**[cite: 224, 225].
* [cite_start]**Memory Settings**: Ensure **IROM1** is set to `0x8000000` (Size: `0x40000`) and **IRAM1** is set to `0x20000000` (Size: `0xC000`)[cite: 282, 284].
* **C/C++ (AC6) Tab**:
    * [cite_start]**Language C**: c99[cite: 375].
    * [cite_start]**Language C++**: c++11[cite: 376].
    * [cite_start]**Short enums/wchar**: Enabled[cite: 379].
    * [cite_start]**Optimization**: Set to **-O1** (Note: -O1 is more stable than -O0 for this port)[cite: 647, 666].

---

## 3. Critical Code Modifications

### 3.1 motor.c (Fixing Motor Rotation)
[cite_start]A milestone fix to solve the issue where only one motor rotates after porting[cite: 485, 486]:
* [cite_start]**Structure Initialization**: Use `memset` to clear configuration structures (e.g., `TIM_TimeBaseStructure`) to zero, as this is mandatory for AC6 migration[cite: 499, 500].
* [cite_start]**PWM Output Enable**: For advanced timers like TIM8, call `TIM_CtrlPWMOutputs(TIM8, ENABLE)` to set the MOE bit[cite: 523, 524].

### 3.2 usart.c (Retargeting printf)
* [cite_start]**Disable Semihosting**: Add `asm(".global __use_no_semihosting");` to avoid semihosting[cite: 176, 537].
* [cite_start]**fputc Modification**: Redefine `fputc` to check `USART_FLAG_TC` (Transmission Complete) status for stable transmission in AC6[cite: 209, 544].

---

## 4. Upload & Debugging

### 4.1 Uploading via FlyMcu
* [cite_start]**Port Settings**: FlyMcu typically recognizes **COM1 to COM4**; change the port in Device Manager if it exceeds this range[cite: 575, 576].
* [cite_start]**Options**: Use 115200 bps and ensure 'Verify' and 'Run After ISP complete' are selected[cite: 578, 586, 587].

### 4.2 Real-time Debugging (ST-Link v2)
1. [cite_start]**Define Symbol**: Add `DEBUG` to the Preprocessor Symbols under Target Options[cite: 647, 662].
2. [cite_start]**External Power**: Turn on the external power source (OLED screen will light up)[cite: 702, 708].
3. [cite_start]**Session**: Click the **Start/Stop Debug Session** (d icon)[cite: 705].
4. [cite_start]**Verification**: Set breakpoints in `app_Bluetooth.c`, run with **F5**, and verify Bluetooth data reception[cite: 704, 706, 707].

---
[cite_start]*Created by: Raymond, 2026-01-25* [cite: 59]