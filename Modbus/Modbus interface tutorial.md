# Modbus interface tutorial

## History of the Modbus protocol

Some communication standards just emerge. Not because they are pushed by a large group of vendors or a special standards organisation. These standards—like the Modbus interface—emerge because they are good, simple to implement and are therefore adapted by many manufacturers. Because of this, Modbus became the first widely accepted field-bus standard.

Modbus has its roots in the late seventies of the previous century. It is 1979 when PLC manufacturer Modicon—now a brand of Schneider Electric’s Telemecanique—published the Modbus communication interface for a multi-drop network based on a master/client architecture. Communication between the Modbus nodes was achieved with messages. It was an open standard that described the messaging structure. The physical layer of the Modbus interface was free to choose. The original Modbus interface ran on [RS-232](https://www.lammertbies.nl/comm/info/rs-232-specs), but most later Modbus implementations used RS-485 because it allowed longer distances, higher speeds and the possibility of a true multi-drop network. In a short time hundreds of vendors implemented the Modbus messaging system in their devices and Modbus became the de facto standard for industrial communication networks.

The nice thing of the Modbus standard is the flexibility, but at the same time the easy implementation of it. Not only intelligent devices like micro-controllers, PLCs etc. are able to communicate with Modbus, also many intelligent sensors are equipped with a Modbus interface to send their data to host systems. While Modbus was previously mainly used on wired serial communication lines, there are also extensions to the standard for wireless communications and TCP/IP networks.

## Modbus message structure

The Modbus communication interface is built around messages. The format of these Modbus messages is independent of the type of physical interface used. On plain old RS232 are the same messages used as on Modbus/TCP over Ethernet. This gives the Modbus interface definition a very long lifetime. The same protocol can be used regardless of the connection type. Because of this, Modbus gives the possibility to easily upgrade the hardware structure of an industrial network, without the need for large changes in the software. A device can also communicate with several Modbus nodes at once, even if they are connected with different interface types, without the need to use a different protocol for every connection.

***On simple interfaces like RS485 or RS232, the Modbus messages are sent in plain form over the network. In this case the network is dedicated to Modbus.*** When using more versatile network systems like TCP/IP over Ethernet, the Modbus messages are embedded in packets with the format necessary for the physical interface. In that case Modbus and other types of connections can co-exist at the same physical interface at the same time. Although the main Modbus message structure is peer-to-peer, Modbus is able to function on both point-to-point and multi-drop networks.

Each Modbus message has the same structure. Four basic elements are present in each message. The sequence of these elements is the same for all messages, to make it easy to parse the content of the Modbus message. ***A conversation is always started by a master in the Modbus network. A Modbus master sends a message and—depending of the contents of the message—a slave takes action and responds to it. There can be more masters in a Modbus network**. **Addressing in the message header is used to define which device should respond to a message**.* All other nodes on the Modbus network ignore the message if the address field doesn’t match their own address.

| Field              | Description                                          |
| ------------------ | ---------------------------------------------------- |
| **Device address** | Address of the receiver                              |
| **Function code**  | Code defining message type                           |
| **Data**           | Data block with additional information               |
| **Error check**    | Numeric check value to test for communication errors |

## Modbus serial transmission modes: Modbus/ASCII and Modbus/RTU

Serial Modbus connections can use two basic transmission modes, ASCII or RTU(Remote Terminal Unit). The transmission mode in serial communications defines the way the Modbus messages are coded. With Modbus/ASCII, the messages are in a readable [ASCII](https://www.lammertbies.nl/comm/info/ascii-characters) format. The Modbus/RTU format uses binary coding which makes the message unreadable when monitoring, but reduces the size of each message which allows for more data exchange in the same time span. All nodes on one Modbus network segment must use the same serial transmission mode. A device configured to use Modbus/ASCII cannot understand messages in Modbus/RTU and vice versa.

When using Modbus/ASCII, all messages are coded in hexadecimal values, represented with readable ASCII characters. Only the characters 0…9 and A…F are used for coding. For every byte of information, two communication-bytes are needed, because every communication-byte can only define 4 bits in the hexadecimal system. With Modbus/RTU the data is exchanged in a binary format, where each byte of information is coded in one communication-byte.

***Modbus messages on serial connections are not sent in a plain format**.* They are framed to give receivers an easy way to detect the beginning and end of a message. When using Modbus/ASCII, characters are used to start and end a frame. The colon ‘:’ is used to flag the start of a message and each message is ended with a [CR](https://www.lammertbies.nl/comm/info/ascii-characters#carr)/[LF](https://www.lammertbies.nl/comm/info/ascii-characters#line) combination. Modbus/RTU on the other hand uses time gaps of silence on the communication line for the framing. ***Each message must be preceded by a time gap with a minimum length of 3.5 characters**. **If a receiver detects a gap of at least 1.5 characters, it assumes that a new message is comming and the receive buffer is cleared**. **The main advantage of Modbus/ASCII is, that it allows gaps between the bytes of a message with a maximum length of 1 second**. **With Modbus/RTU it is necessary to send each message as a continuous stream.***

|                 | Modbus/ascii                           | Modbus/rtu                             |
| --------------- | -------------------------------------- | -------------------------------------- |
| Characters      | ASCII **0**…**9** and **A**..**F**     | Binary **0**…**255**                   |
| Error check     | **LRC** Longitudinal Redundancy Check  | **CRC** Cyclic Redundancy Check        |
| Frame start     | character ‘**:**‘                      | 3.5 chars silence                      |
| Frame end       | characters **CR**/**LF**               | 3.5 chars silence                      |
| Gaps in message | 1 sec                                  | 1.5 times char length                  |
| Start bit       | **1**                                  | **1**                                  |
| Data bits       | **7**                                  | **8**                                  |
| Parity          | even, odd, none                        | even, odd, none                        |
| Stop bits       | **1** (*even/odd*) **2** (*no parity*) | **1** (*even/odd*) **2** (*no parity*) |

## Modbus addressing

The first information in each Modbus message is the address of the receiver. This parameter contains one byte of information. In Modbus/ASCII it is coded with two hexadecimal characters, in Modbus/RTU one byte is used. ***Valid addresses are in the range 0..247. The values 1..247 are assigned to individual Modbus devices and 0 is used as a broadcast address.*** Messages sent to the latter address will be accepted by all slaves. ***A slave always responds to a Modbus message.*** ***When responding it uses the same address as the master in the request. In this way the master can see that the device is actually responding to the request.***

***Within a Modbus device, the holding registers, inputs and outputs are assigned a number between 1 and 10000.*** One would expect, that the same addresses are used in the Modbus messages to read or set values. Unfortunately this is not the case. ***In the Modbus messages addresses are used with a value between 0 and 9999.*** If you want to read the value of output (coil) 18 for example, you have to specify the value 17 in the Modbus query message. ***More confusing is even, that for input and holding registers an offset must be subtracted from the device address to get the proper address to put in the Modbus message structure.*** This leads to common mistakes and should be taken care of when designing applications with Modbus. The following table shows the address ranges for coils, inputs and holding registers and the way the address in the Modbus message is calculated given the actual address of the item in the slave device.

|       Device address | Modbus address  | Description       |
| -------------------: | --------------- | ----------------- |
|     **1**…**10000*** | address – 1     | Coils (outputs)   |
| **10001**…**20000*** | address – 10001 | Inputs            |
| **40001**…**50000*** | address – 40001 | Holding registers |

## Modbus function codes

The second parameter in each Modbus message is the function code. ***This defines the message type and the type of action required by the slave.*** The parameter contains one byte of information. In Modbus/ASCII this is coded with two hexadecimal characters, in Modbus/RTU one byte is used. ***Valid function codes are in the range 1..255.*** Not all Modbus devices recognize the same set of function codes. The most common codes are discussed here.

***Normally, when a Modbus slave answers a response, it uses the same function code as in the request.*** However, when an error is detected, the highest bit of the function code is turned on. In that way the master can see the difference between success and failure responses.

| Code | Description               |
| ---- | ------------------------- |
| 01   | Read coil status          |
| 02   | Read input status         |
| 03   | Read holding registers    |
| 04   | Read input registers      |
| 05   | Force single coil         |
| 06   | Preset single register    |
| 07   | Read exception status     |
| 15   | Force multiple coils      |
| 16   | Preset multiple registers |
| 17   | Report slave ID           |

### Function 01: Read coil status

***In Modbus language, a coil is a discrete output value***. Modbus function 01 can be used to read the status of such an output. ***It is only possible to query one device at a time**.* ***Broadcast addressing is not supported with this Modbus function.*** ***The function can be used to request the status of various coils at once. This is done by defining an output range in the data field of the message.***

| Byte  | Value           | Description                 |
| :---: | --------------- | --------------------------- |
|   1   | **1**…**247**   | Slave device address        |
|   2   | **1**           | Function code               |
|   3   | **0**…**255**   | Starting address, high byte |
|   4   | **0**…**255**   | Starting address, low byte  |
|   5   | **0**…**255**   | Number of coils, high byte  |
|   6   | **0**…**255**   | Number of coils, low byte   |
| 7(+8) | **LRC**/**CRC** | Error check value           |

When receiving a Modbus query message with function 01, the slave collects the necessary output values and constructs an answer message. ***The length of this message is dependent on the number of values that have to be returned.*** ***In general, when N values are requested, a number of ((N+7) mod 8) bytes are necessary to store these values.*** The actual number of databytes in the data block is put in the first byte of the data field. Therefore the general structure of an answer to a Modbus function 01 query is:

|       Byte        | Value           | Description                |
| :---------------: | --------------- | -------------------------- |
|         1         | **1**…**247**   | Slave device address       |
|         2         | **1**           | Function code              |
|         3         | **0**…**255**   | Number of data bytes **N** |
|     4…**N**+3     | **0**…**255**   | Bit pattern of coil values |
| **N**+4(…**N**+5) | **LRC**/**CRC** | Error check value          |

### Function 02: Read input status

Reading input values with Modbus is done in the same way as reading the status of coils. The only difference is that for inputs Modbus function **02** is used. ***Broadcast addressing mode is not supported.*** ***You can only query the value of inputs of one device at a time. Like with coils, the address of the first input, and the number of inputs to read must be put in the data field of the query message. Inputs on devices start numbering at 10001. This address value is equivalent to address 0 in the Modbus message.***

| Byte  | Value           | Description                 |
| :---: | --------------- | --------------------------- |
|   1   | **1**…**247**   | Slave device address        |
|   2   | **2**           | Function code               |
|   3   | **0**…**255**   | Starting address, high byte |
|   4   | **0**…**255**   | Starting address, low byte  |
|   5   | **0**…**255**   | Number of inputs, high byte |
|   6   | **0**…**255**   | Number of inputs, low byte  |
| 7(+8) | **LRC**/**CRC** | Error check value           |

After receiving a query message with Modbus function **02**, the slave puts the requested input values in a message structure and sends this message back to the Modbus master. The length of the message depends on the number of input values returned. This causes the length of the output message to vary. The number of data bytes in the data field that contain the input values is passed as the first byte in the data field. Each Modbus answering message has the following general structure.

|       Byte        | Value           | Description                 |
| :---------------: | --------------- | --------------------------- |
|         1         | **1**…**247**   | Slave device address        |
|         2         | **2**           | Function code               |
|         3         | **0**…**255**   | Number of data bytes **N**  |
|     4…**N**+3     | **0**…**255**   | Bit pattern of input values |
| **N**+4(…**N**+5) | **LRC**/**CRC** | Error check value           |

### Function 03: Read holding registers

***Internal values in a Modbus device are stored in holding registers.*** These registers are two bytes wide and can be used for various purposes. ***Some registers contain configuration parameters where others are used to return measured values (temperatures etc.) to a host.*** Registers in a Modbus compatible device start counting at **40001**. They are addressed in the Modbus message structure with addresses starting at **0**. Modbus function **03** is used to request one or more holding register values from a device. Only one slave device can be addressed in a single query. Broadcast queries with function **03** are not supported.

| Byte  | Value           | Description                    |
| :---: | --------------- | ------------------------------ |
|   1   | **1**…**247**   | Slave device address           |
|   2   | **3**           | Function code                  |
|   3   | **0**…**255**   | Starting address, high byte    |
|   4   | **0**…**255**   | Starting address, low byte     |
|   5   | **0**…**255**   | Number of registers, high byte |
|   6   | **0**…**255**   | Number of registers, low byte  |
| 7(+8) | **LRC**/**CRC** | Error check value              |

After processing the query, the Modbus slave returns the 16 bit values of the requested holding registers. Because of the size of the holding registers, every register is coded with two bytes in the answering message. The first data byte contains the high byte, and the second the low byte of the register. The Modbus answer message starts with the slave device address and the function code **03**. The next byte is the number of data bytes that follow. This value is two times the number of registers returned. An error check is appended for the host to check if a communication error occurred.