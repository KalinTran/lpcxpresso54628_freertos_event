Integrating SEGGER SystemView with FreeRTOS
==========================================

Purpose
-------
This document describes how to integrate SEGGER SystemView into the FreeRTOS-based LPC54628 example contained in this repository. SystemView provides non-intrusive, real-time tracing of task scheduling, interrupts, and application events to assist with performance analysis and debugging.

Prerequisites
-------------
- SEGGER SystemView (v3.60 or later) installed on the host PC.
- SEGGER J-Link software & drivers installed and a working J-Link debug probe.
- Access to the SystemView source files (licensed; not included in this repository).

Files to copy from your SystemView installation
----------------------------------------------
From the SystemView installation directory (typical path on Windows: "E:\\Program Files\\SEGGER\\SystemView\\Src"), copy the following into this project (suggested destination: `component/segger/`):

- `Config/`
- `SEGGER/` (core SystemView sources)
- `Sample/FreeRTOSV11/` (or the sample matching your FreeRTOS version)

Do not copy any files that you do not have a license to use.

Project layout suggestion
------------------------
Suggested directory in this repository:

 - component/segger/SEGGER/
 - component/segger/Config/
 - component/segger/Sample/FreeRTOSV11/

Add `component/segger` to your compiler include paths so the headers are found by the build system.

Integration steps
-----------------
1. Copy the SystemView source folders listed above into `component/segger/`.
2. Add include paths for the copied headers to the project (MCUXpresso IDE: Project Properties → C/C++ Build → Settings → Include Paths).
3. In `FreeRTOSConfig.h`, after your FreeRTOS configuration macros, add:

   #include "SEGGER_SYSVIEW_FreeRTOS.h"

   This enables the SystemView wrapper for FreeRTOS. Ensure the included sample matches your FreeRTOS version.

4. Provide a SystemView configuration source file. Use the sample `SEGGER_SYSVIEW_Conf...` from the copied Sample folder and adapt the following items to your board:

   - A high-resolution timestamp function (required). You may use SysTick or a hardware timer. The timestamp must be consistent with the CPU/SystemCoreClock used by the sample.
   - CPU frequency / timebase defines used by SystemView.

5. Initialize SystemView before starting the scheduler. Example (pseudo-code placed in `main()` or board init):

   /* Configure board clocks and peripherals */
   ...existing initialization code...

   /* Initialize SystemView */
   SEGGER_SYSVIEW_Conf();
   SEGGER_SYSVIEW_Start();

   /* Start FreeRTOS scheduler */
   vTaskStartScheduler();

6. Build the project and start a debug session with J-Link attached.
7. Launch the SystemView application on the host PC, connect to the target, and start tracing.

Example edits and checks
------------------------
- Ensure the SystemView timestamp function returns a monotonically increasing value in ticks or CPU cycles.
- Make sure `SystemCoreClock` or equivalent frequency macro used by SystemView matches the device clock configuration.
- If FreeRTOS hook macros differ from the sample, adapt the SystemView FreeRTOS wrapper accordingly.

Troubleshooting
---------------
- No events or timestamps show in SystemView: verify J-Link connection and that SystemView initialization runs before the scheduler.
- Corrupt or zero timestamps: verify the timestamp function and frequency settings.
- Missing task events: confirm `SEGGER_SYSVIEW_FreeRTOS.h` is included and that FreeRTOS symbols used by the sample are present in your build.

MCUXpresso IDE notes
--------------------
- Add the SystemView source files to the project (Project Explorer) or ensure the makefile references them.
- Update include paths for both the compiler and the indexer if necessary.

Security & licensing
---------------------
SystemView source files are provided by SEGGER and are under SEGGER's license. Do not redistribute licensed files without permission. This repository contains only guidance and no licensed SystemView code.

References
----------
- SEGGER SystemView: https://www.segger.com/products/development-tools/systemview/
- SEGGER J-Link: https://www.segger.com/products/debug-probes/j-link/

If you want, I can:

- Add a `component/segger` folder skeleton to this repository (empty placeholders) so you can copy the SystemView sources into the expected structure, or
- Produce a short MCUXpressoIDE checklist with exact menu paths and sample include settings for this project.
Integrating SEGGER SystemView with FreeRTOS Project
