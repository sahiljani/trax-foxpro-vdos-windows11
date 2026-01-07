# TRAX on Windows 11 - Setup Documentation

## Problem
TRAX (FoxPro 2.5 application) running via vDos on Windows 11 was showing error:
```
Cannot create program workspace: File access denied
```

## Root Cause
Windows 11 blocks write access to `C:\` root directory for non-administrator processes. FoxPro 2.5 needs to create temporary workspace files, which was being denied.

---

## Solution Summary
Moved the entire TRAX installation from `C:\traxanal` to user Documents folder and updated configuration files.

---

## Changes Made

### 1. New Installation Location
**Old Location:** `C:\traxanal`
**New Location:** `C:\Users\[YourUsername]\Documents\traxanal`

**Why:** Windows 11 allows full read/write access to user Documents folder without administrator privileges.

---

### 2. File Changes

#### **autoexec.txt**
This file controls what vDos executes on startup.

**Original Content:**
```batch
@echo off
use c: c:\
use d: d:\
use e: e:\
use f: f:\
path c:\windows
c:
cd \traxanal
cls
trax.exe
pause
exit
```

**Updated Content:**
```batch
@echo off
use c: c:\Users\[YourUsername]\Documents\traxanal
use d: d:\
use e: e:\
use f: f:\
path c:\windows
c:
cls
trax.exe
pause
exit
```

**Key Changes:**
- **Line 2:** Changed `use c: c:\` to `use c: c:\Users\[YourUsername]\Documents\traxanal`
  - This maps vDos virtual C: drive directly to the traxanal folder
  - Eliminates need for long path navigation
- **Line 8:** Removed `cd \traxanal` command
  - No longer needed since C: already points to traxanal folder

---

#### **CONFIG.FP**
FoxPro configuration file that sets environment variables.

**Original Content:**
```
_foxdoc=""
_foxgraph=""
mvcount=600
```

**Updated Content:**
```
_foxdoc=""
_foxgraph=""
mvcount=600
tmpfiles=c:\Users\[YourUsername]\Documents\traxanal
editwork=c:\Users\[YourUsername]\Documents\traxanal
progwork=c:\Users\[YourUsername]\Documents\traxanal
```

**Key Changes:**
- **Line 4:** Added `tmpfiles=c:\Users\[YourUsername]\Documents\traxanal`
  - Tells FoxPro where to create temporary files
- **Line 5:** Added `editwork=c:\Users\[YourUsername]\Documents\traxanal`
  - Specifies editor workspace location
- **Line 6:** Added `progwork=c:\Users\[YourUsername]\Documents\traxanal`
  - Specifies program workspace location

**Why:** These settings ensure FoxPro creates all temporary/workspace files in a location with full write permissions.

---

#### **config.txt**
vDos configuration file (no changes needed).

**Content:**
```
mouse=on
window=40
```

---

### 3. vDos Installation
**Installed vDos to:** `C:\vDos\` (default location)
**Associated folder:** `C:\Users\[YourUsername]\Documents\traxanal`

**Desktop Shortcut Created:**
- **Name:** vDos - Initial test
- **Target:** `C:\vDos\vDos.exe`
- **Start in:** `C:\Users\[YourUsername]\Documents\traxanal`

---

## How to Run TRAX

### Method 1: Desktop Shortcut
Double-click the **"vDos - Initial test"** shortcut on your desktop.

### Method 2: From Folder
1. Navigate to `C:\Users\[YourUsername]\Documents\traxanal`
2. Double-click `vDosSetup.exe`

---

## Important Notes

### Why This Solution Works
1. **User Permissions:** Documents folder has full read/write access by default
2. **Virtual Drive Mapping:** vDos maps Windows folders to DOS-style drives, allowing legacy apps to work
3. **Workspace Access:** FoxPro can now create temporary files without permission errors

### Why Running as Administrator Doesn't Work
- vDos is designed to refuse administrator privileges
- The proper solution is to use a location with appropriate user permissions

### Files Copied
All files from `C:\traxanal` were copied to `C:\Users\[YourUsername]\Documents\traxanal`:
- TRAX.EXE (main application)
- All .DBF database files
- All .FPT memo files
- All configuration files
- All utility files (PKUNZIP.EXE, PKZIP.EXE, etc.)

---

## Troubleshooting

### If TRAX Shows "Bad command or file name"
Check that `autoexec.txt` has the correct path in line 2:
```
use c: c:\Users\[YourUsername]\Documents\traxanal
```

### If "File access denied" Returns
1. Verify CONFIG.FP has the three added lines (tmpfiles, editwork, progwork)
2. Check Windows Defender → Controlled Folder Access is OFF
3. Verify full permissions on Documents\traxanal folder

### If vDos Won't Start
1. Ensure vDos is installed to `C:\vDos`
2. Run vDosSetup.exe from the traxanal folder again
3. Check that shortcut "Start in" path is correct

---

## Summary of Technical Changes

| File | Line | Original | Updated | Reason |
|------|------|----------|---------|---------|
| autoexec.txt | 2 | `use c: c:\` | `use c: c:\Users\[YourUsername]\Documents\traxanal` | Map C: to traxanal folder |
| autoexec.txt | 8 | `cd \traxanal` | Removed | No longer needed |
| CONFIG.FP | 4 | N/A | `tmpfiles=c:\Users\[YourUsername]\Documents\traxanal` | Temp file location |
| CONFIG.FP | 5 | N/A | `editwork=c:\Users\[YourUsername]\Documents\traxanal` | Editor workspace |
| CONFIG.FP | 6 | N/A | `progwork=c:\Users\[YourUsername]\Documents\traxanal` | Program workspace |

---

## Date
Setup completed: January 7, 2026
---

