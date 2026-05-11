<title>DHCPC SOP | Security Portfolio</title>

<div style="max-width: 1100px; margin: 0 auto;">

[← Back](<./>)

[View PDF Version](<./FortiSIEM_Rule_Exception_DHCPC.pdf>)

# Problem

With initial rule exception being too broad, granularity was needed to ensure denied DHCPC traffic will be blocked by reviewing both the Source and Destination Ports.

The current rule exception was based on Destination Ports 67/68. This was due to the necessary attributes not appearing under the "Attribute" dropdown during exception creation.

# Actions Needed

### To have **Source TCP/UDP Port** attribute appear under the Attribute dropdown:

1. Log in at \[FQDN_FortiSIEMServer\] with a 'Full Admin' account
2. Go to **Resources > Rules**
3. Select rule **_Sudden Increase in Firewall Denied Outbound Traffic To A Specific TCP/UDP port_**
4. Click **Edit** and select **Step 2: Define Condition**
5. Next to Subpattern **StatHighPort**, click **Edit** to open **Edit SubPattern** window
6. Under **Group By**, add a new row at the bottom
7. Set new row **Attribute** as **Source TCP/UDP Port** (This forces the rule to recognize the attribute and its value)
8. Click **Save**
9. Go to **Step 3: Define Action**
10. Next to **Action**, click **Edit**
11. Add a new **Incident Attribute** row at the bottom with the following:
    1. Set **Event Attribute** as **Source TCP/UDP Port**
    2. Set **Subpattern** as **StatHighPort**
    3. Set **Filter Attribute** as **Source TCP/UDP Port**
12. Click **Save**

### To add **Source TCP/UDP Port** on previous existing rule exception:

1. Log in at \[FQDN_FortiSIEMServer\] with a 'Full Admin' account
2. Go to **Resources > Rules**
3. Select rule **_Sudden Increase in Firewall Denied Outbound Traffic To A Specific TCP/UDP port_**
4. Click **Edit** and select **Step 3: Define Action**
5. Next to **Exceptions**, click **Edit**
6. With one pre-existing Destination Port condition:
   1. Add **Paren** before it
   2. Set **Operator** as **\=**
   3. Set **Value** as **67**
   4. Set **Next** as **AND**
7. Add a new row directly under the altered **Destination Port** condition, with the following:
   1. Set **Attribute** as **Source TCP/UDP Port**
   2. Set **Operator** as **\=**
   3. Set **Value** as **68**
   4. Add **Paren** after it
8. For **Notes**, specify which condition set is for DHCPC traffic exception
9. Click **Save**

</div>