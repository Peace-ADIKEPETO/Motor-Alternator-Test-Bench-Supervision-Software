# Real-Time Motor Supervision System (Qt 6)

An industrial-grade Qt 6 / C++ desktop application developed for real-time motor testbed supervision, data acquisition, dynamic sensor discovery, and post-test analytics via Modbus RTU / RS485 communication. Uses a dynamic build (`.dll`) of QCustomPlot for interactive, high-performance visual plotting.

## Application Interface

![Main Supervision Interface](assets/p0.png)
*Real-time multi-channel plotting tab displaying active motor sensor channels, custom trace settings, dynamic axis scaling, and embedded sensor documentation.*
![Review mode](assets/p1.png)
![Review mode](assets/test1.png)
*Scanner Dialog*
![Scanner Dialog](assets/p2.png)
*Add Sensor Dialog*
![Add Sensor Dialog](assets/p3.png)

---

## Key Implemented Features

* **Hardware & Bus Discovery (Modbus Poll Equivalent)**:
  * **Built-in Port & Slave Scanner**: Automatic serial COM port detection and dynamic scanning for active Modbus RTU slave node IDs.
  * **Dynamic Sensor Provisioning**: Interface to define new sensors on-the-fly by setting Modbus function codes, register offsets, word/byte orders (`CDAB`, `ABCD`, etc.), and custom measurement channels.
* **Multi-Sensor Live Plotting (QCustomPlot DLL)**:
  * High-frequency real-time graphing with independent Y-axes per physical quantity (Torque, Speed, Power).
  * Interactive plot manipulation: live panning, region zooming, and dynamic axis rescaling.
  * Customizable visual properties (color picker, line thickness `lineWidth` from 0.5 to 6.0) with real-time trace visibility toggles.
* **Post-Test Review Mode & Curve Analysis**:
  * Historical session rehydration from SQLite databases (`data.sqlite`) for offline curve inspection and data interpretation.
  * **Integrated Session Notepad**: Built-in persistent notepad tied directly to each recording session for recording test notes, operating conditions, and diagnostic comments.
* **Embedded Sensor Documentation**:
  * Integrated document selector (`.pdf`, `.doc`, `.docx`, `.txt`) during sensor configuration.
  * Direct "Open Manual" button integrated into the runtime UI via `QDesktopServices`.
  * Fully portable relative path storage (`docs/<SensorName>/`) within the session directory.
* **Fault-Tolerant Session Persistence**:
  * Continuous metadata saving (`session.json`) immediately upon sensor attachment to prevent configuration loss during power outages or unexpected application exits.
  * Asynchronous background SQLite logging to preserve UI rendering performance under high polling rates.

---

## Hardware Architecture & Communication Protocol

### 1. Hardware Integration
* **Serial Infrastructure**: Host PC connected via USB-to-RS485 converters or Waveshare industrial multi-channel serial modules.
* **Target Transducers**: Measures physical parameters from industrial Modbus RTU sensors (e.g., GTS208 Torque/Speed/Power sensor).

### 2. Modbus RTU Protocol & Register Mapping
* **Serial Settings**: Configurable baud rates (default 115200 bps), 8 Data Bits, 1 Stop Bit, No Parity.
* **Function Code**: Read Holding Registers (`0x03`).
* **32-Bit IEEE 754 Decoding**: Reads contiguous 16-bit register pairs and decodes them into single-precision floats with configurable word configurations (`CDAB`, `ABCD`, `BADC`, `DCBA`).
* **Block Polling**: Continuous register block polling in optimized single requests to minimize bus latency.

---

## Technical Stack & Dependencies

* **Framework**: Qt 6.11.2 (`core`, `gui`, `widgets`, `serialport`, `modbus`, `sql`, `printsupport`)
* **Plotting Engine**: QCustomPlot 2.1.1 (Shared Library)
* **Build System**: qmake (`.pro`)
* **Compiler**: C++17 compliant compiler (MinGW 64-bit)

---

## Project Configuration (`Monitor.pro`)

```qmake
QT += core gui widgets serialport serialbus printsupport sql

CONFIG += c++17

DEFINES += QCUSTOMPLOT_USE_LIBRARY

INCLUDEPATH += $$PWD/qcustomplot_lib
LIBS += -L$$PWD/qcustomplot_lib -lqcustomplot

SOURCES += \
    addsensordialog.cpp \
    main.cpp \
    monitor.cpp \
    scanner.cpp \
    sensorplottab.cpp \
    sessionmanager.cpp

HEADERS += \
    addsensordialog.h \
    modbus_float_decode.h \
    monitor.h \
    scanner.h \
    sensorconfig.h \
    sensorplottab.h \
    sessionmanager.h \
    sessionmetadata.h

FORMS += \
    addsensordialog.ui \
    monitor.ui \
    scanner.ui