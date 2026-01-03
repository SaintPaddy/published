# Notes — LilyGO T-Internet-POE (v1.1) + Downloader Board

**Board:** LILYGO T-Internet-POE (printed version: `v1.1`)  
**Link:** https://lilygo.cc/products/t-internet-poe?variant=52074812965045  

## Home Assistant + ESPHome

1. Install the **ESPHome Device Builder** add-on in Home Assistant.
2. Add a new device and configure/upload the YAML using the site linked by HA:  
   https://web.esphome.io/
3. Flash the generated firmware via USB using the downloader board.
4. Flashing did not go the first time. had to connect the pins 'GND' and '100'

### Firmware file used
esp32-lilypoe-btproxy01-firmware.factory.bin

This configures the device as:

- **Hostname:** `esp32-lilypoe-btproxy01`
- **Networking:** DHCP (Ethernet)

---

## YAML used to build the BIN file

> Minimal "recovery" firmware — Ethernet only (no Bluetooth).  
> Purpose: verify stability & network connectivity.

```yaml
# Minimal Recovery Firmware for LILYGO T-Internet-POE
# Flash this via USB serial to recover the device
# NO Bluetooth - just basic Ethernet to verify stability

esphome:
  name: esp32-lilypoe-btproxy01
  friendly_name: esp32-lilypoe-btproxy01

esp32:
  board: esp32dev
  framework:
    type: esp-idf

logger:
  level: DEBUG

api:

ota:
  - platform: esphome

# Correct Ethernet config for T-Internet-POE (H438) with LAN8720
# Using new clk: syntax instead of deprecated clk_mode:
ethernet:
  type: LAN8720
  mdc_pin: GPIO23
  mdio_pin: GPIO18
  clk:
    pin: GPIO17
    mode: CLK_OUT
  phy_addr: 0

button:
  - platform: safe_mode
    name: Safe Mode Boot
    entity_category: diagnostic
  
  - platform: restart
    name: "Restart"
    entity_category: diagnostic

sensor:
  - platform: uptime
    name: "Uptime"
    update_interval: 60s

text_sensor:
  - platform: ethernet_info
    ip_address:
      name: "IP Address"
