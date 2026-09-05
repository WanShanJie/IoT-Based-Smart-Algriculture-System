# IoT-Based Smart Agriculture System

An IoT-based smart agriculture system that combines environmental sensing, automated irrigation, weather-aware fuzzy logic decision making, and AI-based leaf disease detection.

The system uses an ESP32 to collect real-time environmental data and control a water pump. A Python-based intelligent component processes sensor information together with weather forecast data to determine an appropriate irrigation level. A CNN-based model is also provided for detecting common leaf conditions.

> **Project Status:** Academic / Final Year Project
> **Hardware:** ESP32, DHT11, soil moisture sensor, rain sensor, water pump
> **Communication:** Wi-Fi, MQTT / V-ONE Cloud
> **Programming:** C++ (Arduino), Python
> **Database:** Supabase PostgreSQL
> **AI:** Fuzzy Logic and CNN-based leaf disease detection

---

## Table of Contents

* [Overview](#overview)
* [System Architecture](#system-architecture)
* [Features](#features)
* [Hardware Requirements](#hardware-requirements)
* [Software Requirements](#software-requirements)
* [Project Structure](#project-structure)
* [How It Works](#how-it-works)
* [Setup and Installation](#setup-and-installation)
* [ESP32 Setup](#esp32-setup)
* [Intelligent Components Setup](#intelligent-components-setup)
* [Database Setup](#database-setup)
* [Leaf Disease Detection](#leaf-disease-detection)
* [Testing the System](#testing-the-system)
* [Configuration](#configuration)
* [Demo Video](#demo-video)
* [Limitations](#limitations)
* [Future Improvements](#future-improvements)
* [License](#license)

---

## Overview

The IoT-Based Smart Agriculture System is designed to improve irrigation management by combining real-time environmental sensing with intelligent decision making.

The system continuously monitors:

* Temperature
* Humidity
* Soil moisture
* Rain status
* Weather forecast information

These inputs are processed using a fuzzy logic model to determine whether irrigation is required and the approximate irrigation level.

The ESP32 controls the water pump according to the irrigation decision and provides manual and emergency control capabilities.

A separate AI component uses a trained CNN model to classify captured leaf images into:

* Healthy
* Powdery
* Rust

The project therefore combines IoT, cloud connectivity, intelligent decision making, database storage, automation, and AI-based image classification.

---

## System Architecture

```text
                    ┌─────────────────────┐
                    │     ESP32 Device    │
                    │                     │
                    │ DHT11               │
                    │ Soil Moisture       │
                    │ Rain Sensor         │
                    └──────────┬──────────┘
                               │
                         Wi-Fi / MQTT
                               │
                               ▼
                    ┌─────────────────────┐
                    │   V-ONE Cloud       │
                    │ Telemetry / Control │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │ Intelligent Python  │
                    │ Component           │
                    │                     │
                    │ Fuzzy Logic         │
                    │ Weather Forecast    │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │ Irrigation Decision │
                    │                     │
                    │ None / Low / High   │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │    Water Pump       │
                    └─────────────────────┘


                    Leaf Disease Pipeline

                    Camera
                       │
                       ▼
                Image Preprocessing
                       │
                       ▼
                  CNN Model
                       │
                       ▼
              Healthy / Powdery / Rust
                       │
                       ▼
                 Database Storage
```

---

## Features

### Environmental Monitoring

The ESP32 collects real-time:

* Temperature
* Humidity
* Soil moisture
* Rain status
* Pump status

### Automated Irrigation

The system uses fuzzy logic to determine the irrigation requirement based on environmental conditions and weather information.

The irrigation decision is converted into different pump states:

```text
0 → Pump OFF
1 → Short irrigation pulse
2 → Long irrigation pulse
```

### Weather-Aware Decision Making

The intelligent component retrieves weather forecast information and uses the predicted probability of rain as an additional input to the irrigation decision.

This helps avoid unnecessary watering when rainfall is expected.

### Manual Control

The system supports manual pump activation through the cloud actuator interface.

### Emergency Stop

An emergency-stop mode is provided to force the pump OFF and prevent automatic irrigation until the system returns to automatic operation.

### Leaf Disease Detection

A trained CNN model is provided for classifying leaf images into:

```text
Healthy
Powdery
Rust
```

### Database Storage

Sensor readings, weather information, and leaf disease results can be stored in a PostgreSQL database hosted through Supabase.

---

## Hardware Requirements

The project requires the following hardware:

* ESP32 development board
* DHT11 temperature and humidity sensor
* Soil moisture sensor
* Rain sensor
* Relay module
* Water pump
* LEDs
* Appropriate power supply
* Jumper wires
* Breadboard or suitable prototype board

For leaf disease detection:

* Computer/laptop
* Camera or webcam

> Hardware pin assignments can be changed in `Watering_IoT/Watering_IoT.ino` to match your physical setup.

---

## Software Requirements

### ESP32

* Arduino IDE
* ESP32 board support
* DHT sensor library
* V-ONE MQTT client/library
* Required ESP32 networking and JSON libraries

### Python

* Python 3
* NumPy
* scikit-fuzzy
* Requests
* PySerial
* python-dotenv
* Supabase client
* OpenCV
* TensorFlow / Keras
* psycopg2
* Pillow

The Python dependencies are listed in:

```text
Intelligent_Components/requirements.txt
```

---

## Project Structure

```text
IoT-Based-Smart-Algriculture-System/
│
├── README.md
│
├── Intelligent_Components/
│   ├── fuzzy_logic_model.py
│   ├── leaf_disease_detection.py
│   ├── leaf_disease_detection_model.keras
│   └── requirements.txt
│
└── Watering_IoT/
    └── Watering_IoT.ino
```

---

# How It Works

## 1. Sensor Collection

The ESP32 reads the environmental sensors periodically.

```text
DHT11
 ├── Temperature
 └── Humidity

Soil Moisture Sensor
 └── Soil Moisture %

Rain Sensor
 └── Rain / No Rain
```

The readings are published through the MQTT-based cloud connection.

---

## 2. Fuzzy Logic Decision

The Python intelligent component uses five inputs:

```text
Soil Moisture
Temperature
Humidity
Current Rain Status
Forecast Rain Probability
```

These values are processed using fuzzy membership functions and predefined rules.

The resulting irrigation score is converted into an irrigation state:

```text
Low score
    ↓
Pump OFF

Medium score
    ↓
Short irrigation

High score
    ↓
Long irrigation
```

---

## 3. Pump Control

The ESP32 receives the irrigation state and controls the relay connected to the water pump.

The system also monitors the pump state and publishes changes in pump status.

---

# Setup and Installation

## Step 1 — Clone the Repository

```bash
git clone https://github.com/WanShanJie/IoT-Based-Smart-Algriculture-System.git
cd IoT-Based-Smart-Algriculture-System
```

---

## Step 2 — Create a Python Virtual Environment

It is recommended to use a virtual environment rather than committing a local Conda or Python environment to the repository.

```bash
python -m venv .venv
```

Activate it on Windows:

```bash
.venv\Scripts\activate
```

On Linux/macOS:

```bash
source .venv/bin/activate
```

---

## Step 3 — Install Python Dependencies

```bash
cd Intelligent_Components
pip install -r requirements.txt
```

---

# ESP32 Setup

Open:

```text
Watering_IoT/Watering_IoT.ino
```

Configure the following according to your own environment:

* Wi-Fi SSID
* Wi-Fi password
* V-ONE Cloud configuration
* Device IDs
* Sensor pins
* Relay pin
* LED pins

Do not commit passwords, API keys, or other credentials to GitHub.

Compile and upload the sketch to the ESP32 using Arduino IDE.

After uploading, open the Serial Monitor at:

```text
115200 baud
```

The ESP32 should initialize the sensors, connect to Wi-Fi, synchronize its time, and establish the MQTT connection.

---

# Intelligent Components Setup

The intelligent components require configuration through environment variables.

Create a local `.env` file inside:

```text
Intelligent_Components/
```

Example:

```env
OPENWEATHER_API_KEY=your_openweather_api_key
CITY_NAME=your_city
LAT=your_latitude
LON=your_longitude

SUPABASE_URL=your_supabase_url
SUPABASE_KEY=your_supabase_key
```

Replace the values with your own credentials.

### Important

The `.env` file should remain local and must not be committed to GitHub.

A safer approach is to provide a template such as:

```text
.env.example
```

containing placeholder values only.

---

# Database Setup

The intelligent component uses Supabase PostgreSQL to store system information.

The current implementation expects tables for sensor and weather information.

The fuzzy logic component interacts with:

```text
sensor_data
weather_data
```

The leaf disease detection component stores image information and prediction results.

Before running the system, configure your own Supabase project and update the environment variables accordingly.

Do not reuse credentials from another deployment.

---

# Leaf Disease Detection

The project includes:

```text
Intelligent_Components/leaf_disease_detection.py
```

The component captures an image from the default camera, preprocesses the image to the model input size, performs prediction using the trained Keras model, and reports the predicted class.

The supported classes are:

```text
Healthy
Powdery
Rust
```

The trained model is located at:

```text
Intelligent_Components/leaf_disease_detection_model.keras
```

To use a different model, update the model path and ensure that the model's input format and class ordering match the application.

---

# Testing the System

A complete hardware test can be performed using the following sequence.

### 1. Start the ESP32

Verify:

```text
Wi-Fi connected
MQTT connected
Sensors initialized
```

### 2. Verify sensor readings

Monitor the Serial output and confirm that the system reports:

```text
Temperature
Humidity
Soil Moisture
Rain Status
Pump Status
```

### 3. Start the intelligent component

Run:

```bash
python fuzzy_logic_model.py
```

The component receives sensor information and combines it with weather forecast data.

### 4. Observe the irrigation decision

The fuzzy system calculates an irrigation score and converts it into the corresponding pump state.

### 5. Test manual control

Use the configured cloud actuator interface to manually activate the pump.

### 6. Test emergency stop

Trigger the emergency-stop command and verify that the pump is switched OFF.

### 7. Test leaf disease detection

Connect a camera and run:

```bash
python leaf_disease_detection.py
```

Provide a suitable leaf image/camera view and observe the predicted class.

---

# Configuration

Several hardware settings can be modified directly in:

```text
Watering_IoT/Watering_IoT.ino
```

Examples include:

* GPIO pins
* Soil moisture calibration
* Telemetry interval
* Pump pulse duration
* Manual-control timeout

The fuzzy logic configuration can be modified in:

```text
Intelligent_Components/fuzzy_logic_model.py
```

This includes:

* Membership functions
* Fuzzy rules
* Irrigation output levels
* Weather forecast integration
* Database interaction

---

# Demo Video

A demonstration video will be added here:

**YouTube:** https://youtu.be/91mNTofutME

The demonstration will show the system setup, sensor monitoring, intelligent irrigation decision making, pump control, and leaf disease detection.

---


# Technologies Used

| Category            | Technologies                      |
| ------------------- | --------------------------------- |
| Microcontroller     | ESP32                             |
| Sensors             | DHT11, Soil Moisture, Rain Sensor |
| Programming         | C++, Python                       |
| Communication       | Wi-Fi, MQTT                       |
| Cloud / IoT         | V-ONE Cloud                       |
| Database            | Supabase PostgreSQL               |
| AI                  | CNN                               |
| Intelligent Control | Fuzzy Logic                       |
| Computer Vision     | OpenCV                            |
| ML Framework        | TensorFlow / Keras                |
| Weather Data        | OpenWeather API                   |

---

## Author

**Wan Shan Jie/ Chia Jie Lum**

Computer Science
Universiti Sains Malaysia (USM)

---

## Disclaimer

This repository is provided primarily for educational and academic purposes. Users should review and adapt the hardware configuration, security settings, credentials, and control logic before using the system in a real-world environment.
