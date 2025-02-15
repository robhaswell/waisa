# Application description

## Background
Often times I will realise that my Windows computer's display has not gone to sleep.
This causes unnecessary power consumption and wear on the display.
This problem arises because various applications and system processes can issue "power requests" to Windows, instructing the operating system to keep the display or system awake, even when idle.
**The Windows Power Management API is the underlying system in Windows that manages these power requests.**

## Solution
WAISA is a Windows C++ application that monitors the **Windows Power Management API** via the `PowerEnumerateRequests` function.
It looks for Power Requests of the types "Display" and "System" to idenitfy processes which are keeping the display awake

When processes are identified as keeping the display awake, WAISA will create a system tray icon.
Clicking on the icon will open a list of processes which are keeping the display awake.

## Details

WAISA operates by periodically calling the `PowerEnumerateRequests` function from the **Windows Power Management API**.  It specifically requests enumeration of two key request types:

*   **Display Requests ( `GUID_CONSOLE_DISPLAY_STATE` or `DisplayRequestType` ):** These requests explicitly prevent the monitor from turning off. WAISA identifies processes holding these requests to pinpoint applications directly keeping the display awake.
*   **System Requests ( `GUID_SYSTEM_AWAKE_STATE` or `SystemRequestType` ):** These requests prevent the entire system, and consequently the display, from entering sleep states. WAISA monitors these to identify processes preventing deeper power saving modes, which also indirectly keep the display on.

For each identified Power Request, WAISA extracts relevant information provided by the API, including:

*   **Requesting Process:** WAISA resolves the address of the requesting entity (returned by `PowerEnumerateRequests`) to identify the associated process name and potentially the executable path. This allows users to easily understand *which* application is responsible.
*   **Request Type:** Clearly indicates whether it is a "Display" or "System" request, helping users understand the nature of the wakefulness.
*   **Request Details (where available):**  The API may provide additional details in certain cases (e.g., for audio streams). WAISA aims to present any relevant details to the user for a more complete picture.

## Implementation

WAISA is implemented as a Windows application using C++ and the Windows API.

The build system is CMake.


