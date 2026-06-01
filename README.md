# OTP Dashboard 🔐

A live dashboard that auto-displays the latest OTP codes for **Surfshark VPN**, **Netflix**, and **Booking.com**.

## How it works

1. An OTP email arrives in Gmail
2. Google Apps Script detects it and extracts the 6-digit code
3. The code is written to a GitHub Gist (JSON)
4. This page fetches the Gist every 5 seconds and updates automatically

## Services

| Service | Status |
|---------|--------|
| Surfshark VPN | ✅ Active |
| Netflix | 🔜 Coming soon |
| Booking.com | ✅ Active |

## Setup

- Apps Script polls Gmail every 1 minute
- Gist stores the latest code at `otp.json`
- GitHub Pages serves this dashboard publicly
