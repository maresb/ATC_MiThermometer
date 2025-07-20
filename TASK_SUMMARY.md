# TelinkMiFlasher.html Enhancement Task

## Task Summary
Enhance the TelinkMiFlasher.html script to show MAC addresses and predict device name changes when flashing LYWSD03MMC devices.

## Requirements

### Primary Requirements
1. **Show MAC addresses** in the connection log when connecting to devices
2. **Predict device name changes** when flashing starts (not during connection)
3. **Handle all 4 firmware types** with appropriate predictions for each

### 4 Firmware Types Analysis
1. **Custom Firmware** (`FirmwareUpgrade()`)
   - Source: `otafiles.custom[hwver_id]`
   - **Gets ATC_XXXXXX name** (e.g., ATC_C92647)
   - Prediction: "Device will be renamed to: ATC_C92647 after flashing custom firmware"

2. **Zigbee Firmware** (`FirmwareZigbee()`)
   - Source: `otazfiles.bin[hwver_id]`
   - **Converts to Zigbee protocol**
   - Prediction: "Device will be converted to Zigbee protocol after flashing"

3. **ZB Firmware** (`FirmwareZB()`)
   - Source: `otazbfiles.bin[hwver_id]`
   - **Converts to Zigbee protocol (trial)**
   - Prediction: "Device will be converted to Zigbee protocol (trial firmware) after flashing"

4. **Original Firmware** (`BackToOriginal()`)
   - Source: `otafiles.original[hwver_id]`
   - **Keeps original LYWSD03MMC name**
   - Prediction: "Device will keep original LYWSD03MMC name after flashing stock firmware"

## Technical Implementation

### ATC Name Generation Algorithm
```javascript
function generateATCName(macAddress) {
    if (!macAddress) return null;
    
    // Remove colons and convert to uppercase
    var cleanMac = macAddress.replace(/:/g, '').toUpperCase();
    
    // Take the last 6 characters (3 bytes) from the MAC
    var lastThreeBytes = cleanMac.slice(-6);
    
    // Generate ATC_XXXXXX format
    return "ATC_" + lastThreeBytes;
}
```

Example: MAC `A4:C1:38:C9:26:47` → ATC name `ATC_C92647`

### Required Changes

1. **Add Helper Functions**
   - `generateATCName(macAddress)`: Generates ATC_XXXXXX name from MAC
   - `getDeviceMAC()`: Retrieves device MAC address if available

2. **Add Firmware Type Tracking**
   - Add `currentFirmwareType` variable
   - Set firmware type in each firmware loading function:
     - `FirmwareUpgrade()` → `currentFirmwareType = 'custom'`
     - `FirmwareZigbee()` → `currentFirmwareType = 'zigbee'`
     - `FirmwareZB()` → `currentFirmwareType = 'zb'`
     - `BackToOriginal()` → `currentFirmwareType = 'original'`

3. **Enhanced Connection Logging**
   - Show MAC address during connection
   - Example: "Connecting to: LYWSD03MMC (MAC: A4:C1:38:C9:26:47)"

4. **Enhanced Flashing Prediction** (in `updateBegin()` function)
   ```javascript
   // Predict device name changes based on firmware type
   if (bluetoothDevice) {
       var deviceMAC = getDeviceMAC();
       if (deviceMAC) {
           if (currentFirmwareType === 'custom') {
               var atcName = generateATCName(deviceMAC);
               if (atcName) {
                   addLog("Device will be renamed to: " + atcName + " after flashing custom firmware");
               }
           } else if (currentFirmwareType === 'zigbee') {
               addLog("Device will be converted to Zigbee protocol after flashing");
           } else if (currentFirmwareType === 'zb') {
               addLog("Device will be converted to Zigbee protocol (trial firmware) after flashing");
           } else if (currentFirmwareType === 'original') {
               addLog("Device will keep original LYWSD03MMC name after flashing stock firmware");
           }
       }
   }
   ```

5. **Enhanced Advertisement MAC Logging**
   - Show MAC addresses in advertisement data (without ATC prediction)
   - Works for multiple formats: Mi, pvvx, atc1441, CGG1, Qingping/ClearGrass

## Key Constraints

1. **Timing**: ATC_ predictions should only appear when flashing starts (`updateBegin()`), not during connection
2. **Context**: Only show ATC_ prediction for custom firmware, not stock firmware
3. **Backward Compatibility**: All existing functionality must remain unchanged
4. **Graceful Handling**: Handle missing MAC addresses gracefully

## Expected Log Output Examples

### Connection Logging
```
10:30:18: Connecting to: LYWSD03MMC (MAC: A4:C1:38:C9:26:47)
```

### Flashing Predictions
- **Custom**: "Device will be renamed to: ATC_C92647 after flashing custom firmware"
- **Zigbee**: "Device will be converted to Zigbee protocol after flashing"
- **ZB**: "Device will be converted to Zigbee protocol (trial firmware) after flashing"
- **Original**: "Device will keep original LYWSD03MMC name after flashing stock firmware"

### Advertisement Data
```
10:30:15: MAC: A4C138C92647, Bat: 85%, Vbat: 3000 mV, Temp: 22.5°C, Humi: 45.2%
```

## Files to Modify
- `TelinkMiFlasher.html`: Main script with enhanced logging functionality
- Create documentation files: `CHANGES.md`, `PR_DESCRIPTION.md`, `EXAMPLE_OUTPUT.md`

## Testing Requirements
- Test ATC name generation with various MAC formats
- Verify correct behavior with null/empty inputs
- Confirm backward compatibility with existing functionality
- Test all 4 firmware type predictions

The implementation should provide users with clear device identification (MAC addresses) and accurate predictions about what will happen to their device based on the firmware type they choose to flash.