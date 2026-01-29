---
title: Keil µVision AC5 to AC6
author: Raymond
date: 2026-01-29
category: Tips
tags:
 - STM32
 - Keil µVision
layout: post
---

# ARM Keil µVision: AC5 to AC6 Porting & Debugging Guide

This document summarizes the process of porting an ARM Keil µVision project from ARM Compiler 5 (AC5) to ARM Compiler 6 (AC6). Successful testing was completed on January 24, 2026.

---

## 1. Overview
* **Target Hardware**: STM32F103RCT6 based Self-balancing Robot Driver Board.
* **Objective**: Migrate to AC6 to avoid commercial license requirements associated with AC5.
* **Key Result**: Resolved the single-side motor rotation issue and established real-time debugging via ST-Link v2.

---

## 2. Porting Procedure (AC5 → AC6)

### 2.1 CMSIS and Environment Cleanup
* **Delete Legacy Files**: Remove `system_stm32f10x.c`, `core_cm3.c`, and `startup_stm32f10x_hd.s` from the existing CMSIS folder.
* **Configure RTE**: Navigate to **Project > Manage > Run-Time Environment**.
    * **CMSIS > CORE**: Select Version 5.5.0.
    * **Device > Startup**: Select the System Startup for STM32F1xx series.

### 2.2 Target Options Configuration
* **Target Tab**: Set the ARM Compiler to **Version 6**.
* **Memory Settings**: Ensure **IROM1** is set to `0x8000000` (Size: `0x40000`) and **IRAM1** is set to `0x20000000` (Size: `0xC000`).
* **C/C++ (AC6) Tab**:
    * **Language C**: c99.
    * **Language C++**: c++11.
    * **Short enums/wchar**: Enabled.
    * **Optimization**: Set to **-O1**.

---

## 3. Critical Code Modifications

### 3.1 motor.c (Fixing Motor Rotation)
A milestone fix to solve the issue where only one motor rotates after porting:
* **Structure Initialization**: Use `memset` to clear configuration structures (e.g., `TIM_TimeBaseStructure`) to zero, as this is mandatory for AC6 migration.
* **PWM Output Enable**: For advanced timers like TIM8, call `TIM_CtrlPWMOutputs(TIM8, ENABLE)` to set the MOE bit.

### 3.2 usart.c (Retargeting printf)
* **Disable Semihosting**: Add `asm(".global __use_no_semihosting");` to avoid semihosting.
* **fputc Modification**: Redefine `fputc` to check `USART_FLAG_TC` (Transmission Complete) status for stable transmission in AC6.

---

## 4. Upload & Debugging

### 4.1 Uploading via FlyMcu
* **Port Settings**: FlyMcu typically recognizes **COM1 to COM4**; change the port in Device Manager if it exceeds this range.
* **Options**: Use 115200 bps and ensure 'Verify' and 'Run After ISP complete' are selected.

### 4.2 Real-time Debugging (ST-Link v2)
1. **Define Symbol**: Add `DEBUG` to the Preprocessor Symbols under Target Options.
2. **External Power**: Turn on the external power source.
3. **Session**: Click the **Start/Stop Debug Session** (d icon).
4. **Verification**: Set breakpoints in `app_Bluetooth.c`, run with **F5**, and verify Bluetooth data reception.

