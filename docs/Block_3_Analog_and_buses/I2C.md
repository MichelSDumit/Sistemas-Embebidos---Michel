# I2C - Inter-Integrated Circuit

**Goal:** To understand and implement I2C communication using the RP2350, learning how to configure the I2C protocol and how devices can exchange data through the SDA and SCL lines using the Pico SDK.

## What I Learned
In this practice, I learned how I2C communication works and how devices can exchange information using two communication lines: SDA for data and SCL for the clock signal. I also learned about device addresses and how to configure the I2C protocol using the Pico SDK to send and receive data.

## Exercise

### What I did 
In this practice, we worked with the I2C communication protocol using a Raspberry Pi Pico 2, a DS1307 Real-Time Clock (RTC), and an MPU6050 accelerometer. The main objective was to connect both devices to the same I2C bus and establish communication between them. We connected the SDA and SCL pins of both components to GP4 and GP5 on the Pico 2, while assigning each device its own I2C address.
After connecting the components, we programmed the Pico 2 using the Pico SDK. First, we initialized the I2C communication at 100 kHz and configured the devices. We then set an initial date and time on the DS1307 and activated the MPU6050. The program continuously read data from both devices: the DS1307 provided the current time and date, while the MPU6050 provided acceleration measurements along the X, Y, and Z axes. Finally, the information was displayed in the terminal and updated approximately every second.

### Code

```
#include <stdio.h>
#include "pico/stdlib.h"
#include "hardware/i2c.h"
 
#define DS1307_ADDR 0x68
#define MPU6050_ADDR 0x69
#define I2C_PORT i2c0
#define PIN_SDA 4
#define PIN_SCL 5
 
uint8_t bcdToDec(uint8_t val) {
    return (val / 16 * 10) + (val % 16);
}
 
void mpu6050_init() {
    uint8_t buf[] = {0x6B, 0x00};
    i2c_write_blocking(I2C_PORT, MPU6050_ADDR, buf, 2, false);
}
 
// Función modificada: Convierte los datos crudos a unidades físicas (float) y retorna true/false si tuvo éxito
bool mpu6050_read_physical(float *accel, float *gyro, float *temp) {
    uint8_t buffer[14];
    uint8_t val = 0x3B;
    
    i2c_write_blocking(I2C_PORT, MPU6050_ADDR, &val, 1, true);
    int bytes = i2c_read_blocking(I2C_PORT, MPU6050_ADDR, buffer, 14, false);
 
    // Filtro de seguridad: Solo procesa las matemáticas si leyeron los 14 bytes exactos
    if (bytes == 14) {
        int16_t raw_ax = (buffer[0] << 8) | buffer[1];
        int16_t raw_ay = (buffer[2] << 8) | buffer[3];
        int16_t raw_az = (buffer[4] << 8) | buffer[5];
        
        int16_t raw_temp = (buffer[6] << 8) | buffer[7];
        
        int16_t raw_gx = (buffer[8] << 8) | buffer[9];
        int16_t raw_gy = (buffer[10] << 8) | buffer[11];
        int16_t raw_gz = (buffer[12] << 8) | buffer[13];
 
        // Escalas físicas del Datasheet
        accel[0] = raw_ax / 16384.0f;
        accel[1] = raw_ay / 16384.0f;
        accel[2] = raw_az / 16384.0f;
 
        *temp = (raw_temp / 340.0f) + 36.53f;
 
        gyro[0] = raw_gx / 131.0f;
        gyro[1] = raw_gy / 131.0f;
        gyro[2] = raw_gz / 131.0f;
        
        return true;
    }
    return false;
}
 
// Función modificada: Retorna true/false si logró leer el reloj
bool ds1307_read_time(uint8_t *hour, uint8_t *minute, uint8_t *second) {
    uint8_t reg = 0x00;
    uint8_t buffer[3];
 
    i2c_write_blocking(I2C_PORT, DS1307_ADDR, &reg, 1, true);
    int bytes = i2c_read_blocking(I2C_PORT, DS1307_ADDR, buffer, 3, false);
 
    if (bytes == 3) {
        *second = bcdToDec(buffer[0] & 0x7F);
        *minute = bcdToDec(buffer[1]);
        *hour   = bcdToDec(buffer[2] & 0x3F);
        return true;
    }
    return false;
}
 
int main() {
    stdio_init_all();
 
    // 400 kHz es la velocidad recomendada ("Fast Mode") para leer sensores rápidamente
    i2c_init(I2C_PORT, 400 * 1000);
    gpio_set_function(PIN_SDA, GPIO_FUNC_I2C);
    gpio_set_function(PIN_SCL, GPIO_FUNC_I2C);
    gpio_pull_up(PIN_SDA);
    gpio_pull_up(PIN_SCL);
 
    mpu6050_init();
    sleep_ms(1000);
 
    // Arreglos de tipo float para almacenar los datos ya convertidos
    float accel[3], gyro[3], temp;
    uint8_t hour, minute, second;
 
    while (true) {
        // Ejecutamos las lecturas y revisamos si fueron exitosas
        bool rtc_ok = ds1307_read_time(&hour, &minute, &second);
        bool mpu_ok = mpu6050_read_physical(accel, gyro, &temp);
 
        // Imprimimos el reloj
        if (rtc_ok) {
            printf("Tiempo [%02u:%02u:%02u] | ", hour, minute, second);
        } else {
            printf("Tiempo [Error de I2C] | ");
        }
 
        // Imprimimos los sensores
        if (mpu_ok) {
            printf("Temp: %.1f C | ", temp);
            printf("Acel(G) [X:%.2f, Y:%.2f, Z:%.2f] | ", accel[0], accel[1], accel[2]);
            printf("Giro(deg/s) [X:%.1f, Y:%.1f, Z:%.1f]\n", gyro[0], gyro[1], gyro[2]);
        } else {
            printf("MPU6050 [Error de I2C]\n");
        }
 
        sleep_ms(1000);
    }
    return 0;
}
```
### **Video**
<iframe width="560" height="315" src="https://www.youtube.com/embed/tCy0nkmPuvY?si=mTV5HTFQeCjPz1t7" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

### *Link to the simulation*
https://wokwi.com/projects/480258826413108225

### What could have gone better
The way we assembled the circuit and checked our code. At first, the circuit did not work properly, so we had to spend extra time reviewing the connections and making sure every component was connected to the correct pin. We also needed to be more careful when writing and checking the code to avoid errors.

### Conclusion
This practice helped us understand how I2C communication works and how multiple devices can share the same communication bus. By working with the DS1307 and MPU6050, we learned how to configure devices, read their registers, and obtain different types of information through the Raspberry Pi Pico 2. We also understood the importance of making correct circuit connections and carefully checking the code to achieve the expected results.