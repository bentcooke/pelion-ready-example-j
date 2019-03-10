# Pelion Device Ready example - NRF52840_DK + Avnet BG96

### AVNET BG96 shield connections and power up

|BG96 signal (3V3)  | NRF52840_DK Arduino Pin| Source Setting|
|-------------------|------------------------|---------------|
|Reset (active high)| D7                     |bg96_pwr.cpp   |
|PWRKEY             | D10                    |bg96_pwr.cpp   |
|VBAT_3V8_EN        | Not connected, enabled by default |    |
|UART TX            | D1                     |mbed_app.json  |
|UART RX            | D0                     |mbed_app.json  |
|UART RTS           | D12                    |mbed_app.json  |
|UART CTS           | A1                     |mbed_app.json  |

The RESET and PWRKey signals are used to bring the BG96 up during system startup.  The current implementation overrides the weakly defined `mbed_main` function in `bg96_pwr.cpp`.  `mbed_main` is called before the RTOS is initialized and provides a convenient location for system init functions that are compatible with the automated test framework used with mbed os.

### Building with Mbed CLI

1. Import the application into your desktop:

    ```
    mbed import https://github.com/ARMmbed/pelion-ready-example-j
    cd pelion-ready-example-j
    ```

2. Configure the API key for your Pelion Portal account.

     If you don't have an API key available, then login in [Pelion IoT Platform portal](https://portal.mbedcloud.com/), navigate to 'Access Management', 'API keys' and create a new one. Then specify the API key as global `mbed` configuration:

    ```
    mbed config -G CLOUD_SDK_API_KEY <your-api-key>
    ```

3. Create the device management update certificate:

    ```
    mbed dm init -d "company.com" --model-name "product-model" -q --force
    ```

4. Compile and program:

    ```
    mbed compile -t GCC_ARM -m NRF52840_DK
    ```

#### Firmware update

Follow these steps to generate a manifest, compile and perform a firmware update of your device:

1. Configure the API key for your Pelion account.

     If you don't have an API key available, then login in [Pelion IoT Platform portal](https://portal.mbedcloud.com/), navigate to 'Access Management', 'API keys' and create a new one. Then specify the API key as global `mbed` configuration:

    ```
    mbed config -G CLOUD_SDK_API_KEY <your-api-key>
    ```

2. Initialize the device management feature:

    ```
    mbed dm init -d "company.com" --model-name "product-model" -q --force
    ```

3. Compile the application, include the firware update credentials generated before, merge with the bootloader and program the device:

    ```
    mbed compile -t GCC_ARM -m NRF52840_DK -c
    ```

4. Open a serial terminal, verify the application boots and is able to register to the Device Management service. Write down the `<endpoint ID>`, as it's required to identify the device to perform a firmware update.

5. Update the firmware of the device through Mbed CLI:

    ```
    mbed dm update device -D <Pelion device ID> -t GCC_ARM -m NRF52840_DK
    ```

    Inspect the logs on the device to see the update progress. It should look similar to:

    ```
    Firmware download requested
    Authorization granted
    Downloading: [+++- ] 6 %
    ```

    When the download completes, the firmware is verified. If everything is OK, the firmware update is applied, the device reboots and attemps to connect to the Device Management service again. The `<endpoint ID>` should be preserved.
