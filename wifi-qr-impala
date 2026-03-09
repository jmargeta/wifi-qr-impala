#!/bin/bash

# 1. Identify your wireless interface (usually wlan0)
WIFI_DEV=$(iwctl device list | grep 'station' | awk '{print $2}' | head -n 1)

if [ -z "$WIFI_DEV" ]; then
    echo "Error: No wireless station found. Is iwd running?"
    exit 1
fi

echo "Opening webcam... Point it at the QR code."

# 2. Run zbarcam and capture the raw output
# The --raw flag strips the "QR-Code:" prefix
RAW_DATA=$(zbarcam --raw --oneshot /dev/video0)

if [ -z "$RAW_DATA" ]; then
    echo "No QR code detected or zbarcam closed."
    exit 1
fi

# 3. Parse the SSID and Password from the WIFI: URI
# Format: WIFI:S:MyNetwork;T:WPA;P:MyPassword;;
SSID=$(echo "$RAW_DATA" | sed -n 's/.*S:\([^;]*\);.*/\1/p')
PASS=$(echo "$RAW_DATA" | sed -n 's/.*P:\([^;]*\);.*/\1/p')

if [ -z "$SSID" ] || [ -z "$PASS" ]; then
    echo "Could not parse WiFi credentials from: $RAW_DATA"
    exit 1
fi

# 4. Connect using iwctl
echo "Attempting to connect to '$SSID' on $WIFI_DEV..."
iwctl station "$WIFI_DEV" connect "$SSID" --passphrase "$PASS"

if [ $? -eq 0 ]; then
    echo "Successfully connected!"
else
    echo "Connection failed. Check your password or signal."
fi
