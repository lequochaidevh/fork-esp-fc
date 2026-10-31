# ESP-FC Custom Flight Controller Setup Guide

This README provides the pin mapping and configuration instructions for building a custom ESP32-based Flight Controller (FC) using the **BMI160 IMU**, the **QMC5883L (GY-271) Compass**, and the **BMP280 Barometer** running the **ESP-FC** firmware.

---

## Hardware Overview
* **Microcontroller:** ESP32 (WROOM) or ESP32-S3
* **Primary IMU (Gyro + Accel):** BMI160 (Connected via SPI for maximum performance)
* **Compass (Magnetometer):** QMC5883L / GY-271 (Connected via I2C for navigation/rescue features)
* **Barometer (Altitude):** BMP280 (Connected via I2C for Altitude Hold features)

---

## Wiring & Pin Mapping Tables

### 1. BMI160 Connection (SPI Mode)
*Highly recommended for 5-inch freestyle builds to ensure low latency and high loop times.*

| BMI160 Module Pin | Documentation CLI Name | ESP32 Pin (WROOM) | ESP32-S3 Pin | Description |
| :--- | :--- | :--- | :--- | :--- |
| **VCC** (5V / 3V3) | Power Supply | **5V** or **3V3** | **5V** or **3V3** | Main power input |
| **GND** | Ground | **GND** | **GND** | Common ground |
| **SCL / SCK** | `pin_spi_0_sck` | **18** | **12** | SPI Clock |
| **SDA / MOSI** | `pin_spi_0_mosi` | **23** | **11** | Master Out Slave In |
| **SDO / MISO** | `pin_spi_0_miso` | **19** | **13** | Master In Slave Out |
| **CS / CSB** | `pin_spi_cs_0` | **5** | **8** | Chip Select |

### 2. QMC5883L (Compass) & BMP280 (Barometer) Connection (Shared I2C Bus)
*Both the Compass and Barometer modules share the exact same I2C data and clock lines.*

| GY-271 / BMP280 Pin | Documentation CLI Name | ESP32 Pin (WROOM) | ESP32-S3 Pin | Description |
| :--- | :--- | :--- | :--- | :--- |
| **VCC** | Power Supply | **5V** or **3V3** | **5V** or **3V3** | Main power input |
| **GND** | Ground | **GND** | **GND** | Common ground |
| **SCL / SCK** | `pin_i2c_scl` | **22** | **10** | I2C Clock Line (Shared) |
| **SDA / SDI** | `pin_i2c_sda` | **21** | **9** | I2C Data Line (Shared) |

> ⚠️ **Important Hardware Tips:**
> 1. **Compass Placement:** Do not mount the GY-271 compass near high-current battery leads or the ESC. Extend its wires to the tail of the drone to avoid severe magnetic interference.
> 2. **Barometer Protection:** You **MUST** cover the small hole on top of the BMP280 sensor with a piece of dark, porous open-cell foam (sponge). Wind from the 5-inch propellers creates static pressure drops that will confuse the sensor and make the altitude reading highly inaccurate.

---

## CLI Configuration & Remapping

Connect your ESP32 to your PC, open the ESP-FC CLI interface, and verify settings:

1. **Verify Current Settings:**
   ```text
   get pin
   ```

2. **Manually Force Default SPI Pin Settings (if needed for WROOM):**
   ```text
   set pin_spi_0_sck 18
   set pin_spi_0_mosi 23
   set pin_spi_0_miso 19
   set pin_spi_cs_0 5
   save
   ```

3. **Activate Drivers:**
   Navigate to your Web Configurator or main configuration file. Toggle the sensor types:
   * **IMU:** Set to `BMI160`
   * **Mag:** Enable `QMC5883L`
   * **Baro:** Enable `BMP280`

---

## Limitations & Strapping Pins Warning
* **I2C Limitation:** The I2C driver only accepts pin numbers ranging from **1 to 31**.
* **Strapping Pins:** Avoid using ESP32 strapping pins (such as GPIO 0, 2, 5, 12, 15) for random inputs/outputs, as they may prevent the MCU from booting up properly.