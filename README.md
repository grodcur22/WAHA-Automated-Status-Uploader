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
   ```Python
import os
import random
import time
import requests
import schedule
import base64
from datetime import datetime

# --- CONFIGURATION ---
# It is recommended to use environment variables for sensitive data
WAHA_URL = os.getenv("WAHA_URL", "http://localhost:3000")
API_KEY = os.getenv("WAHA_API_KEY", "YOUR_API_KEY_HERE")
SESSION = os.getenv("WAHA_SESSION", "default")
VIDEO_DIR = os.getenv("VIDEO_DIR", "./media")

# Generic links for the repository
LINK_INFO = "https://your-website.com/info"
LINK_CATALOG = "https://your-website.com/catalog"

HEADERS = {"x-api-key": API_KEY, "Content-Type": "application/json"}

# --- CREATIVE TEXTS (No emojis, English version) ---
MESSAGES = [
    f"Join my leadership team. Start your own business today and change your life. \n\nMore info here: {LINK_INFO}\nOr shop directly at our store: {LINK_CATALOG}\nWe accept multiple payment methods.",
    
    f"Looking for financial freedom? Be part of our project and grow with us. \n\nMore details: {LINK_INFO}\nCheck our catalog: {LINK_CATALOG}\nNationwide shipping available.",
    
    f"The products everyone is looking for. Invest in your future with us.\n\nRegistration & Info: {LINK_INFO}\nOnline Store: {LINK_CATALOG}\nSafe and easy payment."
]

def get_random_resource():
    if not os.path.exists(VIDEO_DIR):
        print(f"[ERROR] The folder {VIDEO_DIR} does not exist.")
        return None
    
    # Supported extensions
    extensions = ('.mp4', '.jpg', '.jpeg', '.png')
    files = [f for f in os.listdir(VIDEO_DIR) if f.lower().endswith(extensions)]
    
    if not files:
        print("[ERROR] No media files found in the folder.")
        return None
        
    return os.path.join(VIDEO_DIR, random.choice(files))

def upload_status():
    resource_path = get_random_resource()
    if not resource_path:
        return

    print(f"[STATUS] Preparing resource: {resource_path}")
    
    # File type detection
    is_video = resource_path.lower().endswith('.mp4')
    endpoint = "status/video" if is_video else "status/image"
    mimetype = "video/mp4" if is_video else "image/jpeg"

    # Base64 conversion
    try:
        with open(resource_path, "rb") as f:
            base64_data = base64.b64encode(f.read()).decode('utf-8')
    except Exception as e:
        print(f"[ERROR] Could not read file: {e}")
        return

    final_text = random.choice(MESSAGES)
    url = f"{WAHA_URL}/api/{SESSION}/{endpoint}"
    
    payload = {
        "file": {
            "mimetype": mimetype,
            "data": base64_data,
            "filename": os.path.basename(resource_path)
        },
        "caption": final_text
    }

    if is_video:
        payload["convert"] = True 

    try:
        print(f"[SYSTEM] Uploading status ({endpoint}) at {datetime.now().strftime('%H:%M:%S')}...")
        r = requests.post(url, json=payload, headers=HEADERS, timeout=120)
        
        if r.status_code in [200, 201]:
            print("[SUCCESS] Status uploaded successfully.")
        else:
            print(f"[ERROR] Status code: {r.status_code} | Response: {r.text}")
    except Exception as e:
        print(f"[CRITICAL FAILURE] Error connecting to the server: {e}")

# --- TASK SCHEDULING ---
schedule.every().day.at("09:00").do(upload_status)
schedule.every().day.at("15:00").do(upload_status)
schedule.every().day.at("21:00").do(upload_status)

if __name__ == "__main__":
    print("\n" + "="*50)
    print("WHATSAPP STATUS UPLOADER ACTIVATED")
    print(f"Media directory: {VIDEO_DIR}")
    print("Scheduled times: 09:00, 15:00, and 21:00")
    print("="*50 + "\n")
    
    while True:
        schedule.run_pending()
        time.sleep(30)
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

