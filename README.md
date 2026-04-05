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

All traffic was generated locally using: `127.0.0.1:1502`



This allowed me to safely simulate OT communication without interacting with any real network.

---

## Modbus Server Setup (ModbusPal)

I created a simulated device:

- Name: PLC1  
- Unit ID: 1  
- Configured:
  - Holding Registers
  - Coils

![ModbusPal Setup](images/modbuspal-setup.png)

---

## Client Connection (QModMaster)

Connected to the local Modbus server:

- Host: 127.0.0.1  
- Port: 1502  

![QModMaster Connected](images/qmodmaster-connected.png)

---

## Generating Traffic

### Read Operation (Normal Behavior)

- Function Code: 3 (Read Holding Registers)
- Used to retrieve data
- Does not modify system behavior

---

### Write Operation (Suspicious Behavior)

- Function Code: 6 (Write Single Register)
- Register: 0  
- Value: 65535  

![QModMaster Write](images/qmodmaster-write.png)

---

## Packet Capture (Wireshark)

Captured traffic using loopback interface with filter:



![Wireshark Capture](images/wireshark-capture.png)

---

## Key Finding

The most important observation was a write operation modifying a register:

- Function Code: 6  
- Register Number: 0  
- Value: 65535  

![Wireshark FC6](images/wireshark-fc6.png)

---

## Analysis

Function Code 6 represents a write operation, which directly changes system behavior.

The value **65535** is the maximum value for a 16-bit unsigned integer. In a real system, writing this value could:

- Push thresholds beyond safe limits  
- Disrupt system logic  
- Cause instability depending on what the register controls  

Since Modbus does not verify the source of commands, any device with network access could send this request.

---

## Security Risks

- No authentication  
- No encryption  
- No integrity validation  

This allows:
- Unauthorized reads  
- Unauthorized writes  
- Potential manipulation of critical systems  

---

## Detection Approach

From a monitoring perspective, this type of activity should be flagged.

Key detection ideas:

- Monitor for Function Codes:
  - 5 (Write Coil)
  - 6 (Write Register)

- Alert on:
  - Unexpected write activity  
  - Abnormal values (like 65535)  
  - Unknown source systems  

---

## Real-World Relevance

In environments like data centers, Modbus may be used for:

- Cooling systems  
- Power monitoring  
- Environmental controls  

A write operation like this could impact system stability or availability.

---

## Conclusion

This lab gave me hands-on experience with:

- Modbus protocol behavior  
- Packet-level analysis in Wireshark  
- Identifying high-risk write operations  
- Thinking through detection strategies  

It also reinforced how important monitoring is in OT environments where protocols were not designed with security in mind.

---
