# mcp9600
Basic I2C driver for the Microchip Technology MCP960X Thermocouple Amplifier Chip
---
## ! This crate is a WIP and has minimal functionality !

Currently, the following features are implemented:
- Reading the device ID
- Configuring the Sensor portion of the device
- Configuring the measurement profile of the device
- Performing basic hot junction temperature measurements (results in an f32)

TODO:
- Read the status register and pass the result to the user
- Enable configuration of the Alert registers
- Documentation

Example use (Using linux_embedded_hal in this case):
```rust
extern crate linux_embedded_hal as hal;

use mcp9600::*;
use hal::I2cdev;
use std::thread;
use std::time::Duration;

fn main() -> Result<(), hal::I2CError> {
    let i2c_bus = I2cdev::new("/dev/i2c-1").unwrap();
    let mut sensor = MCP9600::new(i2c_bus, DeviceAddr::AD7)?; // Determined by I2C address

    sensor.set_sensor_configuration(
        ThermocoupleType::TypeK,
        FilterCoefficient::FilterMedium,
        )?;

// Configure the measurement profile of the device
    sensor.set_device_configuration(
            ColdJunctionResolution::High,
            ADCResolution::Bit18,
            BurstModeSamples::Sample1,
            ShutdownMode::NormalMode
            )?;

     loop {
        let data = sensor.read_hot_junction();
        println!("Temperature is: {:?}C", data.unwrap());
        thread::sleep(Duration::from_secs(1));
     }
}
```
Determine the I2C address of the MCP9600 using an i2c scan utility. Find the corresponding DeviceAddr in the table below to use in initializing the sensor. 

| DeviceAddr |   Hex   | Decimal |
| :-------:  | :-----: | :-----: |
|    AD0     |   x60   |    96   |
|    AD1     |   x61   |    97   |
|    AD2     |   x62   |    98   |
|    AD3     |   x63   |    99   |
|    AD4     |   x64   |   100   |
|    AD5     |   x65   |   101   |
|    AD6     |   x66   |   102   |
|    AD7     |   x67   |   103   |
