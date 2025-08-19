# GMKtec NucBox K8 Plus IT8613E Support

## Hardware
- **Model**: GMKtec NucBox K8 Plus  
- **CPU**: AMD Ryzen 7 8845HS
- **Super I/O Chip**: ITE IT8613E (revision 12)
- **Base Address**: 0xa30

## Issue
The IT8613E chip appears as "Device not activated" in standard detection, preventing the module from loading automatically.

## Solution
The module requires specific parameters to work on this hardware:

```bash
modprobe it87 ignore_resource_conflict=1 force_id=0x8613 mmio=1
```

### Parameters Explained:
- `ignore_resource_conflict=1` - Bypasses ACPI resource conflicts
- `force_id=0x8613` - Forces detection of IT8613E chip
- `mmio=1` - **CRITICAL**: Uses Memory-Mapped I/O to bypass the activation check

## Installation on Fedora

1. Build the module:
```bash
make
sudo make install
```

2. Configure module parameters:
```bash
echo "options it87 ignore_resource_conflict=1 force_id=0x8613 mmio=1" | sudo tee /etc/modprobe.d/it87.conf
```

3. Add to modules-load.d for systemd:
```bash
echo "it87" | sudo tee /etc/modules-load.d/it87.conf
```

4. Rebuild module dependencies:
```bash
sudo depmod -a
```

## Detected Sensors
After successful loading:
- **fan2**: ~930 RPM (CPU Fan)
- **fan3**: ~1890 RPM (Case Fan)
- **temp1**: CPU temperature (thermistor)
- **temp2**: System temperature (thermistor)
- **temp3**: Additional temperature sensor

## Troubleshooting
If the module doesn't load on boot, check `dmesg` for "Device not activated" message. The `mmio=1` parameter is essential for this hardware.