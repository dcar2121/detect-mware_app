# Malware Prevention System - Configuration & Best Practices Guide

## Configuration Examples

### 1. Default Security Policy

```python
# File Monitoring Configuration
MONITORED_PATHS = [
    "C:\\Users\\admin\\Desktop",
    "C:\\Users\\admin\\Documents",
    "C:\\Windows\\Temp",
    "C:\\ProgramData"
]

SUSPICIOUS_EXTENSIONS = [
    '.exe', '.bat', '.vbs', '.scr', '.pif', '.com',
    '.dll', '.sys', '.drv', '.cpl', '.ocx'
]

MAX_FILE_SIZE = 500 * 1024 * 1024  # 500MB - executables larger = suspicious

# Network Monitoring Configuration
BLOCKED_PORTS = [
    4444, 5555, 6666, 9999,  # Common backdoor ports
    27374,  # SubSeven
    31337,  # Elite backdoor
]

# Behavioral Analysis Thresholds
BEHAVIOR_THRESHOLDS = {
    'file_enumeration': {
        'count': 100,
        'timeframe': 60,  # seconds
        'severity': 'CRITICAL'
    },
    'registry_modification': {
        'count': 50,
        'timeframe': 60,
        'severity': 'HIGH'
    },
    'process_injection': {
        'count': 5,
        'timeframe': 30,
        'severity': 'CRITICAL'
    },
    'network_scan': {
        'count': 20,
        'timeframe': 60,
        'severity': 'HIGH'
    }
}
```

### 2. Threat Signature Database

```python
# Add custom signatures based on your environment

THREAT_SIGNATURES = {
    'ransom_notes': [
        r'.*readme\.txt.*',
        r'.*howto_restore.*',
        r'.*_recovery_.*',
    ],
    'malware_indicators': [
        r'.*HKEY_LOCAL_MACHINE\\Software\\Microsoft\\Windows\\CurrentVersion\\Run.*',
        r'.*powershell.*hidden.*',
        r'.*cmd\.exe.*\/c.*taskkill.*',
    ],
    'suspicious_behavior': [
        r'.*WriteProcessMemory.*',
        r'.*CreateRemoteThread.*',
        r'.*SetWindowsHookEx.*',
    ],
    'known_malware': [
        # Add known malware hashes here
        'hash1_of_known_malware',
        'hash2_of_known_malware',
    ]
}
```

### 3. Incident Response Playbooks

```python
# Define your incident response procedures

INCIDENT_PLAYBOOKS = {
    'ransomware': {
        'severity': 'CRITICAL',
        'actions': [
            {
                'step': 1,
                'action': 'ISOLATE',
                'description': 'Disconnect affected systems from network',
                'timeout': 60
            },
            {
                'step': 2,
                'action': 'BACKUP_PRESERVE',
                'description': 'Preserve backup and system logs',
                'timeout': 300
            },
            {
                'step': 3,
                'action': 'NOTIFY',
                'description': 'Alert security team and management',
                'timeout': 30
            },
            {
                'step': 4,
                'action': 'INVESTIGATE',
                'description': 'Analyze attack vector and scope',
                'timeout': 3600
            },
            {
                'step': 5,
                'action': 'RESTORE',
                'description': 'Restore from clean backups',
                'timeout': 7200
            }
        ]
    },
    'data_breach': {
        'severity': 'CRITICAL',
        'actions': [
            {
                'step': 1,
                'action': 'IDENTIFY',
                'description': 'Identify what data was compromised',
                'timeout': 1800
            },
            {
                'step': 2,
                'action': 'CONTAIN',
                'description': 'Stop ongoing data exfiltration',
                'timeout': 600
            },
            {
                'step': 3,
                'action': 'NOTIFY',
                'description': 'Notify affected parties',
                'timeout': 7200
            },
            {
                'step': 4,
                'action': 'INVESTIGATE',
                'description': 'Determine breach cause',
                'timeout': 3600
            }
        ]
    },
    'malware_spread': {
        'severity': 'HIGH',
        'actions': [
            {
                'step': 1,
                'action': 'QUARANTINE',
                'description': 'Isolate infected systems',
                'timeout': 300
            },
            {
                'step': 2,
                'action': 'SCAN',
                'description': 'Full antivirus/malware scan',
                'timeout': 3600
            },
            {
                'step': 3,
                'action': 'BLOCK',
                'description': 'Block malicious IPs and domains',
                'timeout': 600
            }
        ]
    }
}
```

## Deployment Best Practices

### 1. File Monitoring Setup

**Good:**

```python
# Monitor user-writable directories
MONITOR_PATHS = [
    "C:\\Users",
    "C:\\ProgramData",
    "C:\\Windows\\Temp",
]

# Exclude noisy directories
EXCLUDE_PATHS = [
    "C:\\Program Files\\*",  # Trusted installations
    "C:\\Windows\\System32\\*",  # System files
    "C:\\Temp\\*",  # Temporary files
]
```

**Better:**

