# GateWay

## Gateway board

The gateway board uses a [CM4-POE-UPS-BASE KIT](https://www.waveshare.com/wiki/CM4-POE-UPS-BASE) board as base with a Raspberry PI 4 Compute module. This board has a buildin UPS which uses 3 18650 Li-ion batteries, furthermore the board can be powered from POE (802.3af).

## BOM

- [CM4-POE-UPS-BASE KIT](https://www.waveshare.com/wiki/CM4-POE-UPS-BASE)
- 3X 18650 batteries
- [Raspberry PI Compute module 4 with 32GB MMC (CM4104032)](https://datasheets.raspberrypi.com/cm4/cm4-product-brief.pdf)
- Antennas voor LoRaWAN
- 2x Pigtail SMA-MHF
- CR2032 batterij
- USB SDR + antenna
- [IC880A LoRaWAN board](https://wireless-solutions.de/products/lora-solutions-by-imst/radio-modules/ic880a-spi/)
- Adapter bord IC880A (see below)
- [Metal housing](https://benl.rs-online.com/web/p/wall-boxes/7755808)
- Metal Housing adapter, lasercut from custom design [housing_adapter.dxf](./housing_adapter.dxf)

### Adapter Board

- [PCB design](https://github.com/OpenIotNetwork/ic880a-adapter-raspberry)
- 1 pc of 1x20 pinheader
- 1 pc of 2x20 pinheader
- 2 pcs of 1x3 pinheader
