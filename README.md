# HMI Integrated Conveyor System

An industrial automation project developed using **OpenPLC Ladder Logic** and integrated with a **Node-RED HMI** through **Modbus TCP**.

The system simulates an automated conveyor process in which products are transported, detected, evaluated against weight and quality criteria, rejected when non-compliant, and counted into production batches.

The project demonstrates practical implementation of PLC control logic, safety interlocking, sensor processing, timers, counters, product rejection, batch management, industrial communication, and HMI-based monitoring and control.

---

## Project Overview

The system is designed around an automated conveyor sorting and batching process.

The PLC is responsible for the core control functions, including:

- Conveyor motor control
- Physical and HMI-based start/stop commands
- Emergency-stop and overload interlocking
- Product detection
- Product weight processing and validation
- Quality inspection
- Non-standard product rejection
- Accepted-product counting
- Batch completion indication

A Node-RED HMI provides an operator interface for interacting with and monitoring the PLC system. Communication between Node-RED and OpenPLC is implemented using **Modbus TCP**.

The project therefore combines the control layer and supervisory interface into a small-scale industrial automation architecture.

---

## System Architecture

```text
                    ┌─────────────────────────┐
                    │       Node-RED HMI       │
                    │                         │
                    │  • Start / Stop         │
                    │  • Motor Status         │
                    │  • Safety Status        │
                    │  • Batch Monitoring     │
                    └────────────┬────────────┘
                                 │
                              Modbus TCP
                                 │
                    ┌────────────▼────────────┐
                    │         OpenPLC          │
                    │                         │
                    │      Ladder Logic       │
                    │                         │
                    │  • Motor Control        │
                    │  • Safety Interlocks    │
                    │  • Product Detection    │
                    │  • Weight Validation    │
                    │  • Quality Inspection   │
                    │  • Reject Control       │
                    │  • Batch Counting       │
                    └────────────┬────────────┘
                                 │
                    ┌────────────┴────────────┐
                    │                         │
                 Inputs                    Outputs
                    │                         │
              ┌─────┴─────┐             ┌─────┴─────┐
              │ Sensors   │             │ Actuators │
              │           │             │           │
              │ Product   │             │ Conveyor  │
              │ Quality   │             │ Motor     │
              │ Weight    │             │ Reject    │
              │ E-Stop    │             │ Solenoid  │
              │ Overload  │             │ Indicators│
              └───────────┘             └───────────┘
```
			  
---

## PLC Control Logic

The control system was developed in **OpenPLC** using **Ladder Logic**. The program is divided into functional sections, with each section responsible for a specific part of the automated conveyor process.

### 1. Motor Control Circuit

The motor control circuit manages the operation of the conveyor motor.

The motor can be initiated through the available start commands and remains controlled by the motor control logic until a stop condition or safety interlock interrupts operation.

![Motor Control Circuit](screenshots/OpenPLC/01-motor-control-circuit.PNG)

---

### 2. Motor Memory and Control Logic

A memory/latching section is used to maintain the required motor control state after the start command is activated.

This allows the momentary start command to initiate the motor operation while the control logic maintains the running state until a relevant stop condition occurs.

![Motor Memory Circuit](screenshots/OpenPLC/02-motor-memory-circuit.PNG)

---

### 3. Sub-Standard Product Rejection

The rejection section handles products that do not meet the required processing criteria.

The PLC uses the product evaluation results to initiate the rejection sequence and control the reject actuator.

![Sub-Standard Product Rejection](screenshots/OpenPLC/03-substandard-product-rejection.PNG)

---

### 4. Weight Conditioning and Validation

The system processes the raw weight input and converts it into a usable weight value for product evaluation.

The calculated weight is then evaluated against configured minimum and maximum limits to determine whether the product satisfies the required weight condition.

![Weight Conditioning Circuit 1](screenshots/OpenPLC/04-weight-conditioning-1.PNG)

![Weight Conditioning Circuit 2](screenshots/OpenPLC/05-weight-conditioning-2.PNG)

The project uses a raw scale input together with a scale factor to calculate the actual product weight. The configured acceptable range is used as part of the product classification process.

---

### 5. Standard Product Batching

Products that satisfy the required conditions are processed as standard products and contribute to the production batch.

An edge-triggered counting mechanism is used so that a detected product contributes a controlled increment to the batch count.

![Standard Products Batching](screenshots/OpenPLC/06-standard-products-batching.PNG)

The batching section provides the basis for monitoring production quantity and determining when the configured batch quantity has been achieved.

---

## PLC Programming Techniques

The OpenPLC program demonstrates several common industrial control techniques:

