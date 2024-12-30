---
title: Vitis Application Acceleration (Emulation)
date: 2024-12-27 22:54:00 +0800
categories: [Architecture, FPGA]
tags: [vitis application acceleration, fpga, software/hardware co-design]     # TAG names should always be lowercase
pin: false
math: false
mermaid: false
---

> This tutorial uses Vitis 2022.1 version.
{: .prompt-info }

## Background

### Why Vitis Emulation Flow?

Programming actual FPGA hardware directly is time consuming and prone to fault.
If we successfully achieve the expected results through emulation, we can be confident that it will likely work on actual hardware.
Additionally, emulation targets provide full visibility into the application or accelerator, thus making it easier to perform debugging.

## Vitis Platform Creation

Follow [Vitis Custom Embedded Platform Creation Example on ZCU104](https://github.com/bupthpc/Vitis-Tutorials/blob/2022.1/Vitis_Platform_Creation/Design_Tutorials/02-Edge-AI-ZCU104/README.md).

## References

- [Vitis Unified Software Platform Documentation: Application Acceleration Development (UG1393)](https://docs.amd.com/r/2022.1-English/ug1393-vitis-application-acceleration)
- [AMD Vitis™ In-Depth Tutorials at BUPT HPC's fork](https://github.com/bupthpc/Vitis-Tutorials)
