# wazuh-detection-lab
Atomic Red Team emulation for MITRE ATT&CK T1133 (External Remote Services). Contains PowerShell scripts to simulate and clean up registry-based force installation of unauthorized Chrome VPN extensions on Windows, designed for testing SIEM and EDR detection rules.



#### Alert 1 

### Step 1 (Persistence & Impact)

The script (Atomic Test) modified the Chrome browser configuration and installed VPN-related extensions through the Registry, establishing **Registry Persistence / Proxy** behavior.

### Step 2 (Attempted Direct Authentication)

Once `chrome.exe` was automatically launched by the script (`Start chrome`), the browser—or one of the loaded extensions—attempted to authenticate or access locally stored credentials and authentication data (**Credentials / Windows Authentication**).

### Step 3 (Event 4625 Alert)

When `chrome.exe` attempted to perform authentication through the **Advapi** library and the **Negotiate** authentication interface without providing the correct Windows password (or when the extension failed to bypass the authentication requirement), the request was rejected.

As a result, **Windows Security Event ID 4625** was logged with **SubStatus `0xc000006a`**, which indicates that an **incorrect password was provided**.



#### Alert 2





### Step 1 (Initiating the Change)

The script (or the tool executing the Atomic Test) used elevated privileges on the system to modify the configuration of system services through the **Service Control Manager (SCM)**, with the goal of impairing defenses or preparing for stealth.

### Step 2 (Manipulating the Service Startup Type)

The startup type of the **BITS (Background Intelligent Transfer Service)** service was changed from **automatic start (`auto start`)** to **on-demand start (`demand start`)**.

This change can be used to interfere with automatic system updates or disrupt communication channels that security tools may rely on to download security updates and signatures.

### Step 3 (Event 7040 Alert)

The Windows Service Control Manager detected the configuration change at the system level and generated **Event ID 7040** in the `System` log to record the modification to the service startup type.

**Wazuh** then captured the event and classified it under rule **61104** (`Service startup type was changed`).

