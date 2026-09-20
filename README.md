# Arduino Nicla Vision: Hardware Setup, Sensor Validation & PDM Microphone Testing

A hands-on embedded systems project using the **Arduino Nicla Vision**, focusing on hardware configuration, onboard sensor validation, PDM microphone audio acquisition, and real-time sound visualization.

This project is inspired by the [MLSysBook Arduino Nicla Vision Setup Guide](https://mlsysbook.ai/kits/contents/arduino/nicla_vision/setup/setup.html).

> **Project Status:** Arduino serial communication and the basic Serial Plotter test have been verified. Microphone testing and the remaining sensor experiments require hardware validation.

---

## Table of Contents

1. [Project Overview](#project-overview)
2. [Objectives](#objectives)
3. [Hardware Requirements](#hardware-requirements)
4. [Arduino Nicla Vision Specifications](#arduino-nicla-vision-specifications)
5. [Software Requirements](#software-requirements)
6. [System Architecture](#system-architecture)
7. [Arduino IDE Installation](#arduino-ide-installation)
8. [Board Configuration](#board-configuration)
9. [Serial Communication Test](#serial-communication-test)
10. [PDM Microphone Testing](#pdm-microphone-testing)
11. [Complete Arduino Code](#complete-arduino-code)
12. [Expected Output](#expected-output)
13. [Troubleshooting](#troubleshooting)
14. [Additional Sensor Experiments](#additional-sensor-experiments)
15. [Future Improvements](#future-improvements)
16. [Learning Outcomes](#learning-outcomes)
17. [References](#references)

---

# Project Overview

The Arduino Nicla Vision is a compact embedded development platform designed for edge artificial intelligence, computer vision, audio processing, and environmental sensing.

It integrates a high-performance microcontroller and several onboard sensors, making it suitable for developing intelligent embedded applications.

This project focuses on:

- Configuring the Arduino Nicla Vision development environment.
- Establishing USB serial communication.
- Testing the built-in PDM microphone.
- Acquiring digital audio samples.
- Processing microphone data.
- Visualizing sound amplitude using Arduino Serial Plotter.
- Troubleshooting firmware upload and debugging errors.
- Exploring future TinyML applications.

The primary experiment is **real-time sound-level visualization using the onboard PDM microphone**.

---

# Objectives

The objectives of this project are:

1. Understand the architecture of Arduino Nicla Vision.
2. Install and configure the appropriate Arduino board package.
3. Establish reliable communication between the board and computer.
4. Interface with the integrated PDM microphone.
5. Capture digital audio samples using the PDM library.
6. Process sampled audio to calculate peak-to-peak amplitude.
7. Display the sound-level measurements using Serial Plotter.
8. Explore additional onboard sensors.
9. Prepare the platform for future TinyML development.

---

# Hardware Requirements

| Component | Description |
|---|---|
| Development Board | Arduino Nicla Vision |
| Microcontroller | STM32H747 |
| Microphone | Built-in digital MEMS PDM microphone |
| USB Cable | Micro-USB data cable |
| Computer | Windows, macOS, or Linux |
| External Sensors | Not required |
| Breadboard | Not required |

**Note:** The Arduino Nicla Vision already contains a built-in microphone. No external microphone connections are required.

---

# Arduino Nicla Vision Specifications

| Feature | Specification |
|---|---|
| Microcontroller | STM32H747 |
| Processor | Dual-core Arm Cortex-M7 and Cortex-M4 |
| Camera | 2 MP GC2145 |
| Microphone | MP34DT05 digital MEMS microphone |
| Motion Sensor | LSM6DSOX 6-axis IMU |
| Distance Sensor | VL53L1CBV0FY Time-of-Flight |
| Connectivity | Wi-Fi and Bluetooth Low Energy |
| Programming | Arduino IDE / OpenMV |
| Applications | TinyML, computer vision, audio processing |

The onboard microphone captures sound through a digital Pulse Density Modulation (PDM) interface.

The PDM library provides audio samples that can be processed and visualized.

---

# Software Requirements

Install the following software:

| Software | Purpose |
|---|---|
| Arduino IDE 2.x | Firmware development |
| Arduino Mbed OS Nicla Boards | Board support package |
| PDM Library | Microphone communication |
| Arduino Serial Monitor | Serial data inspection |
| Arduino Serial Plotter | Waveform visualization |

### Download Arduino IDE

https://www.arduino.cc/en/software

### Reference Setup Guide

https://mlsysbook.ai/kits/contents/arduino/nicla_vision/setup/setup.html

---

# System Architecture

The microphone test follows this signal-processing pipeline:

```text
             Acoustic Sound
                   |
                   v
        Built-in MEMS Microphone
                   |
                   v
           PDM Audio Signal
                   |
                   v
        Arduino PDM Library
                   |
                   v
          PCM Audio Samples
                   |
                   v
       Audio Buffer Processing
                   |
                   v
    Peak-to-Peak Amplitude Calculation
                   |
                   v
          USB Serial Output
                   |
                   v
        Arduino Serial Plotter
                   |
                   v
       Real-Time Sound Visualization
```

The application measures sound amplitude from each received buffer.

It does not calculate calibrated sound pressure level (dB SPL).

---

# Arduino IDE Installation

## Step 1: Install Arduino IDE

Download and install the latest compatible version of Arduino IDE.

Open Arduino IDE after installation.

## Step 2: Connect Arduino Nicla Vision

Connect the board to the computer using a Micro-USB data cable.

Make sure the cable supports data transfer.

A power-only USB cable will not establish serial communication.

## Step 3: Install the Board Package

Open:

```text
Tools → Board → Boards Manager
```

Search for:

```text
Arduino Mbed OS Nicla Boards
```

Install the package.

Wait until installation completes.

## Step 4: Select the Board

Navigate to:

```text
Tools → Board → Arduino Mbed OS Nicla Boards
```

Select:

```text
Arduino Nicla Vision
```

## Step 5: Select the Serial Port

Navigate to:

```text
Tools → Port
```

Select the port associated with the Nicla Vision.

Example:

```text
COM5
```

The COM port may change after resetting or uploading firmware.

Always confirm the current port.

---

# Board Configuration

Use the following settings:

| Parameter | Configuration |
|---|---|
| Board | Arduino Nicla Vision |
| Board Package | Arduino Mbed OS Nicla Boards |
| Port | Current Nicla Vision COM port |
| Upload Method | Standard Upload |
| Serial Baud Rate | 115200 |
| Audio Sample Rate | 16000 Hz |
| Audio Channels | Mono |

**Important:** Use the standard Upload button.

Do not select Start Debugging for the microphone experiment.

---

# Serial Communication Test

Before testing the microphone, verify communication between the Nicla Vision and the computer.

Create a new Arduino sketch.

Upload the following code:

```cpp
void setup()
{
    Serial.begin(115200);
}

void loop()
{
    static int value = 0;

    Serial.println(value);

    value += 5;

    if (value > 100)
    {
        value = 0;
    }

    delay(20);
}
```

## Testing Procedure

1. Upload the sketch.
2. Open Arduino Serial Monitor.
3. Select 115200 baud.
4. Confirm that numerical values appear.
5. Close Serial Monitor.
6. Open Arduino Serial Plotter.

Expected behavior:

```text
0
5
10
15
20
25
...
100
0
5
10
...
```

The Serial Plotter should display a repeating rising waveform.

### Test Result

The basic serial plotting experiment has been successfully verified during this project's initial testing.

This confirms that the board can transmit numerical data to the Arduino Serial Plotter.

---

# PDM Microphone Testing

## Introduction

The Arduino Nicla Vision contains an integrated digital MEMS microphone.

The microphone uses Pulse Density Modulation (PDM) for audio acquisition.

The Arduino PDM library converts microphone data into audio samples that can be processed by the microcontroller.

This experiment measures the peak-to-peak amplitude of each audio buffer.

The amplitude is calculated using:

```text
Peak-to-Peak Amplitude = Maximum Sample - Minimum Sample
```

Higher amplitude generally indicates larger variations in the captured audio signal.

Actual values depend on the acoustic environment, microphone gain, and sound source.

---

## Step 1: Open the Official Example

In Arduino IDE, navigate to:

```text
File → Examples → PDM → PDMSerialPlotter
```

Example availability may depend on the installed board package.

Alternatively, use the official Arduino example:

https://github.com/arduino/ArduinoCore-mbed/blob/main/libraries/PDM/examples/PDMSerialPlotter/PDMSerialPlotter.ino

Upload the example using the standard Upload button.

For the unmodified official example, use the serial baud rate specified in its code, which is 9600.

Speak or clap near the board.

Observe whether the waveform changes.

---

## Step 2: Test the Custom Sound-Level Plotter

The following implementation provides a simplified method for monitoring the microphone.

It:

- Initializes the PDM microphone.
- Captures audio samples.
- Stores samples in a buffer.
- Calculates minimum and maximum sample values.
- Computes peak-to-peak amplitude.
- Prints the result through USB serial.
- Displays sound-level variations in Serial Plotter.

The custom sketch uses:

```text
Audio Sample Rate: 16000 Hz
Channels: 1
Serial Baud Rate: 115200
```

---

# Complete Arduino Code

Create a folder named:

```text
NiclaMicLevelPlotter
```

Save the following sketch as:

```text
NiclaMicLevelPlotter.ino
```

### Source Code

```cpp
/*
  Project:
  Arduino Nicla Vision PDM Microphone
  Sound-Level Plotter

  Board:
  Arduino Nicla Vision

  Microphone:
  Built-in PDM microphone

  Sample Rate:
  16000 Hz

  Serial Baud Rate:
  115200

  Output:
  Peak-to-peak audio amplitude
*/

#include <PDM.h>

// Microphone configuration
const int sampleRate = 16000;

// Audio buffer configuration
const int bufferSize = 512;

// Receive buffer
short sampleBuffer[bufferSize];

// Processing buffer
short audioBuffer[bufferSize];

// Number of received samples
volatile int samplesRead = 0;

// Microphone callback
void onPDMdata()
{
    int bytesAvailable = PDM.available();

    if (bytesAvailable <= 0)
    {
        return;
    }

    // Limit data to available buffer size
    if (bytesAvailable > sizeof(sampleBuffer))
    {
        bytesAvailable = sizeof(sampleBuffer);
    }

    // Read microphone data
    PDM.read(sampleBuffer, bytesAvailable);

    // Convert bytes to number of samples
    samplesRead = bytesAvailable / sizeof(short);
}

void setup()
{
    // Initialize serial communication
    Serial.begin(115200);

    // Wait briefly for serial connection
    unsigned long startTime = millis();

    while (!Serial && millis() - startTime < 5000)
    {
    }

    // Register microphone callback
    PDM.onReceive(onPDMdata);

    // Initialize the microphone
    if (!PDM.begin(1, sampleRate))
    {
        Serial.println("PDM INIT FAILED");

        while (1)
        {
            delay(1000);
        }
    }
}

void loop()
{
    int count = 0;

    // Copy samples safely
    noInterrupts();

    count = samplesRead;

    if (count > 0)
    {
        for (int i = 0; i < count; i++)
        {
            audioBuffer[i] = sampleBuffer[i];
        }

        samplesRead = 0;
    }

    interrupts();

    if (count == 0)
    {
        return;
    }

    // Initialize minimum and maximum
    short minimumValue = audioBuffer[0];
    short maximumValue = audioBuffer[0];

    // Find minimum and maximum samples
    for (int i = 1; i < count; i++)
    {
        if (audioBuffer[i] < minimumValue)
        {
            minimumValue = audioBuffer[i];
        }

        if (audioBuffer[i] > maximumValue)
        {
            maximumValue = audioBuffer[i];
        }
    }

    // Calculate peak-to-peak amplitude
    int peakToPeak =
        (int)maximumValue - (int)minimumValue;

    // Send amplitude to Serial Plotter
    Serial.println(peakToPeak);
}
```

### Code Explanation

**1. PDM Library**

```cpp
#include <PDM.h>
```

Provides the interface for microphone initialization and audio acquisition.

**2. Sampling Frequency**

```cpp
const int sampleRate = 16000;
```

Configures the requested sampling frequency to 16 kHz.

**3. Audio Buffer**

```cpp
short sampleBuffer[512];
```

Stores 16-bit audio samples received from the microphone.

**4. Callback Function**

```cpp
PDM.onReceive(onPDMdata);
```

Registers a callback to retrieve audio samples when microphone data becomes available.

**5. Microphone Initialization**

```cpp
PDM.begin(1, sampleRate);
```

Initializes the microphone in mono mode.

**6. Signal Processing**

```cpp
int peakToPeak =
    (int)maximumValue - (int)minimumValue;
```

Computes the amplitude range within the received audio buffer.

**7. Serial Output**

```cpp
Serial.println(peakToPeak);
```

Transmits the calculated amplitude to Arduino Serial Plotter.

### Implementation Limitation

This demonstration uses a single shared receive buffer. If the main loop cannot process data quickly enough, some samples may be overwritten.

The code is intended for basic microphone validation rather than lossless audio recording.

---

# Expected Output

## Test 1: Quiet Environment

Place the Nicla Vision in a quiet environment.

Expected behavior:

- Relatively small amplitude values.
- Minor fluctuations due to background noise.
- No large audio peaks unless environmental noise increases.

## Test 2: Speaking

Speak near the onboard microphone.

Expected behavior:

- Increased amplitude variation.
- Sound-level peaks corresponding to speech.
- Changes in the displayed graph.

## Test 3: Clapping

Clap near the microphone.

Expected behavior:

- A sharp increase in measured amplitude.
- A visible transient peak in Serial Plotter.
- Return toward baseline after the sound ends.

### Example Interpretation

| Environment | Expected Response |
|---|---|
| Quiet room | Lower amplitude |
| Normal conversation | Variable amplitude |
| Clapping | Short amplitude peak |
| Loud sound | Increased amplitude, potentially with clipping |

These are qualitative expectations.

No specific amplitude values are guaranteed.

The Serial Plotter displays a relative amplitude measurement and not calibrated sound-pressure level.

---

# Troubleshooting

## Error 1: OpenOCD Debugger Error

### Error Message

```text
Error:
Can't find interface/{programmer.protocol}.cfg

GDB server session ended.
```

### Cause

The IDE attempted to start an OpenOCD debugging session without a correctly configured programmer interface.

### Solution

1. Stop the debugging session.
2. Select Arduino Nicla Vision under Tools → Board.
3. Select the correct serial port.
4. Click the standard Upload button.
5. Do not use Start Debugging.

OpenOCD is not required for this microphone experiment.

---

## Error 2: Serial Plotter Shows No Graph

### Possible Causes

- Incorrect COM port.
- No numerical serial data.
- Sketch not running.
- Serial Monitor using the port.
- Serial configuration mismatch.

### Solution

1. Verify the board's current COM port.
2. Close Serial Monitor.
3. Confirm successful firmware upload.
4. Open Serial Plotter.
5. Select the correct baud rate.

For the custom sketch:

```text
115200 baud
```

For the unmodified official PDMSerialPlotter example:

```text
9600 baud
```

---

## Error 3: PDM Initialization Failed

### Error Message

```text
PDM INIT FAILED
```

### Solution

Check that:

```text
Board: Arduino Nicla Vision
Core: Arduino Mbed OS Nicla Boards
```

Confirm that the installed PDM library is compatible with the selected board.

Try the official Arduino microphone example to establish a baseline.

---

## Error 4: Flat Microphone Signal

If the Serial Plotter displays a constant value:

1. Speak or clap near the microphone.
2. Confirm that the sketch is producing fresh samples.
3. Test the official PDMSerialPlotter example.
4. Verify that microphone initialization succeeded.
5. Check that the microphone opening is not obstructed.

---

## Error 5: Upload Failure

If the board is not detected:

1. Disconnect and reconnect the USB cable.
2. Use a data-capable USB cable.
3. Close any program using the serial port.
4. Recheck Tools → Port.
5. If necessary, double-press the reset button to enter bootloader mode.
6. Select the newly detected port and upload again.

Follow the official Arduino recovery documentation if normal uploading remains unsuccessful.

---

# Additional Sensor Experiments

The Arduino Nicla Vision supports several additional sensing applications.

These are proposed extensions and are not implemented in the microphone sketch.

## 1. IMU Sensor Testing

The board includes an LSM6DSOX inertial measurement unit.

It provides:

- Three-axis acceleration measurements.
- Three-axis gyroscope measurements.

Potential experiments:

- Motion detection.
- Orientation estimation.
- Gesture recognition.
- Vibration monitoring.

The Arduino LSM6DSOX library can be used to explore available IMU examples.

---

## 2. Camera Testing

The Nicla Vision includes a 2 MP camera.

Potential experiments:

- Image acquisition.
- Object detection.
- Image classification.
- Embedded computer vision.

Camera functionality can be explored using supported Arduino examples or OpenMV firmware.

---

## 3. Time-of-Flight Sensor

The integrated distance sensor supports proximity measurements.

Potential experiments:

- Obstacle detection.
- Distance monitoring.
- Object presence detection.
- Sensor fusion.

Use an appropriate library and compatible example for the onboard sensor.

---

# Future Improvements

The microphone experiment can be extended into a complete embedded audio-processing application.

Potential improvements include:

### 1. Real-Time Audio Processing

Implement:

- RMS amplitude calculation.
- Digital filtering.
- Frequency analysis.
- Fast Fourier Transform (FFT).

### 2. Sound Event Detection

Develop algorithms to detect:

- Clapping.
- Knocking.
- Environmental noise events.

### 3. TinyML Audio Classification

Build an embedded machine-learning pipeline:

```text
Microphone
    |
    v
Audio Acquisition
    |
    v
Signal Preprocessing
    |
    v
Feature Extraction
    |
    v
TinyML Model
    |
    v
Sound Classification
    |
    v
Embedded Application
```

Possible applications include:

- Keyword spotting.
- Acoustic event recognition.
- Smart environmental monitoring.

### 4. Multimodal Sensor Fusion

Combine microphone, camera, and IMU information for intelligent embedded sensing.

### 5. Performance Optimization

Investigate:

- Audio processing latency.
- Memory consumption.
- CPU utilization.
- Power consumption.
- Real-time processing performance.

---

# Learning Outcomes

This project provides practical experience in:

- Embedded development environment setup.
- STM32-based microcontroller programming.
- Arduino board support package configuration.
- USB serial communication.
- PDM microphone interfacing.
- Digital audio acquisition.
- Audio buffering.
- Basic digital signal processing.
- Real-time signal visualization.
- Firmware debugging and troubleshooting.

It establishes a foundation for more advanced projects in embedded systems, signal processing, and TinyML.

---

# Project Validation Checklist

- [ ] Arduino Mbed OS Nicla Boards package installed.
- [ ] Arduino Nicla Vision selected correctly.
- [ ] Successful firmware upload.
- [x] Basic serial plotting test completed.
- [ ] PDM microphone initialized successfully.
- [ ] Microphone samples received.
- [ ] Sound-level graph displayed.
- [ ] Graph responds to speaking.
- [ ] Graph responds to clapping.
- [ ] Additional onboard sensors tested.
- [ ] TinyML application developed.

Only mark an experiment complete after verifying its results on the hardware.

---

# Repository Structure

```text
nicla-vision-setup/
|
|-- README.md
|
|-- NiclaMicLevelPlotter/
|   |
|   |-- NiclaMicLevelPlotter.ino
```

The `NiclaMicLevelPlotter.ino` sketch should be saved inside the folder with the same name.

---

# References

### 1. MLSysBook — Arduino Nicla Vision Setup

https://mlsysbook.ai/kits/contents/arduino/nicla_vision/setup/setup.html

### 2. Arduino Nicla Vision Documentation

https://docs.arduino.cc/hardware/nicla-vision

### 3. Arduino PDM Serial Plotter Example

https://github.com/arduino/ArduinoCore-mbed/blob/main/libraries/PDM/examples/PDMSerialPlotter/PDMSerialPlotter.ino

### 4. Arduino IDE

https://www.arduino.cc/en/software

### 5. Arduino Nicla Vision Bootloader Documentation

https://support.arduino.cc/hc/en-us/articles/8468641365276-Update-the-bootloader-on-Nicla-Vision

---

# Conclusion

This project documents the setup and initial validation of the Arduino Nicla Vision for embedded sensing.

The initial serial communication test confirms that numerical data can be transmitted from the board and displayed using Arduino Serial Plotter.

The provided PDM microphone application extends this setup toward audio acquisition and basic sound-amplitude analysis.

Once validated on hardware, the project can serve as a starting point for advanced digital signal processing, acoustic event detection, computer vision, and TinyML development.

---

**Project:** Arduino Nicla Vision — Hardware Setup and PDM Microphone Testing

**Platform:** Arduino Nicla Vision

**Language:** Embedded C/C++

**Development Environment:** Arduino IDE

**Application Domain:** Embedded Systems, Digital Signal Processing, and TinyML
