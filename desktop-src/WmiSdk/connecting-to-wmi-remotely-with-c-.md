---
Description: As with other languages such as PowerShell, VBScript, or C++, you can use C# to remotely monitor the hardware and software on remote computers.
audience: developer
author: REDMOND\\markl
manager: REDMOND\\markl
ms.assetid: 9E03A8D0-01AB-4B7E-99B6-FEEF9C1CAE17
ms.prod: windows-server-dev
ms.technology: windows-management-instrumentation
ms.tgt_platform: multiple
title: Connecting to WMI Remotely with C#
ms.author: windowssdkdev
ms.topic: article
ms.date: 05/31/2018
---

# Connecting to WMI Remotely with C#

As with other languages such as PowerShell, VBScript, or C++, you can use C# to remotely monitor the hardware and software on remote computers. Remote connections for managed code are accomplished through the [Microsoft.Management.Infrastructure](https://msdn.microsoft.com/library/microsoft.management.infrastructure.aspx) namespace. (Previous versions of WMI used the [System.Management](https://msdn.microsoft.com/library/system.management.aspx) namespace, which is included here for completeness.)

> [!Note]  
> **System.Management** was the original .NET namespace used to access WMI; however, the APIs in this namespace generally are slower and do not scale as well relative to their more modern **Microsoft.Management.Infrastructure** counterparts.

 

