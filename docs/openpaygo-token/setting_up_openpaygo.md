---
sidebar_position: 3
---

# Setting up OpenPAYGO Tokens

## Libraries for Setting up

### Python

### C

#### How to use the code provided

#### The Example C implementation for a Device

The "Example_Device_Implementation_C" folder contains code intended to make it very easy to implement the system on a device. It is a working example that implements a device "simulator" and shows how the system would work on it.

To use it on the real device, just replace the "simulator" functions (such as the `BlinkRedLed` function or the `GetKeyPressed` function) by the real function used in your device.

The folder contains:

- The `main.c` file that presents an example of a complete PAYG firmware that can be implemented on the device
- The `opaycode_system` folder, containing a device-neutral C implementation of the code system, with the code generation and code decoding functions.
- The `device_simulator` folder, containing "simulator" implementations of the functions that would be needed on a real device (for example the `BlinkRedLed` function only prints "The Red LED Blinked" on your computer screen instead of actually blinking an LED).

#### The Python full system implantation and security tests

The "Example_Full_System_Implementation_Python" folder contains code intended to showcase how the full system of a server and a device would work together to generate tokens and decode them. It provides a concrete scenario tests and allows to generate tokens of any value for any device (given that the device uses the same key as the server).

The server and device simulator both have a `print_status()` function that can be used to print their internal state, this should be very useful for debugging when implementing into the device. The whole code is made for use with Python 3 and the tests can be run as it is.

The folder contains:

- A `shared.py` file containing functions shared between encoder and decoder
- A `encode_token.py` and `decode_token.py" containing respectively the function to generate tokens and to decode tokens.
- A `server_simulator` folder that contains an example implementation of a server
- A `device_simulator` folder that contains an example implementation of a device
- A `tools` folder with a utility to generate random keys
- An `example` folder that contains test scenarios
- A `security` folder that contains example attacks (see the "Security Audit" for context)

:::info Important Note
A secret key was here chosen randomly for the example. This secret key ensures that someone knowing the algorithm cannot generate tokens for all your devices if he does not have that secret key.

For more security in the actual implementation we should use a different key that you choose yourself and keep private for everybody except the person who compiles the firmware in the device and the person who sets up the server.
:::

#### How to quickly test that a device implementation is functional

This test (scenario 1) allows to quickly assess whether an implementation seems to work or not and help in the debugging process, even if it is by no mean exhaustive. For more tests, you can check the test scenarios 2 and 3 in the full system implementation example.

1. Setup the device with the starting code 123456789 and the model as well as with the following secret key: `{0xa2, 0x9a, 0xb8, 0x2e, 0xdc, 0x5f, 0xbb, 0xc4, 0x1e, 0xc9, 0x53, 0xf, 0x6d, 0xac, 0x86, 0xb1}`.

   We assume that the device has a count of 0.

   :::danger
   Do not use that key in production, this is just an example.
   :::

2. Press `*` to start entering the token and enter the token `123456789` into the device, the Red LED 🔴 should blink 10 times showing that the token is not valid. Invalid codes are the codes that take the longest to process by design.
3. Press `*` to start entering the token and enter the code `662486790`, this should activate the device for 1 day (Add Time) and the Green LED 🟢 should blink twice to show it is valid.
4. Press `*` to start entering the token and entering the code `662486790` again, this should not change the device activation and the Red LED 🔴 should blink 10 times to show that the token has already been entered properly.
5. Press `*` to start entering the token and enter the code `927706818`, this should activate the device for an additional 29 days days (Add Time) and the Green LED 🟢 should blink twice to show it is valid.
6. Press `*` to start entering the token and enter the code `942433796`, this should activate the device for 7 days (Set Time), removing 23 days from the current status, and the Green LED 🟢 should blink twice to show it is valid.
7. Press `*` to start entering the token and enter the code `650975787`, this should disable PAYG on the device (it should now be active forever) and the Green LED 🟢 should blink 5 times to show it is valid.
8. Press `*` to start entering the token and enter the code `592185789`, this should enable PAYG again on the device and set it to 0 days (not active). and the Green LED 🟢 should blink 2 times to show it is valid.

## Example implementation provided

An example implementation both on the server side and on the device side is provided with this documentation. It should allow you to just copy and paste most of the code into the firmware of your device and only adapt parts of the codes (which are purposely placeholders using Unix libraries to allow simulation on a computer) to match the actual hardware of your device (mainly the code to read the keypad presses, blink the LEDs and to access the RTCand memory). For more details, see below after the description of the solution.

## Simplified description of the solution

The code system is based on codes that contain a unique "Activation Value" that the device for which it was generated can find. The code also contains a "count" that increments by one each time a code is generated on the server.

The decoder makes sure that the code entered has a count that is higher than its current count but only up to 30 higher for security reasons. This number can be decreased significantly to improve security, although there is a compromise in terms of usability in case the clients loses enough intermediary codes that the new code is then considered invalid. This ensures that entering a code already used will not add more days again. The decoder then extracts the activation value from the code and returns it to the rest of the system.

## Encoding of the value

The value is encoded in the last 3 digits of the code by masking the value in the "starting code". This is done by adding the value to the last 3 digits of the "starting code", if the resulting value is over 999, then 1000 is subtracted from the value. For example, if the code is "123456**789**" and the value to be encoded is 50, the resulting code with value encoded would be "123456**839**".

The decoding is done by subtracting the "starting code" base (the last 3 digit) to the base (last 3 digits) of the received code. For example, if the received code is 123456829, the base is 829. Subtracting 789 from 829 gives us 40 which is the value. If the value obtained is negative, then 1000 needs to be added.

## Decoding the code on the device

1. Decode the value form the input code.
1. Generate the "base code" by adding encoding that value into the starting code following the steps in the "encoding of the value" section
1. Pass the "base code" X times through the "code generation function", Xbeing the last count + 30, at each iteration:
   1. Replace the "code base" in the resulting code with the "encoded code base". For example, if the resulting code from step 3 is "234567**890**", then the final code would be "234567**839**" (if the value to be encoded is 50 and the base 789 like in the example above)
   1. If the current X is strictly higher than the last count, we compare the resulting code to the input code, if they match. If it matches, then the code is valid and we return the value. If not we continue iterating
1. If the end of the loop is reached and no match was found, the code was invalid (either already used or properly invalid).

## Description of the code generation function

The function only takes a 32 bits integer no bigger than 999999999 as a parameter, and returns a similar integer.

Computational steps for code generation:

1. Copy the input integer twice (big endianness) turning the 32bits (4 bytes) into 8 bytes
1. Generate the SipHash-2-4 hash of the those 8 bytes with the key
1. Split the resulting hash into 2 parts of 32 bits
1. XOR the two parts together
1. Remove the top 2 Most Significant Bits from the result of the XORing above (leaving the 30 LSBits)
1. If the resulting value is over 999999999, subtract 73741825 to the value, this leaves 29.5 effective bits of entropy
