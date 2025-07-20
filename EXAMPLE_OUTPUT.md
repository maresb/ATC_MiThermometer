# Example Enhanced Logging Output

## Before Changes
```
10:30:15: Searching for devices
10:30:18: Connecting to: LYWSD03MMC
10:30:20: Connected
```

## After Changes
```
10:30:15: Searching for devices
10:30:18: Connecting to: LYWSD03MMC (MAC: A4:C1:38:C9:26:47)
10:30:18: Device will be renamed to: ATC_C92647 after flashing
10:30:20: Connected
```

## Advertisement Data Examples

### Mi Device Advertisement
```
10:30:15: MAC: A4C138C92647 (will become: ATC_C92647), DevID: 0x055B-LYWSD03MMC, FnCnt: 15, CtrID: 0x2000, Not bound, Standard certification
```

### pvvx Custom Firmware Advertisement
```
10:30:15: MAC: A4C138C92647 (will become: ATC_C92647), Bat: 85%, Vbat: 3000 mV, Temp: 22.5°C, Humi: 45.2%, Count: 1234, Flg: 1
```

### atc1441 Firmware Advertisement
```
10:30:15: MAC: A4C138C92647 (will become: ATC_C92647), Bat: 85%, Vbat: 3000 mV, Temp: 22.5°C, Humi: 45%, Count: 1234
```

### CGG1 "Goose" Advertisement
```
10:30:15: MAC: A4C138C92647 (will become: ATC_C92647), Bat: 85%, Temp: 22.5°C, Humi: 45.2%
```

### Qingping/ClearGrass Advertisement
```
10:30:15: MAC: A4C138C92647 (will become: ATC_C92647) CGG1, Temp: 22.5°C, Humi: 45.2%, Bat: 85%
```

## Benefits for Users
1. **Clear Device Identification**: Users can see the actual MAC address of each device
2. **Predictive Information**: Users know exactly what the device name will be after flashing
3. **Better Troubleshooting**: MAC addresses help identify specific devices in logs
4. **Improved Workflow**: No need to remember or guess what ATC name a device will have