---
alias: slmgr, "product key"
---

# Activating Windows from CLI with slmgr

Software Licensing Management Tool (slmgr) is a VBS file in Windows against which you can run commands to perform Windows Activation and Key Management Service (KMS) related tasks.

Slmgr.vbs is used only for the Windows operating system. Ospp.vbs manages volume licensing for other Microsoft products

## Syntax

```batch
slmgr.vbs [<ComputerName> [<User> <Password>]] [<Options>]
```

## Command Options


### Display Licence information

```batch
slmgr.vbs /dli
```

For a more detailed output, use:

```batch
slmgr.vbs /dlv
```


### Display expiration date of current product

```batch
slmgr.vbs /xpr
```


### Add product key

```batch
slmgr.vbs /ipk <ProductKey>
```


### Set / Remove KMS server

```batch
slmgr.vbs /skms <Name[:Port]>
```

This option specifies the name and, optionally, the port of the KMS host computer to contact. Setting this value disables auto-detection of the KMS host. See example in [[sysprep-procedures|sysprep]] where this is used in a batch script example.

```batch
slmgr.vbs /ckms
```

This option removes the specified KMS host name, address, and port information from the registry and restores KMS auto-discovery behavior.

### Activate product

```batch
slmgr.vbs /ato
```

For retail editions and volume systems that have a KMS host key or a Multiple Activation Key (MAK) installed, **/ato** prompts Windows to try online activation.  

For systems that have a Generic Volume License Key (GVLK) installed, this prompts a KMS activation attempt.

### Check Volume Licence Details In Registry

This key holds information related to KMS settins: 
`HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\SoftwareProtectionPlatform`

To retrieve data with Powershell, run it as admin, then do:
```powershell
Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\SoftwareProtectionPlatform" | Select-Object KeyManagementServiceName, KeyManagementServicePort
```

Where:
- `KeyManagementServiceName`: This property contains the hostname or IP address of the KMS server.
- `KeyManagementServicePort`: This property contains the port number used by the KMS server (usually 1688).

## How KMS Activation Works

- **KMS Client Setup Key (GVLK):**
    - Windows volume license editions use a Generic Volume License Key (GVLK). This key tells the OS to look for a KMS server.
- **DNS SRV Record Query:**
    - If a specific KMS server isn't already defined in the client's registry, the client queries DNS for SRV records of `_vlmcs._tcp`.
    - This query aims to find available KMS hosts on the network.
- **KMS Host Connection:**
    - If the DNS query is successful, the client attempts to connect to a KMS host returned by the DNS server.
- **Activation:**
    - If the connection to the KMS host is successful, and the KMS host validates the client, the client is activated.
- **Registry Behavior:**
    - It's important to understand that while the client may use DNS to _discover_ the KMS server, it may or may not always write that server information to the registry in a persistent way.
    - The operating system does keep track of the KMS server that it has used, for the purpose of the regular 7 day re-activation attempts. So information is stored, but the way and how long it is stored, can vary.
    - The degree to which the information is persistently written to the registry can vary depending on windows version, and also on if KMS host caching is enabled.
    - The goal of KMS is for the clients to be able to dynamically find the KMS servers. So the windows operating systems does not always need to write that information into the registry in a perminant way.
- **Renewal:**
    - KMS activation is valid for 180 days. The client attempts to renew its activation every 7 days. This renewal process might again involve DNS queries if the previously used KMS host is unavailable.

## Issues & Troubleshooting

### OxC004F074 error due to wrong time on computer

This one will occur upon executing /ato command with a volume key previously loaded which will result with error:

*"Activating Windows ... Error: OxC004F074 The Software Licencing Service reported that the computer could not be activated. No Key Management Service could be contacted. Please see the Application event log for additional information."*

Checking the logs under Event Viewer\Applications, there will be an event 12288 but not the needed following 12289 event. Instead, there will be a 8198 event with a brief error description and a hexadecimal footprint (did not save it).

Solution is to disable the automatic time sync (in case it's not working) and configure the time manually or enable the automatic time sync so it can correctly pull the time data from Windows time servers.

## References

1. [Official doc about slmgr commands](https://learn.microsoft.com/en-us/windows-server/get-started/activation-slmgr-vbs-options)
2. [Official docs about troubleshooting kms](https://learn.microsoft.com/en-us/windows-server/get-started/activation-troubleshoot-kms-general)
3. [Registry settings for Volume Activation](https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/dn502532(v=ws.11))





