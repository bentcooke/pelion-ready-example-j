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

3. Create the device management and update certificates:

    ```
    mbed dm init -d "company.com" --model-name "product-model" -q --force
    ```

4. Set the apn for your SIM card/operator in `mbed_app.json`:
    ```
    "nsapi.default-cellular-apn": "\"YOUR_APN\"",
    ```

5. Compile and program(-f):

    ```
    mbed compile -t GCC_ARM -m NRF52840_DK -f
    ```

#### Firmware update

Assuming your API key was added to your env (Step 2) and the device management credentials were created properly in the project root(Step 3), the following command will generate a manifest, compile and perform a firmware update of your device via Pelion:

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
