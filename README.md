# Modbus OT Security Lab – Traffic Analysis & Write Detection

## Overview

I built this lab to get hands-on experience with OT protocols, specifically Modbus, and to understand how insecure communication can impact real-world systems.

Modbus is widely used in environments like data centers, BMS, and industrial systems, but it does not include authentication or encryption. Because of that, it allows devices to read and write values without verifying the source.

The goal of this project was to:
- Simulate a Modbus environment
- Capture real Modbus traffic
- Identify risky behavior (write operations)
- Understand how this would look from a detection standpoint

---

## Lab Setup

### Tools Used

- ModbusPal (Modbus server / PLC simulation)
- QModMaster (Modbus client)
- Wireshark (packet capture and analysis)

---

### Architecture

All traffic was generated locally using:
