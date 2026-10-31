# Motor-Alternator Test Bench Supervision Software

Supervision and data-logging application for a motor-alternator test bench, written in C++ with Qt 6. It reads measurement instruments over Modbus RTU on an RS-485 bus, plots the values live on a multi-axis graph, and stores each measurement session so it can be reviewed later.

> **Source code not published.**
> This work was carried out under a confidentiality clause with the host company. For that reason the source code is not available in this repository, which contains documentation only. Nothing in this repository grants a right to use, copy or redistribute the software.

## Context

The software was developed during an internship as part of the Electrical Engineering program (Industrial Process Control) at EPAC, Benin. The bench consists of an electric motor driving an alternator through a torque sensor mounted on the coupling shaft. The goal was a desktop tool that a test technician can use to follow torque, speed, power and electrical quantities during a test, and to keep a record of each test.

## How the work progressed

The project was built in three stages, each one reusing what the previous one had taught.

1. **Servo motor control from Qt.** An Arduino Uno driving an SG90 servo, controlled from a Qt application through `QSerialPort`. This stage covered opening and configuring a serial port, sending commands, reading confirmations asynchronously, and plotting requested versus confirmed angle over time.
2. **Stepper motor control and multi-quantity plotting.** A NEMA 17 stepper with an A4988 driver, commanded from Qt. Speed, current, temperature and a torque value computed from the current were plotted on the same graph with `QCustomPlot`, each on its own vertical axis.
3. **Modbus RTU supervision of the test bench.** The same communication and plotting techniques applied to industrial instruments on an RS-485 bus, with session management, storage and export.

## Main features

- Detection of serial ports and a two-phase Modbus scan: the first phase finds the connection parameters (baud rate, parity, stop bits, slave address), the second finds the function code and register address that answer.
- Live plotting with one vertical axis per quantity, zoom, grid, per-curve show/hide, and adjustable color and line width.
- One Modbus client per RS-485 channel, sending one request at a time on a given bus.
- Sessions stored as self-contained folders: a SQLite database, a metadata file, free-text notes and on-demand exports.
- Review mode to reopen a past session and display its recorded measurements without any instrument connected.
- Export of measurements to CSV and of the graph to PDF or PNG.
- Interface available in French and English, switchable at run time.
- Default sessions folder that can be changed from the application.

## Technology

| Area | Choice |
|---|---|
| Language | C++17 |
| Framework | Qt 6 (Widgets, Serial Port, Serial Bus, SQL, Print Support) |
| Plotting | QCustomPlot, built as a shared library |
| Storage | SQLite, one database per session |
| Build system | qmake |
| Field bus | Modbus RTU over RS-485, through a USB multi-channel adapter |
| Diagnostic tools | Modbus Poll, DocKlight |
| Installer | Inno Setup |

## Design notes

- A sensor is described in two separate parts: what it is (channels, decoding, display settings) and how it is reached at a given moment (port, baud rate, slave address). The first part is reusable from one session to the next; the second belongs to the session, since wiring can change.
- Each session folder can be copied, archived or opened on its own. No shared central database is needed.
- Register decoding is centralized and driven by a per-channel description, because a wrong word order for a 32-bit float gives a plausible but incorrect value without any error.

## Status

- Working: device discovery, live plotting, session storage, review mode, CSV/PDF/PNG export, bilingual interface, pause and resume of recording, editing a sensor's connection parameters during a session, configurable default sessions folder, installer.
- Not yet integrated: two of the energy meters planned for the bench did not answer on their channels during diagnosis, and remain to be commissioned.

## Access and contact

Access to the source code depends on the host company's authorization and cannot be granted from this repository. 

## Author

Peace Georland ADIKPETO
Electrical Engineering student, École Polytechnique d'Abomey-Calavi (EPAC), Benin