- Ladder Logic programming
- Motor start/stop control
- Memory and latching logic
- Safety interlocking
- Timer-based control
- Rising-edge detection
- Counter implementation
- Sensor-based sequencing
- Analog value processing
- Limit comparison
- Product classification
- Reject actuator control
- Batch counting

# Node-RED Integration

The OpenPLC control system is integrated with **Node-RED** to provide a Human Machine Interface (HMI) for operator control and real-time process monitoring.

Communication between Node-RED and OpenPLC is implemented using **Modbus TCP** over **port 5020**.

The integration was successfully tested, with Node-RED exchanging control commands and process information with the OpenPLC ladder logic through the configured Modbus TCP connection.

This creates a communication path between the PLC control layer and the operator interface:

```text
┌─────────────────────────┐
│      Node-RED HMI       │
│                         │
│  Operator Controls      │
│  Status Indicators      │
│  Process Monitoring     │
└────────────┬────────────┘
             │
             │ Modbus TCP
             │ Port 5020
             │
┌────────────▼────────────┐
│         OpenPLC         │
│                         │
│      Ladder Logic       │
│                         │
│  Control & Processing   │
└─────────────────────────┘
```
---

# Human Machine Interface (HMI)

A **Node-RED dashboard** was developed to provide the operator with a graphical interface for controlling and monitoring the conveyor system.

The HMI communicates with the OpenPLC control system through the configured **Modbus TCP connection on port 5020**.

## Motor Stopped State

The stopped-state dashboard provides the operator with a visual indication that the conveyor motor is not running.

![HMI Motor Stopped State](screenshots/HMI/01-hmi-motor-stopped-state.PNG)

---

## Motor Running State

When the motor is started and the required control conditions are satisfied, the HMI reflects the running state of the conveyor.

![HMI Motor Running State](screenshots/HMI/02-hmi-motor-running-state.PNG)

---

## Operator Control Workflow

The basic HMI control path is:

```text
Operator
    │
    ▼
Node-RED HMI
    │
    ▼
Modbus TCP
Port 5020
    │
    ▼
OpenPLC
    │
    ▼
Ladder Logic
    │
    ▼
Motor Control
    │
    ▼
HMI Status Feedback	
```
---

# Modbus TCP Communication

Communication between the OpenPLC controller and Node-RED HMI is implemented using **Modbus TCP**.

The configured communication port for the project is **5020**.

Modbus TCP provides the interface through which Node-RED can send commands to the PLC and read process information and status values from the control system.

## Communication Roles

| System | Role |
|---|---|
| OpenPLC | PLC control and process logic |
| Node-RED | HMI and supervisory interface |
| Modbus TCP | Communication protocol |
| Port | 5020 |

## PLC Variables Used for HMI Integration

The project exposes selected PLC variables for interaction with the Node-RED HMI.

| Variable | Address | Type | Function |
|---|---|---|---|
| `HMI_MOTOR_CMD` | `%MX0.0` | BOOL | HMI motor start command |
| `HMI_STOP_CMD` | `%MX0.1` | BOOL | HMI motor stop command |
| `BATCH_COUNT_VALUE` | `%MW0` | INT | Batch count value |
| `MOTOR_RUN` | `%QX0.0` | BOOL | Motor running status |
| `LAMP_RUN` | `%QX0.1` | BOOL | Running indicator |
| `PRODUCT_SENSOR` | `%IX0.4` | BOOL | Product detection input |

## Control Communication

The HMI sends operator commands from Node-RED to the corresponding PLC variables.

```text
Node-RED HMI
      │
      │ Modbus TCP
      ▼
HMI_MOTOR_CMD / HMI_STOP_CMD
      │
      ▼
OpenPLC Ladder Logic
      │
      ▼
Motor Control	
```
---

# Project Structure

The repository is organized to separate the PLC program, HMI implementation, documentation, and visual project evidence.

```text
Automated Conveyor Sorting & Batching System
│
├── assets
│
├── documentation
│
├── Node-RED
│   └── flows.json
│
├── OpenPLC
│   └── ACBSS.xml
│
├── screenshots
│   ├── OpenPLC
│   │   ├── 01-motor-control-circuit.PNG
│   │   ├── 02-motor-memory-circuit.PNG
│   │   ├── 03-substandard-product-rejection.PNG
│   │   ├── 04-weight-conditioning-1.PNG
│   │   ├── 05-weight-conditioning-2.PNG
│   │   └── 06-standard-products-batching.PNG
│   │
│   ├── Node-RED
│   │   └── 01-complete-nodered-flow.PNG
│   │
│   └── HMI
│       ├── 01-hmi-motor-stopped-state.PNG
│       └── 02-hmi-motor-running-state.PNG
│
└── README.md	  