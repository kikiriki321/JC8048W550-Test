  Guition JC8048W550 – Master Config v2 
🏆 MASTER CONFIG: Guition JC8048W550
=======================================

**Board:** ESP32-S3 (16MB Flash / 8MB OPI PSRAM)  
**Updated:** Jan 14, 2026

⚠️ The screen "flickering" issue upon touch is not a hardware screen problem, but a _Bus Contention_ issue. The solution is to allocate the LVGL buffer in **INTERNAL RAM**, not in PSRAM.

1\. PlatformIO Settings (platformio.ini)
----------------------------------------

OPI memory settings are mandatory.

    [env:guition-jc8048w550]
    platform = espressif32
    board = esp32-s3-devkitc-1
    framework = arduino
    monitor_speed = 115200
    
    board_upload.flash_size = 16MB
    board_build.arduino.memory_type = qio_opi 
    board_build.flash_mode = qio
    board_build.psram_type = opi
    board_build.f_flash = 80000000L
    board_build.f_cpu = 240000000L
    board_build.partitions = default_16MB.csv
    
    build_flags = 
        -DBOARD_HAS_PSRAM
        -DARDUINO_USB_CDC_ON_BOOT=1
        -DARDUINO_USB_MODE=1

2\. RGB Screen (Hardware Init)
------------------------------

We use 16 MHz. If lowered to 10-12 MHz, the panel loses synchronization (RGB Slideshow effect).

    #include <Arduino_GFX_Library.h>
    #define GFX_BL 2
    
    Arduino_ESP32RGBPanel *rgbpanel = new Arduino_ESP32RGBPanel(
        40, 41, 39, 42,      // DE, VSYNC, HSYNC, PCLK
        45, 48, 47, 21, 14,  // R
        5, 6, 7, 15, 16, 4,  // G
        8, 3, 46, 9, 1,      // B
        0, 40, 5, 40,        // HSYNC
        0, 8, 5, 8,          // VSYNC
        1,                   // Polarity: 1 (Original/Stable)
        16000000             // 16 MHz (Below this causes a slideshow effect)
    );
    
    Arduino_RGB_Display *gfx = new Arduino_RGB_Display(800, 480, rgbpanel);

3\. LVGL Buffer (Solution for Jitter)
-------------------------------------

✅ **CORRECT:** The buffer must be in `MALLOC_CAP_INTERNAL`.

    // 1. Buffer size (1/10 of screen)
    size_t buffer_size = 800 * 480 / 10;
    
    // 2. Allocation in fast INTERNAL RAM
    // This prevents bus congestion when touch sends data
    disp_draw_buf = (lv_color_t *)heap_caps_malloc(
        sizeof(lv_color_t) * buffer_size, 
        MALLOC_CAP_INTERNAL | MALLOC_CAP_8BIT
    );

4\. Peripherals Pinout
----------------------

### microSD Card (SPI)

*   CS: **10**
*   SCK: **12**
*   MOSI: **11**
*   MISO: **13**

Cards larger than 32GB (exFAT) do not work with the default SD library.

### Touch (GT911)

*   SDA: **19**, SCL: **20**
*   INT: **18**, RST: **38**
*   Setting: `ts.setRotation(ROTATION_INVERTED);`