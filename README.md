# AdversaryEmulationLab

**Open-Source MITRE ATT&CK Adversary Emulation & Detection Validation Framework**

AdversaryEmulationLab is an open-source security testing framework designed for **Purple Team exercises, Detection Engineering, SOC validation, telemetry generation, and MITRE ATT&CK coverage testing**.

It provides controlled adversary-like activity across Linux, Windows, and macOS environments while helping defenders validate whether endpoint and network telemetry is successfully collected, forwarded, detected, and investigated inside a SIEM.

AdversaryEmulationLab is designed primarily for security labs, defensive security research, detection validation, and authorized security testing.

---

## Why AdversaryEmulationLab?

Creating a detection rule does not guarantee that the behavior will actually be detected.

A complete detection pipeline includes multiple layers:

```text
ATT&CK Technique
       ↓
Technique Execution
       ↓
Endpoint / Network Telemetry
       ↓
Log Collection
       ↓
Log Forwarding
       ↓
SIEM Ingestion
       ↓
Detection Rule
       ↓
SOC Investigation
```

A failure at any stage can create a visibility gap.

AdversaryEmulationLab helps security teams validate this entire workflow.

---

# Key Features

AdversaryEmulationLab provides components for:

- MITRE ATT&CK technique execution
- Adversary behavior simulation
- Atomic Red Team integration
- Linux telemetry generation
- Windows telemetry generation
- macOS monitoring
- Sysmon configuration
- Sysmon for Linux
- Auditd monitoring
- PowerShell logging
- Windows Security Auditing
- Zeek network monitoring
- Splunk log ingestion
- Splunk detection engineering
- Splunk dashboards
- Detection rule validation
- ATT&CK coverage validation
- Purple Team exercises
- SOC analyst training

---

# Supported Platforms

## Linux

Linux monitoring and telemetry generation can include:

- Auditd
- Sysmon for Linux
- Bash activity
- Process execution
- File activity
- Network connections
- System calls
- `/proc` activity
- Command execution
- Atomic Red Team tests

---

## Windows

Windows telemetry can include:

- Sysmon
- Windows Security Event Logs
- PowerShell Script Block Logging
- PowerShell Module Logging
- Process creation
- Network connections
- File creation
- Registry activity
- Account activity
- Atomic Red Team tests

---

## macOS

AdversaryEmulationLab also provides components for monitoring and validating activity on macOS systems.

Available telemetry depends on the logging and collection configuration deployed in the environment.

---

## Network

Network visibility can be provided through tools such as:

- Zeek
- DNS monitoring
- Connection logging
- Protocol analysis
- Network metadata collection

---

# MITRE ATT&CK

AdversaryEmulationLab uses the MITRE ATT&CK framework to organize adversary behaviors and detection validation.

Tests can cover tactics including:

```text
Reconnaissance
Resource Development
Initial Access
Execution
Persistence
Privilege Escalation
Defense Evasion
Credential Access
Discovery
Lateral Movement
Collection
Command and Control
Exfiltration
Impact
```

Technique availability depends on the operating system and lab environment.

---

# Atomic Red Team Integration

AdversaryEmulationLab can work alongside **Atomic Red Team** to execute publicly documented ATT&CK tests.

Atomic Red Team provides small security tests mapped directly to MITRE ATT&CK techniques.

Example workflow:

```text
Atomic Red Team
       ↓
ATT&CK Technique
       ↓
Controlled Execution
       ↓
Endpoint Telemetry
       ↓
Splunk
       ↓
Detection Validation
```

This allows defenders to determine whether security controls can observe and detect specific attacker behaviors.

---

# Detection Stack

AdversaryEmulationLab is not limited to technique execution.

The project also focuses on the defensive side of the validation process.

## Linux Detection Stack

Typical components include:

```text
Linux Endpoint
    │
    ├── Auditd
    ├── Sysmon for Linux
    ├── System Logs
    │
    ▼
Splunk Universal Forwarder
    │
    ▼
Splunk
```

---

## Windows Detection Stack

Typical components include:

