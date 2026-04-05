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

<img width="959" height="431" alt="Modbus-setup" src="https://github.com/user-attachments/assets/7465db2c-686d-4e2a-becc-2cda10235985" />


---

## Client Connection (QModMaster)

Connected to the local Modbus server:

- Host: 127.0.0.1  
- Port: 1502  

<img width="417" height="321" alt="QModMaster-connected" src="https://github.com/user-attachments/assets/1011bc82-f630-49ff-94ab-e23fe664c84d" />


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

<img width="412" height="317" alt="QModMaster-write06" src="https://github.com/user-attachments/assets/e26cf5c5-8b00-420b-836b-8b4ca8e56da4" />


---

## Packet Capture (Wireshark)

Captured traffic using loopback interface with filter:



<img width="956" height="560" alt="Wireshark-capture" src="https://github.com/user-attachments/assets/635b106e-e549-4c4a-9800-89fe1ca446d9" />


---

## Key Finding

The most important observation was a write operation modifying a register:

- Function Code: 6  
- Register Number: 0  
- Value: 65535  

<img width="959" height="563" alt="Wireshark-fc6" src="https://github.com/user-attachments/assets/f016e1ed-49fb-4da5-9158-a6764a5091a7" />


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
