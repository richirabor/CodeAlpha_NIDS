# CodeAlpha_NIDS
# Network Intrusion Detection System (NIDS)
---
## Project Overview
This project implements a Network Intrusion Detection System (NIDS) using Suricata on Kali Linux. The system monitors network traffic visible to the configured interface, evaluates traffic against detection rules, and generates alerts for events matching those rules.

The project demonstrates the fundamentals of network security monitoring, rule configuration, event logging, alert investigation, and incident response.

---
## Objectives

- Install and configure Suricata.
- Monitor network traffic on a selected interface.
- Configure custom detection rules.
- Generate and inspect security alerts.
- Investigate detected events and document response procedures.
- Optionally visualize events using a security dashboard.

---
## Tools and Technologies
---
| Tool     | Purpose |
|--------  |-------  |
| Kali Linux | Security testing environment |
| VirtualBox | Virtual lab environment |
| Suricata | Network intrusion detection engine |
| jq | Filtering JSON event logs |
| curl and ping | Generating authorized test traffic |
| Git and GitHub | Version control and project documentation |

---
## System Architecture
---
Test VM / Authorized Lab Traffic → Monitored Network Interface → Suricata Detection Engine → Detection Rules → Alert Logs → Investigation and Response

---
## Installation
---
Update Kali Linux and install the required packages:

```bash
sudo apt update
sudo apt install suricata jq curl iproute2 -y
```

Verify the installation:

```bash
suricata --build-info
```

Update the available detection rules:

```bash
sudo suricata-update
```

## Network Interface Configuration

Identify available interfaces:

```bash
ip -br addr
ip route
```

Select the interface that carries the lab traffic to be inspected. Configure the appropriate network range in `/etc/suricata/suricata.yaml`. The `HOME_NET` setting must reflect the actual lab network.

## Custom Detection Rules

Create a local rules file, typically at `/var/lib/suricata/rules/local.rules`, and add the following demonstration rules:

```text
alert icmp any any -> any any (msg:"LAB ICMP Traffic Detected"; itype:8; sid:1000001; rev:1;)
alert http any any -> any any (msg:"LAB HTTP GET Request Detected"; flow:established,to_server; http.method; content:"GET"; sid:1000002; rev:1;)
```

Ensure `local.rules` is included in the `rule-files` section of the Suricata configuration. These rules detect ICMP echo requests and decoded HTTP GET requests. They are demonstration rules and do not, by themselves, prove malicious activity.

Validate the configuration:

```bash
sudo suricata -T -c /etc/suricata/suricata.yaml
```

## Monitoring and Logging

Check the Suricata service:

```bash
sudo systemctl status suricata
```

If required, start the service:

```bash
sudo systemctl enable --now suricata
```

Inspect human-readable alerts:

```bash
sudo tail -f /var/log/suricata/fast.log
```

Inspect JSON events:

```bash
sudo tail -f /var/log/suricata/eve.json
```

Log paths and logging options may vary by configuration. For direct interface monitoring, use the correct interface and avoid running a second Suricata instance against the same interface and logs.

## Testing and Validation

### Test 1: ICMP Detection

Generate ping traffic between authorized lab machines on the monitored network:

```bash
ping -c 4 <LAB_VM_IP>
```

Replace `<LAB_VM_IP>` with the actual IP address of the test machine. Check whether the `LAB ICMP Traffic Detected` alert appears.

### Test 2: HTTP GET Detection

On a lab machine that you control, start a temporary HTTP server:

```bash
python3 -m http.server 8000 --bind 0.0.0.0
```

From another authorized lab machine, issue a request to the server's actual IP address:

```bash
curl http://<LAB_SERVER_IP>:8000/
```

Check whether the `LAB HTTP GET Request Detected` alert appears. The HTTP rule requires traffic that Suricata can observe and decode; it does not ordinarily inspect encrypted HTTPS requests.

### Test Results

Record the real results after executing the tests:

| Test | Expected result | Actual result |
|---|---|---|
| Suricata configuration validation | Configuration loads successfully | Pending verification |
| ICMP detection | Matching ICMP alert | Pending verification |
| HTTP GET detection | Matching HTTP alert | Pending verification |
| Event logging | Events recorded in configured logs | Pending verification |

Update the result column with your observed outcomes and include screenshots in the repository.

## Alert Investigation

Extract alert details from the EVE JSON log:

```bash
sudo jq 'select(.event_type == "alert") | {timestamp, src_ip, dest_ip, alert: .alert.signature, severity: .alert.severity}' /var/log/suricata/eve.json
```

Review the timestamp, source and destination addresses, rule signature, severity, and relevant surrounding events. Determine whether an alert represents normal activity, suspicious behavior, or a confirmed incident before taking action.

## Incident Response Procedure

1. **Detect:** Identify the event through a Suricata alert.
2. **Investigate:** Review the event details and related logs.
3. **Validate:** Assess whether the activity is malicious or legitimate.
4. **Contain:** If warranted, use approved firewall or network controls to isolate the affected system or block confirmed malicious traffic.
5. **Recover:** Restore affected systems only after appropriate checks and remediation.
6. **Document:** Record the evidence, actions taken, and lessons learned.

This project uses detection-only monitoring. Alerts do not automatically block traffic unless a separate response mechanism is deliberately configured and tested. Automatic blocking should not be based solely on the demonstration rules because legitimate traffic can trigger them.

## Optional Visualization

A compatible security dashboard, such as EveBox or a log analytics platform, can be integrated to visualize alert counts, signatures, severity levels, source addresses, and event trends. Dashboard functionality should only be reported as implemented after it has been configured and verified.

## Project Structure

```text
network-intrusion-detection-system/
├── README.md
├── rules/
│   └── local.rules
├── screenshots/
│   ├── suricata-installation.png
│   ├── configuration-test.png
│   ├── icmp-alert.png
│   └── http-alert.png
└── reports/
    └── findings.md
```

Add screenshots of your actual installation, successful configuration validation, and observed alerts. Store a copy of your custom rules in `rules/local.rules`.

## Limitations

- Suricata can only inspect traffic visible to its configured capture interface.
- VirtualBox networking mode affects which packets are visible.
- Encrypted application traffic limits inspection of application-layer content.
- Demonstration rules can generate alerts for legitimate activity.
- Detection effectiveness depends on rule quality, updates, configuration, and network visibility.
- This lab does not establish that a real attack has occurred merely because a test alert was generated.

## Security and Ethical Considerations

All tests should be conducted on systems and networks owned by the tester or used with explicit authorization. Avoid collecting unrelated traffic or publishing sensitive IP addresses, credentials, or personal data in public logs.

## Conclusion

This project demonstrates the basic implementation of a Suricata-based Network Intrusion Detection System. It covers installation, rule configuration, network monitoring, alert logging, event investigation, and incident response planning. The project can be extended with additional detection rules, tuned alert thresholds, automated notifications, and dashboard integration.

---
## 👤 Author
---

Irabor Richard Ehis

CA/DF1/316498

LinkedIn: https://www.linkedin.com/in/richard-ehis-irabor-46b8899b/

---
## 📌 Project Information

Program Name: Cybersecurity at CodeAlpha | Project: Network Intrusion Detection | Repository: GitHub