```python
# Use whitelist approach for critical systems
WHITELISTED_LOCATIONS = [
    "C:\\Program Files\\Microsoft Office\\*",
    "C:\\Program Files\\Chrome\\*",
]

RESTRICTED_LOCATIONS = [
    "C:\\Users\\*\\AppData\\Roaming\\*",  # High-risk area
    "C:\\ProgramData\\*",
]

# Monitor only restricted locations
def should_monitor(path):
    return any(path.startswith(r) for r in RESTRICTED_LOCATIONS)
```

### 2. Network Monitoring Setup

**Effective Rules:**

```python
# Block known malicious infrastructure
MALICIOUS_IPS = [
    "192.168.1.100",  # Example C&C server
    "10.0.0.50",      # Known bot master
]

BLOCKED_DOMAINS = [
    "malware.com",
    "c2server.net",
]

# Monitor suspicious ports
ALERT_ON_PORTS = [
    4444, 5555,  # Backdoors
    27374,       # SubSeven
    31337,       # Elite
    # Add more as needed
]
```

### 3. Performance Optimization

```python
# Balance security with performance

# Scan frequency
QUICK_SCAN_INTERVAL = 300  # 5 minutes for quick scans
FULL_SCAN_INTERVAL = 3600  # 1 hour for full scans

# File hashing
HASH_FILES_LARGER_THAN = 50 * 1024 * 1024  # Only large files
HASH_ALGORITHM = 'SHA256'  # Fast and secure

# Threading
MAX_SCANNER_THREADS = 4  # Adjust based on CPU cores
MAX_NETWORK_MONITORS = 2

# Caching
CACHE_FILE_HASHES = True
CACHE_EXPIRY = 3600  # 1 hour
```

## Threat Hunting Procedures

### 1. Malware Hunting Checklist

- [ ] Review recent file modifications in monitored paths
- [ ] Check for unexpected process injections
- [ ] Analyze network connections to unknown IPs
- [ ] Search for suspicious scheduled tasks
- [ ] Examine registry modifications
- [ ] Check Windows Event Logs for errors
- [ ] Review antivirus quarantine logs
- [ ] Analyze outbound connections
- [ ] Look for unusual file extensions
- [ ] Check for disabled security software

### 2. Investigation Workflow

```
1. Alert Received
   ├─ Severity Assessment
   ├─ Scope Determination
   └─ Containment Decision

2. Containment
   ├─ Isolate System(s)
   ├─ Preserve Evidence
   └─ Stop Spread

3. Investigation
   ├─ Root Cause Analysis
   ├─ Attack Timeline
   ├─ Affected Systems
   └─ Compromised Data

4. Eradication
   ├─ Remove Malware
   ├─ Fix Vulnerabilities
   └─ Restore Systems

5. Recovery
   ├─ System Restoration
   ├─ Configuration Hardening
   └─ Monitoring Enhancement

6. Lessons Learned
   ├─ Update Procedures
   ├─ Improve Detection
   └─ Training Updates
```

## Security Hardening Recommendations

### 1. System Level

- Enable Windows Defender
- Keep OS patched and updated
- Enable Windows Firewall
- Disable unnecessary services
- Implement UAC (User Account Control)

### 2. Network Level

- Use network segmentation
- Deploy EDR (Endpoint Detection & Response)
- Enable network monitoring
- Implement IDS/IPS
- Use VPN for remote access

### 3. Application Level

- Keep applications patched
- Disable unnecessary features
- Implement application whitelisting
- Use sandboxing for suspicious files
- Enable code signing verification

### 4. User Level

- Security awareness training
- Phishing simulation exercises
- Email filtering
- Safe browsing practices
- Multi-factor authentication

## Compliance Integration

### Mapping to Frameworks

**NIST CSF:**

- Prevent (PR): File monitoring, Network filtering
- Detect (DE): Alert system, Behavioral analysis
- Respond (RS): Incident playbooks, Automated response
- Recover (RC): Backup integration, Recovery procedures

**CIS Controls:**

- #2: Inventory of Authorized Software
- #5: Access Control
- #6: Secure Configuration
- #13: Incident Response

**ISO 27001:**

- A.12.4: Logging and monitoring
- A.13: Communications security
- A.16: Incident management

## Monitoring & Metrics

### Key Performance Indicators

```
Detection Metrics:
  • Mean Time to Detect (MTTD): <5 minutes
  • False Positive Rate: <2%
  • Detection Accuracy: >98%

Response Metrics:
  • Mean Time to Respond (MTTR): <15 minutes
  • Incident Resolution Time: <1 hour
  • Containment Success Rate: >95%

System Metrics:
  • System Resource Usage: <10% CPU
  • Scanner Uptime: >99.9%
  • Alert Queue Processing: <30 seconds
```

## Incident Documentation

### Required Information

- Incident ID and timestamp
- Detection method
- Affected systems
- Attack indicators
- Severity level
- Response actions taken
- Investigation findings
- Root cause
- Remediation steps
- Preventive measures

## Contact & Escalation

**Escalation Matrix:**

- CRITICAL → Immediate CTO notification + Law Enforcement
- HIGH → Security Lead + System Owner
- MEDIUM → Security Team + Logging
- LOW → Logging only

---

**Last Updated:** May 2026
**Version:** 1.0