```text
Windows Endpoint
    │
    ├── Sysmon
    ├── Windows Security Logs
    ├── PowerShell Logging
    ├── Process Auditing
    │
    ▼
Splunk Universal Forwarder
    │
    ▼
Splunk
```

---

## Network Detection Stack

```text
Network Traffic
      │
      ▼
     Zeek
      │
      ▼
Network Telemetry
      │
      ▼
    Splunk
```

---

# Splunk Integration

AdversaryEmulationLab contains detection-oriented content that can be used with Splunk.

This may include:

- SPL detection queries
- Saved searches
- Dashboards
- Lookups
- Macros
- Index configuration
- Parsing configuration
- ATT&CK technique mapping
- Detection validation searches

---

# Example Splunk Queries

## Verify Available Data

```spl
index=*
| stats count by host sourcetype source
| sort - count
```

---

## Process Creation

```spl
index=* (EventCode=1 OR EventID=1)
| table _time host user Image CommandLine ParentImage
```

---

## Network Connections

```spl
index=* (EventCode=3 OR EventID=3)
| table _time host Image SourceIp SourcePort DestinationIp DestinationPort
```

---

## File Creation

```spl
index=* (EventCode=11 OR EventID=11)
| table _time host Image TargetFilename
```

---

## DNS Queries

```spl
index=* (EventCode=22 OR EventID=22)
| table _time host Image QueryName
```

---

## Linux Auditd Events

```spl
index=* sourcetype=*audit*
| table _time host type exe comm syscall key
```

---

# Detection Validation Workflow

A typical AdversaryEmulationLab validation exercise consists of the following stages.

## 1. Select a Technique

Select the MITRE ATT&CK technique you want to validate.

Example:

```text
T1059
Command and Scripting Interpreter
```

---

## 2. Execute the Test

Run the corresponding test in the authorized lab environment.

Execution can be performed using:

- AdversaryEmulationLab scripts
- Atomic Red Team
- PowerShell
- Bash
- Python
- Other controlled testing methods

---

## 3. Verify Endpoint Telemetry

Check whether the behavior produced the expected logs.

For example:

```text
Sysmon
Auditd
Windows Event Logs
PowerShell Logs
Zeek Logs
```

---

## 4. Verify Log Forwarding

Confirm that logs reached the central monitoring platform.

Example:

```spl
index=*
| stats count by host sourcetype
```

---

## 5. Validate the Detection

Run or verify the corresponding detection rule.

Determine whether the activity was:

```text
Detected
Telemetry Available
Detection Missing
Telemetry Missing
Test Failed
Not Applicable
```

---

## 6. Investigate the Alert

Review the event as a SOC analyst would during a real investigation.

Useful information may include:

- Parent process
- Child process
- Command line
- User
- Source host
- Destination
- File path
- Hash
- DNS query
- Network connection
- Technique ID
- Detection rule

---

# Example Architecture

```text
                AdversaryEmulationLab
                     │
          Adversary Emulation
                     │
       ┌─────────────┼─────────────┐
       │             │             │
       ▼             ▼             ▼
     Linux        Windows        macOS
       │             │             │
    Auditd         Sysmon        Logging
 Sysmon Linux      Windows          │
       │           Events           │
       └─────────────┬───────────────┘
                     │
                     ▼
              Log Collection
                     │
                     ▼
        Splunk Universal Forwarder
                     │
                     ▼
                  Splunk
                     │
           ┌─────────┴─────────┐
           │                   │
           ▼                   ▼
      Detection Rules       Dashboards
           │
           ▼
       SOC Validation
```

---

# Use Cases

AdversaryEmulationLab can be used for:

### Detection Engineering

Test whether detection rules actually identify the behaviors they were designed to detect.

### Purple Team Exercises

Allow Red Team and Blue Team personnel to validate attack visibility together.

### SOC Validation

Verify that analysts receive the expected events and alerts.

### SIEM Validation

Confirm that telemetry is properly ingested, normalized, searched, and correlated.

### ATT&CK Coverage Assessment

Evaluate which ATT&CK techniques have sufficient telemetry and detection coverage.

### Telemetry Generation

Generate controlled security events for testing pipelines and dashboards.

