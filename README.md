# WAHA Automated Status Uploader

This script provides an automated solution for managing and scheduling WhatsApp Status updates (Stories) using the [WAHA (WhatsApp HTTP API)](https://github.com/devlikeapro/waha) framework. It is designed to cycle through a directory of media files and post them at specific intervals with randomized captions.

## Prerequisites & Compatibility

### WAHA Plus License Required
**CRITICAL:** This script interacts with the `/api/status/` endpoints. Managing Status via API is a **WAHA Plus** feature. You must have an active Plus license and be running the Plus version of the WAHA Docker container for this script to function. 

You can find more information about the Plus version here: https://waha.devlikeapro.com/

### Environment Requirements
- Python 3.x
- WAHA (Plus Version) installed and running
- `requests` and `schedule` Python libraries

## Features

- **Automated Scheduling:** Pre-configured to upload content three times a day (09:00, 15:00, and 21:00).
- **Multi-Media Support:** Automatically detects and handles both images (`.jpg`, `.png`) and videos (`.mp4`).
- **Randomized Content:** Picks a random file from your media directory and pairs it with a randomized marketing caption to keep engagement high.
- **Base64 Encoding:** Handles local file conversion to Base64 strings for seamless API transmission.
- **Video Conversion:** Includes the `"convert: true"` flag for video uploads to ensure compatibility with WhatsApp's requirements.

## Configuration

The script uses environment variables for security and flexibility. You can set these in your OS or modify the script directly:

- `WAHA_URL`: The URL where your WAHA instance is hosted (default: `http://localhost:3000`)
- `WAHA_API_KEY`: Your secret API key for authentication
- `WAHA_SESSION`: The name of the session you want to use (default: `"default"`)
- `VIDEO_DIR`: The local path to the folder containing your images and videos

## Installation

1. Clone the repository.
2. Install the required dependencies:
   ```bash
   pip install requests schedule
````

3. Ensure your media folder is populated with content.
4. Run the script:
````
   ```bash
   python main.py
   ```

## Workflow

1. The script initializes and monitors the schedule.
2. At the designated time, it scans the `VIDEO_DIR` for supported extensions.
3. It converts the selected file to a Base64 string.
4. It sends a POST request to the WAHA API endpoint specific to the media type (image or video).
5. The WAHA Plus instance processes the request and updates your WhatsApp Status.

## Disclaimer

This project is not affiliated with WhatsApp or Meta. Use this tool responsibly and in compliance with WhatsApp's Terms of Service to avoid account bans.

```

