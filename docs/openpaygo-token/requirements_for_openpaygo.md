---
sidebar_position: 2
---

# Requirements for OpenPAYGO Tokens

## General requirements for the use case

- The activation token should be able to contain 2 years worth of activation time (and should be able to use more tokens after that period to add more time, up to at least 10 years).
- The activation tokens should be able to both add and remove days from a system (presenting advantages over most currently existing solution).
- The tokens should also be able to enable or disable the PAYGO mode at will
- The token system should be extensible to add certain commands required by some devices (but that do not need standardisation, e.g. "Enable High Power mode").
- The code should be reasonably secure; trying to type the same code twice, taking a code from a neighbour or typing codes randomly should not succeed (or extremely rarely).
- The token generator (the server), will be given some starting data for each device that are defined by the manufacturer. This includes a key (that can be shared for a whole batch or not) as well as a serial number and a "starting code" for each device. Aspecific format for this data based on CSV shall be specified to ensure interoperability.
- The token verification (the unit) will know its "starting code" and the key and will extract the number of days to be activated from the code
- The codes should ideally be 9 digits longs to make successful entry by end user more likely
- For devices with limited space and without the options for infrared remotes, having the ability to enter codes using only digits 1 to 4 should be possible (at the expense of accepting a longer 15 digit code).

## Target minimum hardware requirements for the token verification part

- An MCU with at least 8KB of AVAILABLE flash (after the rest of the firmware functions are implemented). It is also possible to use a separate dedicated MCU that will communicate with your main MCU but this entirely depends on your design requirements and choices of MCU.
- A keypad input method (either Infrared Receiver and Infrared remote, an actual membrane keypad, or a USB port to which a USB keyboard can be connected). It should preferably have at least 12 buttons, but 10 buttons or even 4 buttons can be sufficient.
- A hardware Real Time Clock (RTC) that is accurate enough to drift by a non- noticeable time over 12 months (usually less than 6h would be good). However, there is no need for the RTC to be configured with the actual real time, it is only required that the timestamp obtained from the RTC keeps going up constantly by 1 every second with minimal drift.
- An EEPROM or other memory on which permanent data can be stored (such as the serial number, device keys, PAYG status, etc.). Flash can be used as long as proper care is taken to make sure the lifetime of the used flash blocks is sufficient
- A UART or other communication interface that allows the "starting code" and key to be set in factory. Alternatively, some other way to have different firmware for each unit can be used (e.g. factory flashed MCUs with procedure to set different values in flash for each device).
- If the device can be disconnected from the battery, also make sure that:
- The RTC can be powered by a backup battery to not be reset and keep running when the battery is disconnected
- There are backup registers with at least 96 bits that are kept by the backup battery when the power is disconnected (but lost if the backup battery is not present and the RTC stops running). This is used for the storage of key values that are frequently changing.