### Security Training

Provide realistic telemetry for SOC analysts and security students.

### Security Research

Experiment with ATT&CK techniques and defensive telemetry in isolated environments.

---

# Recommended Repository Structure

A clean deployment of AdversaryEmulationLab can follow this structure:

```text
AdversaryEmulationLab/
│
├── attack/
│   ├── linux/
│   ├── windows/
│   └── helpers/
│
├── detection/
│   ├── linux/
│   ├── windows/
│   ├── macos/
│   └── network/
│
├── splunk/
│   ├── app/
│   ├── detections/
│   ├── dashboards/
│   ├── lookups/
│   └── configs/
│
├── docs/
│   ├── installation/
│   ├── techniques/
│   └── detection-validation/
│
├── manifest.json
├── VALIDATION.json
├── LICENSE
└── README.md
```

---

# Requirements

Exact requirements depend on the tests being executed.

## Common Linux Requirements

```text
Bash
Python 3
PowerShell 7
Auditd
Sysmon for Linux
Splunk Universal Forwarder
Atomic Red Team
```

---

## Common Windows Requirements

```text
PowerShell
Sysmon
Windows Event Logging
Splunk Universal Forwarder
Atomic Red Team
```

---

## Detection Platform

AdversaryEmulationLab is designed to work especially well with:

```text
Splunk Enterprise
Splunk Enterprise Security
```

The concepts and telemetry can also be adapted to other SIEM platforms.

---

# Important Safety Notice

AdversaryEmulationLab contains security testing capabilities that may execute behaviors commonly associated with real adversaries.

**Only run AdversaryEmulationLab on systems that you own or have explicit permission to test.**

Some tests may:

- Create or delete files
- Execute commands
- Launch processes
- Generate network traffic
- Modify temporary configuration
- Trigger antivirus alerts
- Trigger EDR detections
- Generate SIEM alerts
- Modify operating system settings

Always review a test before executing it.

An isolated security laboratory is strongly recommended.

---

# Project Philosophy

AdversaryEmulationLab is built around one simple principle:

> A detection is only useful if it can be validated.

The purpose of the project is to answer questions such as:

```text
Did the technique execute?

Did it generate telemetry?

Did the endpoint record it?

Did the collector receive it?

Did the SIEM ingest it?

Did the detection rule trigger?

Can the SOC investigate it?
```

Finding the answer to these questions helps defenders identify gaps before those gaps are exploited during a real incident.

---

# Responsible Use

AdversaryEmulationLab is intended exclusively for:

- Authorized penetration testing
- Purple Team exercises
- Detection Engineering
- Security research
- Security education
- SOC training
- Defensive validation
- Laboratory environments

Do not use this project against systems or networks without authorization.

---

# Contributing

Contributions are welcome.

Useful contributions include:

- New ATT&CK technique tests
- New Splunk detections
- Detection improvements
- Sysmon configurations
- Auditd rules
- Zeek detections
- macOS monitoring improvements
- Documentation
- Bug fixes
- ATT&CK mappings
- Detection validation scenarios

For a new technique, consider documenting:

```text
Technique ID
Technique Name
Operating System
Execution Method
Required Privileges
Expected Telemetry
Required Logging
Detection Query
Cleanup Procedure
Known Limitations
```

---

# Disclaimer

This project is provided for educational, research, defensive security, and authorized testing purposes only.

The author and contributors are not responsible for misuse, unauthorized activity, system damage, data loss, service disruption, or other consequences resulting from the use of this project.

Users are responsible for ensuring that all testing activities comply with applicable laws, policies, and authorization requirements.

---

# References

- MITRE ATT&CK
- Atomic Red Team
- Sysmon
- Sysmon for Linux
- Auditd
- Zeek
- Splunk

---

# Author

**Mohamad Yaghoobi**

Security Research · Purple Team · Detection Engineering

---

# License

An open-source license such as **MIT**, **Apache-2.0**, or **GPL-3.0** should be added before public distribution.

---

⭐ If AdversaryEmulationLab helps with your security research, detection engineering, or Purple Team exercises, consider starring the repository.
