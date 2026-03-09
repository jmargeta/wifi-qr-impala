# wifi-qr-impala

Scan a Wi-Fi QR code with your webcam and connect automatically — no typing passwords by hand.

## How it works

1. Opens your webcam and waits for a standard `WIFI:S:...;T:...;P:...;;` QR code
2. Parses the SSID and passphrase from the scanned data
3. Connects via `iwctl` (iwd) on the first available wireless interface

The whole flow is a single command with zero interaction beyond pointing the camera.

## Dependencies

| Package | Purpose |
|---------|---------|
| [iwd](https://iwd.wiki.kernel.org/) | Wireless daemon & `iwctl` CLI |
| [zbar](https://github.com/mchehab/zbar) | `zbarcam` for QR code scanning |
| A V4L2 webcam at `/dev/video0` | Camera input |

On Arch-based systems (including those using Impala):

```bash
sudo pacman -S iwd zbar
```

## Installation

Copy the script somewhere on your `$PATH`:

```bash
cp wifi-qr-impala ~/.local/bin/
chmod +x ~/.local/bin/wifi-qr-impala
```

## Usage

```bash
wifi-qr-impala
```

Point your webcam at a Wi-Fi QR code. The script scans it, shows the detected SSID/security, asks for confirmation, then connects.

Skip the confirmation prompt:

```bash
wifi-qr-impala --yes
```

### Example output

```
Opening webcam... Point it at the QR code.
Scanned network:
  SSID: MyNetwork
  Security: WPA
Attempting to connect to 'MyNetwork' on wlan0...
Successfully connected!
```

## QR code format

The script expects the standard Wi-Fi QR encoding:

```
WIFI:S:<SSID>;T:<WPA|WEP|nopass>;P:<password>;;
```

Most phones and routers generate this format when sharing a network via QR code.

## Troubleshooting

| Problem | Fix |
|---------|-----|
| *No wireless station found* | Make sure `iwd` is running: `sudo systemctl start iwd` |
| *No QR code detected* | Check that `/dev/video0` exists and isn't in use by another app |
| *Could not parse WiFi credentials* | Verify the QR code uses the `WIFI:` URI scheme |
| *Connection failed* | Ensure you're in range and the password hasn't changed |

## License

MIT
