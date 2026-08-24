# 01 - Configuration Bits

## 1. What Are Configuration Bits?

Configuration Bits are non-volatile device settings programmed together with the firmware.

They define fundamental MCU behavior such as:

* startup clock source
* reset behavior
* Watchdog Timer configuration
* programming mode
* write/code protection
* PPS and interrupt-vector locking behavior

Configuration Bits are configuration data, not executable instructions.

---

## 2. Configuration Bits vs. SFRs

Configuration Bits and Special Function Registers (SFRs) serve different purposes.

|                    | Configuration Bits            | SFRs                       |
| ------------------ | ----------------------------- | -------------------------- |
| Storage            | Configuration Memory          | Data Memory                |
| Configuration time | Programming                   | Runtime                    |
| Modified by        | Programmer / firmware image   | CPU instructions           |
| Typical use        | Device-level startup behavior | Peripheral and CPU control |
| XC8 syntax         | `#pragma config`              | Register access            |

Conceptually:

```text id="p40u4v"
Configuration Bits
        ↓
device startup configuration

SFRs
        ↓
runtime configuration
```

A setting may have both configuration-time and runtime controls. The corresponding runtime behavior is covered in the relevant peripheral section.

---

## 3. Where Is Configuration Memory?

PIC18F16Q41 contains a dedicated Configuration Memory area.

The Configuration Bytes are located at:

| Address    | Register  |
| ---------- | --------- |
| `0x300000` | `CONFIG1` |
| `0x300001` | `CONFIG2` |
| `0x300002` | `CONFIG3` |
| `0x300003` | `CONFIG4` |
| `0x300004` | `CONFIG5` |
| `0x300005` | `CONFIG6` |
| `0x300006` | `CONFIG7` |
| `0x300007` | `CONFIG8` |
| `0x300008` | `CONFIG9` |

These addresses are separate from normal Program Flash and runtime Data Memory.

---

## 4. What Is `CONFIGx`?

`CONFIG1` through `CONFIG9` are Configuration Bytes.

Each byte contains one or more configuration fields.

For example, `CONFIG1` contains:

```text id="mfo1iv"
CONFIG1
├── RSTOSC[2:0]
└── FEXTOSC[2:0]
```

Other Configuration Bytes contain fields related to reset, Watchdog Timer, programming, protection and other device-level options.

The datasheet is the source of truth for:

* field location
* bit position
* available values
* value meaning

---

## 5. How Does XC8 `#pragma config` Work?

XC8 provides the `#pragma config` directive for specifying Configuration Bits.

General form:

```c id="htk5v9"
#pragma config FIELD = OPTION
```

For example:

```c id="6i437a"
#pragma config RSTOSC = ...
```

The selected configuration is processed by XC8 and placed into the corresponding Configuration Memory location in the generated firmware image.

```text id="9wvs4q"
#pragma config
      ↓
XC8 compiler / linker
      ↓
firmware image
      ↓
programmer
      ↓
Configuration Memory
```

`#pragma config` describes configuration values; it does not execute as runtime code.

---

## 6. How to Look Up a Configuration Bit

Use the **Device Configuration** section of the PIC18F06/16Q41 datasheet.

Typical lookup flow:

```text id="7uwg39"
Device Configuration
        ↓
Register Summary - Configuration Settings
        ↓
CONFIGx
        ↓
field definition
```

For a field such as `RSTOSC`, identify:

1. Configuration Byte
2. bit position
3. field width
4. available encoded values
5. meaning of each value

Example:

```text id="hm9l8q"
RSTOSC
   ↓
CONFIG1
   ↓
RSTOSC[2:0]
   ↓
available values
```

The Configuration Register Summary provides a compact overview of all fields and their locations.

---

## 7. How to Find the XC8 Value

The datasheet defines the hardware field and its encoded values.

XC8 defines the symbolic names accepted by:

```c id="0r6lpp"
#pragma config FIELD = OPTION
```

Do not infer an XC8 option name directly from the binary value in the datasheet.

Use the device-specific XC8 configuration definitions or compiler documentation to determine the valid symbolic options.

Workflow:

```text id="qzshqi"
Datasheet
   ↓
configuration field
   ↓
hardware meaning

XC8 definitions
   ↓
#pragma config field
   ↓
valid symbolic option
```

---

## 8. Baseline Configuration

The following exercises should use one fixed Configuration Bit set unless a specific configuration field is being studied.

A common project structure is:

```text id="qgb0km"
Project/
├── README.md
├── config.h
└── main.c
```

`config.h` contains Configuration Bit declarations:

```c id="674jdy"
#pragma config ...
```

Runtime peripheral initialization remains outside the Configuration Bit definition.

This separation keeps:

```text id="ai6597"
config.h
   ↓
device configuration

main.c / peripheral code
   ↓
runtime behavior
```

The baseline configuration should remain stable across later examples and only change when the relevant feature requires it.
