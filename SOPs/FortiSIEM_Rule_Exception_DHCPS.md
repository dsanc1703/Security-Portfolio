<title>DHCPS SOP | Security Portfolio</title>

<div style="max-width: 1100px; margin: 0 auto;">

[← Back](<./>)

[View PDF Version](<./FortiSIEM_Rule_Exception_DHCPS.pdf>)

# Problem

With initial rule exception being too broad, granularity was needed to ensure denied DHCPS traffic will be excluded by reviewing Source IPs of known DHCP servers as well as the Source/Destination Ports

The current rule exception was based on Destination Ports 67/68. This was due to the necessary attributes not appearing under the "Attribute" dropdown during exception creation.

# Actions Needed

### To have *Source IP* and *Source TCP/UDP Port* attributes appear under the *Attribute* dropdown:

1. Log in at \[FQDN_FortiSIEMServer\] with a 'Full Admin' account
2. Go to **Resources > Rules**
3. Select rule **_Sudden Increase in Firewall Denied Outbound Traffic To A Specific TCP/UDP port_**
4. Click **Edit** and select **Step 2: Define Condition**
5. Next to Subpattern **StatHighPort**, click **Edit** to open **Edit SubPattern** window
6. Under **Group By**, add a new row at the bottom
7. Set new row **Attribute** as **Source IP** (This forces the rule to recognize the attribute and its value)
8. Repeat Steps 6-7, with **Attribute** as **Source TCP/UDP Port**
9. Click **Save**
10. Go to **Step 3: Define Action**
11. Next to **Action**, click **Edit**
12. Add a new **Incident Attribute** row at the bottom with the following:
    1. Set **Event Attribute** as **Source IP**
    2. Set **Subpattern** as **StatHighPort**
    3. Set **Filter Attribute** as **Source IP**
13. Add a new **Incident Attribute** row at the bottom with the following:
    1. Set **Event Attribute** as **Source TCP/UDP Port**
    2. Set **Subpattern** as **StatHighPort**
    3. Set **Filter Attribute** as **Source TCP/UDP Port**
14. Click **Save**

### To add *Source IP* and *Source TCP/UDP Port* on previous existing rule exception:

1. Log in at \[FQDN_FortiSIEMServer\] with a 'Full Admin' account
2. Go to **Resources > Rules**
3. Select rule **_Sudden Increase in Firewall Denied Outbound Traffic To A Specific TCP/UDP port_**
4. Click **Edit** and select **Step 3: Define Action**
5. Next to **Exceptions**, click **Edit**
6. With one pre-existing **Destination Port** condition:
    1. Add **Paren** before it
    2. Set **Operator** as **\=**
    3. Set **Value** as **68**
    4. Set **Next** as **AND**
7. For new condition directly below:
    1. Set **Attribute** as **Source IP**
    2. Set **Operator** as **IN**
    3. Set **Value as \[IPs_Of_DHCPServers\]**
    4. Set **Next** as **AND**
8. For new condition directly below:
    1. Set **Attribute** as **Source TCP/UDP Port**
    2. Set **Operator** as **\=**
    3. Set **Value** as **67**
    4. Add **Paren** after it
9. For **Notes**, specify which condition set is for DHCPS traffic exception
10. Click **Save**

</div>