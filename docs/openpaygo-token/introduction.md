---
sidebar_position: 1
---

# Introduction to OpenPAYGO Tokens

## What is the OpenPAYGO Token?

This project targets manufacturer that want to add a Pay-As-You-Go (PAYGO) feature to their product. They can use the hardware examples to add a hardware control feature (e.g. a switch to control the load) and use this token system as a secure way to remotely control the activation of the devices via a token.

The token system consists of a way of generating tokens containing a certain number of days to be activated on a unit (which will be generated on a server), and a way to verify that a token is valid and extract the number of days to be activated (which will be done in the device).

The token generation part of the system can then be integrated with a software platform such as PaygOps or any other software platform.
The token validation part can be integrated in virtually any device.

## License

While projects financed by EnAccess usually use an MIT license for maximum openness, this project is using an Apache 2.0 license that adds the additional restriction over MITlicense that changes made to the code have to be documented when used in other projects. We have chosen to do this to avoid having projects using this token system with modification that break compatibility with no mention that they are not compatible, hence leading to confusion.

## Alternatives Tokens in OpenPayGo Usecase

### How to choose the right PAYGO Technology

When deciding to make your product pay as you go, there are several options available, and different considerations to take into account to know what is best in your situation. Below is a short presentation of the different options with their advantages, inconvenience and an opinionated view of the situations in which it is best to use them.

We then go on and go into a little bit more detail about the critical choice within those different options.

### Choosing the PAYGO technology category

### Keypad entry of tokens

This includes device using entry of token via keypad, remote control, etc. It is probably the most common PAYGO choice at the moment.

**➕ Pros:**

- Can be very cheap to implement
- Works even in the most remote regions with no coverage

**➖ Cons:**

- Requires some customer education on token entry
- Still some costs to send the token to the client (if done via SMS)

**When to use it:** We recommend this method on most products as it is cheap and easy to implement. It can also easily be used as a complement to a Direct Radio Communication technology in order to offer a backup method of activation when the network connection is unavailable.

### Direct Radio Communication

This includes devices using direct GSM communication, connection via WiFi hotspot, LoraWAN hotspot, etc. It is a relatively common PAYG choice and allows the device to directly communicate with the manufacturer/distributors servers.

**➕ Pros:**

- Ability to receive back data feedback from devices (usage data, health data, etc.)
- Easiest option to use for clients, nothing to do

**➖ Cons:**

- Requires more expensive hardware
- Must have network reception
- Can have high running costs

**When to use it:** We recommend this method for larger products for which the cost of the monitoring is small compared to the cost of the product or for new products being developed for which the monitoring data has very high value.

### Other methods

Those other methods are less common but have been found in commercial products and might be useful in some situations.

#### Cable activation

**➕ Pros:**

- Very cheap hardware

**➖ Cons:**

- Requires an agent to physically access the device for every activation

**When to use it:** When clients need to pay directly with the agent and the absolute lowest cost is required on the product.

#### Activation via Bluetooth

**➕ Pros:**

- Cheaper than direct radio communication

**➖ Cons:**

- Requires the client to have a smartphone and internet connection

**When to use it:** When the clients is guaranteed to have a smartphone with a good internet connection and data feedback and convenience are important.

#### Activation via phone sound

**➕ Pros:**

- Easier to use for clients than token
- Lower running costs than direct GSM

**➖ Cons:**

- Complex engineering
- Requires high quality phone connection

## Choosing the right keypad input method

### Separate Remote Control

**➕ Pros:**

- Requires very little changes to the device design
- Very small physical footprint
- Requires few MCU pins

**➖ Cons:**

- Clients might lose or break the remotes
- The batteries in the remote might need changing
- Remotes are sometimes unreliable

**When to use it:** We recommend this when performing change to an existing product design is not a viable option, since this can be added with just a small PCB changes and a hole for the IR receiver and an indicator LED.
It is also recommended for very small products on which having a keypad or a port is not mechanically possible.

### Integrated Keypad

**➕ Pros:**

- Usually cheapest
- Most reliable and user friendly

**➖ Cons:**

- Requires significant design changes
- Relatively large physical footprint

**When to use it:** We recommend using for products that are big enough to feature a keypad when designing them new or when physical design changes are not an issue. A restricted keypad with only 4 or 5 buttons can also be considered to reduce the design change and design footprint.

### External Wired Keypad

**➕ Pros:**

- Reliable
- Requires few MCU pins

**➖ Cons:**

- Keypads might be lost
- Requires potentially significant design change for the port

**When to use it:** We recommend using this when a product has both a PAYGO version and a non-PAYGO version and keeping the cost lowest on the later is key. The physical footprint is also quite low which makes it attractive for small products.

## Choosing the right direct radio communication technology

### GSM/3G

**➕ Pros:**

- Network already in place and broad
- Fairly reliable
- Can transmit significant amount of data

**➖ Cons:**

- Requires expensive Modems (~3\$-15\$)
- Requires expensive M2M subscription (>0.5\$-5\$ per device per month)

**When to use it:** We recommend using this for larger products where the cost (including subscription) is low compared to the overall cost of the product and/or when large amount of data is required to be transmitted.

### WiFi

**➕ Pros:**

- Running costs are covered by the client
- Fairly reliable if the underlying internet connection is reliable
- Relatively cheap modules

**➖ Cons:**

- Requires the client to have a WiFi network
- Requires the client to configure the device to use that network

**When to use it:** We recommend using this for product targeting wealthy market were it can be assumed that all clients will have a functioning WiFi network and the skills necessary to configure the device.

### LoRaWAN

**➕ Pros:**

- Running costs are low
- Relatively cheap modules

**➖ Cons:**

- Requires to setup your own network infrastructure (gateways, etc.) which can be very costly
- Technology is not very widely available and requires additional engineering effort

**When to use it:** We recommend using this when the lowest cost per product is needed and when you have a significant density of clients that can justify investing in the networking infrastructure.
