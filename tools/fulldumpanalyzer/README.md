# Preset Name Extractor

A web-based tool to decode proprietary preset names from a raw device data dump. This tool was developed by reverse-engineering the device's SysEx dump format to provide an automated and reliable translation method.

---

## Overview

The **Preset Name Extractor** provides a simple interface to parse a complete system dump from the device. It automatically isolates the 20 data packets containing preset information, extracts the encoded names using a reverse-engineered understanding of the data structure, and translates them into a human-readable format.

---

## Key Features

* **Full Dump Parsing**: Accepts the entire raw text output from a dump command, automatically cleaning and isolating the relevant data packets.
* **Robust Packet Isolation**: Identifies the 20 distinct data packets by their unique `80 80 F0` header and `F7` footer, ensuring that only the core preset data is processed.
* **Reliable Name Extraction**: A "Find-Then-Stride" algorithm locates all five preset names within each packet. It first finds an initial "anchor" name and then uses the dump's fixed 40-byte structure to read the subsequent names at their precise locations.
* **Accurate Hex-to-Text Decoding**: Translates extracted name data using a custom 2-byte character map that was reverse-engineered from the device's firmware for a perfect one-to-one translation.
* **Simple User Interface**: Just paste the raw dump and click "Translate." The tool handles all the parsing and displays the results in two organized tables for User and Factory presets.
* **Integrated Debugger**: Includes a debug panel that provides a detailed log of the parsing process, showing which packets were found, where name anchors were located, and which data blocks failed validation.

---

## How to Use

1.  Trigger a full dump from the device using the command:
    ```
    8080F0000E00010000000201020400F7
    ```
2.  Copy the entire raw output and paste it into the text area.
3.  Click the **Translate** button to see the decoded preset lists.
