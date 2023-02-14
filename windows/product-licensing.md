---
alias: slmgr, "product key"
---

# Activating Windows from CLI with slmgr

Software Licensing Management Tool (slmgr) is a VBS file in Windows against which you can run commands to perform Windows Activation and Key Management Service (KMS) related tasks.

Slmgr. vbs is used only for the Windows operating system. Ospp. vbs manages volume licensing for other Microsoft products

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

## References

1. [Official doc about slmgr commands](https://learn.microsoft.com/en-us/windows-server/get-started/activation-slmgr-vbs-options)



