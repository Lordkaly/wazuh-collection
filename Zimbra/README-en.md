# Zimbra 8.7 Log Decoder Configuration for Wazuh Agent

This guide describes the steps to configure the Wazuh agent to collect and decode Zimbra logs.

## Configuration Steps

### Step 1: Configure Zimbra Log Collection

1. Edit the Wazuh agent configuration file located at `/var/ossec/etc/ossec.conf`.

2. Add the following XML configuration snippet to specify the Zimbra log file location:

```xml
   <localfile>
     <log_format>syslog</log_format>
     <location>/opt/zimbra/log/audit.log</location>
   </localfile>
```
### Step 2: Restart the Wazuh Agent

Restart the agent to apply the changes:

```bash
systemctl restart wazuh-agent
```

### Step 3: Overwrite Default rule 2501

By default, Zimbra logs will trigger rule 2501. To avoid conflicts with custom Wazuh rules, you need to overwrite this rule.

Edit the custom rules file on your Wazuh server.

Add the following rule, changing the level as needed (recommended values are 0 or 3):

```xml
<rule id="2501" level="0" overwrite="yes">
  <match>FAILED LOGIN |authentication failure|</match>
  <match>Authentication failed for|invalid password for|</match>
  <match>LOGIN FAILURE|auth failure: |authentication error|</match>
  <match>authinternal failed|Failed to authorize|</match>
  <match>Wrong password given for|login failed|Auth: Login incorrect|</match>
  <match>Failed to authenticate user</match>
  <group>authentication_failed,pci_dss_10.2.4,pci_dss_10.2.5,gpg13_7.8,gdpr_IV_35.7.d,gdpr_IV_32.2,hipaa_164.312.b,nist_800_53_AU.14,nist_800_53_AC.7,tsc_CC6.1,tsc_CC6.8,tsc_CC7.2,tsc_CC7.3,</group>
  <description>syslog: User authentication failure.</description>
</rule>
```

### Step 4: Add Custom Wazuh Rules
Add your custom Wazuh rules, ensuring they have a higher level than rule 2501 to prioritize them accordingly.

### Conclusion
After completing these steps, your Wazuh agent will be configured to effectively collect and analyze Zimbra logs.