Connecting remotely using classes in the [Microsoft.Management.Infrastructure](https://msdn.microsoft.com/library/microsoft.management.infrastructure.aspx) namespace uses DCOM as the underlying remote mechanism. WMI remote connections must comply with DCOM security requirements for impersonation and authentication. By default, a scope is bound to the local computer and the "Root\\CIMv2" system namespace. However, you can change both the computer, domain, and WMI namespace that you access. You can also set authority, impersonation, credentials, and other connection options.

**To connect to WMI remotely with C# (Microsoft.Management.Infrastructure)**

1.  Create a session on the remote machine with a call to [CimSession.Create](https://msdn.microsoft.com/library/hh832539.aspx).

    If you are connecting to a remote computer using the same credentials (domain and user name) you are logged on with, then you can specify the name of the computer in the [Create](https://msdn.microsoft.com/library/hh832539.aspx) call. Once you have the returned [CimSession](https://msdn.microsoft.com/library/microsoft.management.infrastructure.cimsession.aspx) object, you can then make your WMI query.

    ```CSharp
    using Microsoft.Management.Infrastructure;
    ...
    string Namespace = @"root\cimv2";
    string OSQuery = "SELECT * FROM Win32_OperatingSystem";
    CimSession mySession = CimSession.Create("Computer_B");
    IEnumerable<CimInstance> queryInstance = mySession.QueryInstances(Namespace, "WQL", OSQuery);
    ```

    

    For more information on making WMI queries with the **Microsoft.Management.Infrastructure** API in C#, see [Retrieving WMI Class or Instance Data](retrieving-class-or-instance-data.md).

2.  If you wish to set different options for your connection, such as different credentials, locale or Impersonation levels, you need to use a [CimSessionOptions](https://msdn.microsoft.com/library/microsoft.management.infrastructure.options.cimsessionoptions.aspx) object in your call to [CimSession.Create](https://msdn.microsoft.com/library/microsoft.management.infrastructure.cimsession.create.aspx).

    [CimSessionOptions](https://msdn.microsoft.com/library/microsoft.management.infrastructure.options.cimsessionoptions.aspx) is a base class for [WSManSessionOptions](https://msdn.microsoft.com/library/microsoft.management.infrastructure.options.wsmansessionoptions.aspx) and [DComSessionOptions](https://msdn.microsoft.com/library/microsoft.management.infrastructure.options.dcomsessionoptions.aspx). You can use either to set the options on your WS-Man and DCOM sessions, respectively. The following code sample describes using a [DComSessionOptions](https://msdn.microsoft.com/library/microsoft.management.infrastructure.options.dcomsessionoptions.aspx) object to set the Impersonation level to Impersonate.

    ```CSharp
    string computer = "Computer_B"
    DComSessionOptions DComOptions = new DComSessionOptions();
    DComOptions.Impersonation = ImpersonationType.Impersonate;

    CimSession Session = CimSession.Create(computer, DComOptions);
    ```

    

3.  If you wish to set the credentials for your connection, you will need to create and add a [CimCredentials](https://msdn.microsoft.com/library/microsoft.management.infrastructure.options.cimcredential.aspx) object to your [CimSessionOptions](https://msdn.microsoft.com/library/microsoft.management.infrastructure.options.cimsessionoptions.aspx).

    The following code sample describes creating a [WSManSessionOptions](https://msdn.microsoft.com/library/microsoft.management.infrastructure.options.wsmansessionoptions.aspx) class, filling it with the proper [CimSessionOptions](https://msdn.microsoft.com/library/microsoft.management.infrastructure.options.cimsessionoptions.aspx), and using it in a [CimSession.Create](https://msdn.microsoft.com/library/microsoft.management.infrastructure.cimsession.create.aspx) call.

    ```CSharp
    string computer = “Computer_B”;
    string domain = “Domain1″;
    string username = “User1″;

    string plaintextpassword; 

    //Retrieve password from the user. 
    //For the complete code, see the sample at the bottom of this topic.

    CimCredential Credentials = new CimCredential(PasswordAuthenticationMechanism.Default, domain, username, securepassword); 

    WSManSessionOptions SessionOptions = new WSManSessionOptions();
    SessionOptions.AddDestinationCredentials(Credentials); 

    CimSession Session = CimSession.Create(computer, SessionOptions);
    ```

    

    It is generally recommended that you do not hardcode a password into your applications; as the above code sample indicates, whenever possible try to query your user for the password, and store it securely.

WMI is intended to monitor the hardware and software on remote computers. Remote connections for WMI v1 is accomplished through the [ManagementScope](https://msdn.microsoft.com/library/system.management.managementscope.aspx) object.

**To connect to WMI remotely with C# (System.Management)**

1.  Create a [ManagementScope](https://msdn.microsoft.com/library/system.management.managementscope.aspx) object, using the name of the computer and the WMI path, and connect to your target with a call to ManagementScope.Connect().

    If you are connecting to a remote computer using the same credentials (domain and user name) you are logged on with, then you only have to specify the WMI path. Once you have connected, you can make your WMI query.

    ```CSharp
    using System.Management;
    ...
    ManagementScope scope = new ManagementScope("\\\\Computer_B\\root\\cimv2");
    scope.Connect();
    ObjectQuery query = new ObjectQuery("SELECT * FROM Win32_OperatingSystem");
    ManagementObjectSearcher searcher = new ManagementObjectSearcher(scope, query);
    ```

    

    For more information on making WMI queries with the [System.Management](https://msdn.microsoft.com/library/system.management.aspx) API in C#, see [Retrieving WMI Class or Instance Data](retrieving-class-or-instance-data.md).

2.  If you connect to a remote computer in a different domain or using a different user name and password, then you must use a [ConnectionOptions](https://msdn.microsoft.com/library/system.management.connectionoptions.aspx) object in the call to the [ManagementScope](https://msdn.microsoft.com/library/system.management.managementscope.aspx).

    The [ConnectionOptions](https://msdn.microsoft.com/library/system.management.connectionoptions.aspx) contains properties for describing the Authentication, Impersonation, username, password, and other connection options. The following code sample describes using a [ConnectionOptions](https://msdn.microsoft.com/library/system.management.connectionoptions.aspx) to set the Impersonation Level to Impersonate.

    ```CSharp
    ConnectionOptions options = new ConnectionOptions();
    options.Impersonation = System.Management.ImpersonationLevel.Impersonate;

    ManagementScope scope = new ManagementScope("\\\\FullComputerName\\root\\cimv2", options);
    scope.Connect();

    ObjectQuery query = new ObjectQuery("SELECT * FROM Win32_OperatingSystem");
    ManagementObjectSearcher searcher = new ManagementObjectSearcher(scope,query);
    ```

    

    Generally speaking, it is recommended that you set your Impersonation level to Impersonate unless explicitly needed otherwise. Further, try to avoid writing your name and password into C# code. (If possible, see if you can query the user to dynamically supply them at runtime.)

    For more examples of setting different properties on a remote WMI connection, see the Examples section of the [ConnectionOptions](https://msdn.microsoft.com/library/system.management.connectionoptions.aspx) reference page.

## Microsoft.Management.Infrastructure Example

The following C# code example, based on the following [blog post on TechNet](https://blogs.technet.microsoft.com/josebda/2014/08/11/sample-c-code-for-using-the-latest-wmi-classes-to-manage-windows-storage/), describes how to use [CimCredentials](https://msdn.microsoft.com/library/microsoft.management.infrastructure.options.cimcredential.aspx) and [WSManSessionOptions](https://msdn.microsoft.com/library/microsoft.management.infrastructure.options.wsmansessionoptions.aspx) to set credentials on a remote connection.


```CSharp
using System;
using System.Text;
using System.Threading;
using Microsoft.Management.Infrastructure;
using Microsoft.Management.Infrastructure.Options;
using System.Security; 

namespace SMAPIQuery
{
    class Program
    {
        static void Main(string[] args)
        { 

            string computer = "Computer_B";
            string domain = "DOMAIN";
            string username = "AdminUserName";


            string plaintextpassword; 

            Console.WriteLine("Enter password:");
            plaintextpassword = Console.ReadLine(); 

            SecureString securepassword = new SecureString();
            foreach (char c in plaintextpassword)
            {
                securepassword.AppendChar(c);
            } 

            // create Credentials
            CimCredential Credentials = new CimCredential(PasswordAuthenticationMechanism.Default, 
                                                          domain, 
                                                          username, 
                                                          securepassword); 

            // create SessionOptions using Credentials
            WSManSessionOptions SessionOptions = new WSManSessionOptions();
            SessionOptions.AddDestinationCredentials(Credentials); 

            // create Session using computer, SessionOptions
            CimSession Session = CimSession.Create(computer, SessionOptions); 

            var allVolumes = Session.QueryInstances(@"root\cimv2", "WQL", "SELECT * FROM Win32_Volume");
            var allPDisks = Session.QueryInstances(@"root\cimv2", "WQL", "SELECT * FROM Win32_DiskDrive"); 

            // Loop through all volumes
            foreach (CimInstance oneVolume in allVolumes)
            {
                // Show volume information

                if (oneVolume.CimInstanceProperties["DriveLetter"].ToString()[0] > ' '  )
                {
                    Console.WriteLine("Volume ‘{0}’ has {1} bytes total, {2} bytes available", 
                                      oneVolume.CimInstanceProperties["DriveLetter"], 
                                      oneVolume.CimInstanceProperties["Size"], 
                                      oneVolume.CimInstanceProperties["SizeRemaining"]);
                }

            } 

            // Loop through all physical disks
            foreach (CimInstance onePDisk in allPDisks)
            {
                // Show physical disk information
                Console.WriteLine("Disk {0} is model {1}, serial number {2}", 
                                  onePDisk.CimInstanceProperties["DeviceId"], 
                                  onePDisk.CimInstanceProperties["Model"].ToString().TrimEnd(), 
                                  onePDisk.CimInstanceProperties["SerialNumber"]);
            } 

            Console.ReadLine();
         }
     }
 }
```



## System.Management Example

The following C# code sample describes a general remote connection, using the System.Management objects.


```CSharp
using System;
using System.Management;
public class RemoteConnect 
{
    public static void Main() 
    {
        ConnectionOptions options = new ConnectionOptions();
        options.Impersonation = System.Management.ImpersonationLevel.Impersonate;

        
        ManagementScope scope = new ManagementScope("\\\\FullComputerName\\root\\cimv2", options);
        scope.Connect();

        //Query system for Operating System information
        ObjectQuery query = new ObjectQuery("SELECT * FROM Win32_OperatingSystem");
        ManagementObjectSearcher searcher = new ManagementObjectSearcher(scope,query);

        ManagementObjectCollection queryCollection = searcher.Get();
        foreach ( ManagementObject m in queryCollection)
        {
            // Display the remote computer information
            Console.WriteLine("Computer Name     : {0}", m["csname"]);
            Console.WriteLine("Windows Directory : {0}", m["WindowsDirectory"]);
            Console.WriteLine("Operating System  : {0}", m["Caption"]);
            Console.WriteLine("Version           : {0}", m["Version"]);
            Console.WriteLine("Manufacturer      : {0}", m["Manufacturer"]);
        }
    }
}
```



## Related topics

<dl> <dt>

[Connecting to WMI on a Remote Computer](connecting-to-wmi-on-a-remote-computer.md)
</dt> </dl>

 

 


