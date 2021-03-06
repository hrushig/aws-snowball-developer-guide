--------

This guide is for the Snowball Edge\. If you are looking for documentation for the Snowball, see the [AWS Snowball User Guide](https://docs.aws.amazon.com/snowball/latest/ug/whatissnowball.html)\.

--------

# Updating an AWS Snowball Edge<a name="updating-device"></a>

The software update feature for Snowball Edge allows you to update the software on Snowball Edge devices in your on\-premises environments\. These updates happen in the background, meaning that you can continue to use your devices as normal, as the latest software is downloaded securely from AWS to your device, and then updated\. To apply the updates, you need to restart the device\.

**Warning**  
We highly recommend that you suspend all activity on the device before you restart the device\. Restarting a device stops running instances, interrupts any writing to Amazon S3 buckets on the device, and stops any write operations from the file interface without clearing the cache\. All of these processes can result in lost data\.

## Prerequisites<a name="prereq-updating-device"></a>

Before you can update your device, the following prerequisites must be met:
+ You've created your job, have the device on\-premises, and you've unlocked it\. For more information see [Getting Started with an AWS Snowball Edge Device](getting-started.md)\.
+ Updating a Snowball Edge is done with the Snowball client, which must be downloaded and installed on a computer in your local environment, with a network connection to the device you want to update\. For more information, see [ Using the Snowball Client  Following, you can find information about how to get and use the Snowball client with your AWS Snowball Edge device\. The Snowball client is a standalone terminal application that you run on your local server to unlock the device and get credentials, logs, and status information\. You can also use the client for administrative tasks for a cluster\. While using the Snowball client, you can get additional support information by running the `snowballEdge help` command\. When you read and write data to the AWS Snowball Edge device, you use the Amazon S3 Adapter for Snowball or the file interface\.  In January 2018, there was a feature update for clusters, making them leaderless\. The cluster update is backward\-compatible with older clusters\. However, if you're looking for documentation for the original Snowball client for Snowball Edge, see [Using the Snowball Client](old-using-client.md) in the Appendices\.   Downloading and Installing the Snowball Client  You can download and install the Snowball client from the [AWS Snowball Edge Resources](http://aws.amazon.com/snowball-edge/resources/) page\. On that page, you can find the installation package for your operating system and follow the instructions to install the Snowball client\. Running the Snowball client from a terminal in your workstation might require using a specific path, depending on your operating system:   **Microsoft Windows** – When the client has been installed, you can run it from any directory without any additional preparation\.   **Linux** – The Snowball client must be run from the \~/snowball\-client\-linux\-*build\_number*/bin/ directory\. Note that the Snowball client is only supported on 64\-bit Linux distributions\.   **MacOS** – The `install.sh` script copies folders from the Snowball client \.tar file to the /usr/local/bin/snowball directory\. If you run this script, you can then run the Snowball client from any directory if /usr/local/bin is a path in your bash\_profile\. You can verify your path with the `echo $PATH` command\.     Commands for the Snowball Client  Following, you can find information on the Snowball client commands, including examples of use and sample outputs\.   Configuring a Profile for the Snowball Client  Every time you run a command for the Snowball client, you provide your manifest file, unlock code, and an IP address\. You can get the first two of these from the AWS Snowball Management Console or the job management API\. For more information on getting your manifest and unlock code, see [Get Your Credentials and Tools](get-credentials.md)\. You have the option of using the `snowballEdge configure` command to store the path to the manifest, the 29\-character unlock code, and the endpoint as a profile\. After configuration, you can use other Snowball client commands without having to manually type in these values for a particular job\. After you configure the Snowball client, the information is saved in a plaintext JSON format to `home directory/.aws/snowball/config/snowball-edge.config`\.  The endpoint is the IP address, with `https://` added to it\. You can locate the IP address for the AWS Snowball Edge device on the AWS Snowball Edge device's LCD display\. When the AWS Snowball Edge device is connected to your network for the first time, it automatically gets a DHCP IP address, if a DHCP server is available\. If you want to use a different IP address, you can change it from the LCD display\. For more information, see [Using an AWS Snowball Edge](using-device.md)\.  Anyone who can access the configuration file can access the data on your Snowball Edge devices or clusters\. Managing local access control for this file is one of your administrative responsibilities\.  **Usage** You can use this command in two ways: inline, or when prompted\. This usage example shows the prompted method\. 

```
snowballEdge configure
``` 

**Example Output**  

```
Configuration will stored at home directory\.aws\snowball\config\snowball-edge.config
Snowball Edge Manifest Path: Path/to/manifest/file
Unlock Code: 29 character unlock code
Default Endpoint: https://192.0.2.0
``` You can have multiple profiles if you have multiple jobs at once, or if you want the option of managing a cluster from different endpoints\. For more info on multiple AWS CLI profiles, see [Named Profiles](https://docs.aws.amazon.com/cli/latest/userguide/cli-multiple-profiles.html) in the AWS Command Line Interface User Guide\.   Getting Your QR Code for NFC Validation  You can use this command to generate a device\-specific QR code for use with the AWS Snowball Edge Verification App\. You can download this app from the App Store or Google Play store\. For more information on NFC validation, see [Validating NFC Tags](nfc-validation.md)\. **Usage** 

```
snowballEdge get-app-qr-code --output-file ~/downloads/snowball-qr-code.png
``` 

**Example Output**  

```
QR code is saved to ~/downloads/snowball-qr-code.png
```   Unlocking Snowball Edge Devices  To unlock a standalone AWS Snowball Edge device, run the `snowballEdge unlock-device` command\. To unlock a cluster, use the `snowballEdge unlock-cluster` command\. These commands authenticate your access to the AWS Snowball Edge device\.   To unlock the devices associated with your job, the devices must be on site, plugged into power and network, and turned on\. In addition, the LCD display on the AWS Snowball Edge device's front must indicate that the device is ready for use\.  **Usage \(configured Snowball client\)** 

```
snowballEdge unlock-device
``` 

**Example Single Device Unlock Input**  

```
snowballEdge unlock-device
``` 

**Example Single Device Unlock Output**  

```
Your Snowball Edge device is unlocking. You may determine the unlock state of your device using the describe-device command. Your Snowball Edge device will be available for use when it is in the UNLOCKED state.
``` **Cluster Usage** When you unlock a cluster, you provide the endpoint for one of your nodes, and all the IP addresses for the other devices in your cluster\. 

```
snowballEdge unlock-cluster --endpoint https://192.0.2.0 --manifest-file Path/to/manifest/file --unlock-code 01234-abcde-ABCDE-01234 --device-ip-addresses 192.0.2.0 192.0.2.1 192.0.2.2 192.0.2.3 192.0.2.4
``` 

**Example Cluster Unlock Output**  

```
Your Snowball Edge Cluster is unlocking. You may determine the unlock state of your cluster using the describe-device command. Your Snowball Edge Cluster will be available for use when your Snowball Edge devices are in the UNLOCKED state.
```   Updating a Snowball Edge  The following commands can be used to download and install updates for your Snowball Edge device\. For procedures that use these commands, see [Updating an AWS Snowball Edge](updating-device.md)\. `snowballEdge check-for-updates`: Returns version information about the Snowball Edge software available in the cloud, and the current version installed on the device\.  **Usage \(configured Snowball client\)** 

```
snowballEdge check-for-updates
``` 

**Example Output**  

```
Latest version: 102
Installed version: 101
``` `snowballEdge describe-device-software`: Returns the current software version for the device\. Additionally, if the update is being downloaded, the download state is also displayed\. If a software update is in progress, the version manifest of update, and state of installation is also displayed\. Following is a list of possible outputs:   `NA` – No software updates are currently in progress\.   `Downloading` – New software is being downloaded\.   `Installing` – New software is being installed\.   `Requires Reboot` – new software has been installed, and the device needs to be restarted\.  We highly recommend that you suspend all activity on the device before you restart the device\. Restarting a device stops running instances, interrupts any writing to Amazon S3 buckets on the device, and stops any write operations from the file interface without clearing the cache\. All of these processes can result in lost data\.    **Usage \(configured Snowball client\)** 

```
snowballEdge describe-device-software
``` 

**Example Output**  

```
Installed version: 101
Installing version: 102
Install State: Downloading
``` `snowballEdge download-updates`: Starts downloading the latest software updates for your Snowball Edge\. **Usage \(configured Snowball client\)** 

```
snowballEdge download-updates
``` 

**Example Output**  

```
Download started. Run describe-device-software API for additional information.
``` `snowballEdge install-updates`: Starts installing the latest software updates for your Snowball Edge that were already downloaded\. **Usage \(configured Snowball client\)** 

```
snowballEdge install-updates
``` 

**Example Output**  

```
Installation started.
``` `snowballEdge reboot-device`: Reboots the device\.  We highly recommend that you suspend all activity on the device before you restart the device\. Restarting a device stops running instances, interrupts any writing to Amazon S3 buckets on the device, and stops any write operations from the file interface without clearing the cache\. All of these processes can result in lost data\.  **Usage \(configured Snowball client\)** 

```
snowballEdge reboot-device
``` 

**Example Output**  

```
Rebooting device now.
``` `snowballEdge configure-auto-update-strategies`: Configures an automatic update strategy\. **Usage \(configured Snowball client\)** 

```
snowballEdge configure-auto-update-strategy --auto-check autoCheck [--auto-check-frequency
autoCheckFreq] --auto-download autoDownload
[--auto-download-frequency autoDownloadFreq]
--auto-install autoInstall
[--auto-install-frequency autoInstallFreq]
--auto-reboot autoReboot [--endpoint
endpoint]
``` 

**Example Output**  

```
Successfully configured auto update strategy. Run describe-auto-update-strategies for additional information.
``` `snowballEdge describe-auto-update-strategies`: Returns any currently configured automatic update strategy\. **Usage \(configured Snowball client\)** 

```
snowballEdge describe-auto-update-strategies
``` 

**Example Output**  

```
auto-update-strategy {[
auto-check:true,
auto-check-frequency: "0 0 * * FRI", // CRON Expression String, Every Friday at midnight
auto-download:true,
auto-download-frequency: "0 0 * * SAT", // CRON Expression String, Every Saturday at
midnight
auto-install:true,
auto-install-frequency: "0 13 * * Sun", // CRON Expression String, Every Saturday at
midnight
auto-reboot: false;
]}
```   Getting Credentials  Using the `snowballEdge list-access-keys` and `snowballEdge get-secret-access-key` commands, you can get your local credentials\. You use these to authenticate your requests when using the AWS CLI or with an AWS SDK\. These credentials are only associated with an individual job for Snowball Edge, and you can use them only on the device or cluster of devices\. The device or devices don't have any AWS Identity and Access Management \(IAM\) permissions in the AWS Cloud\.  If you're using the AWS CLI with the Snowball Edge, you must use these credentials when you configure the CLI\. For information on configuring credentials for the CLI, see [Quick Configuration](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html#cli-quick-configuration) in the *AWS Command Line Interface User Guide\. *  **Usage \(configured Snowball client\)** 

```
snowballEdge list-access-keys
``` 

**Example Output**  

```
{
  "AccessKeyIds" : [ "AKIAIOSFODNN7EXAMPLE" ]
}
``` **Usage \(configured Snowball client\)** 

```
snowballEdge get-secret-access-key --access-key-id Access Key
``` 

**Example Output**  

```
[snowballEdge]
aws_access_key_id = AKIAIOSFODNN7EXAMPLE
aws_secret_access_key = wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
```   Starting a Service on your Snowball Edge  Snowball Edge devices support multiple services, in addition to Amazon S3\. These include compute instances, the file interface, and AWS IoT Greengrass\. Amazon S3 and Amazon EC2 are always on by default, and can't be stopped or restarted with the Snowball client\. However, the file interface and AWS IoT Greengrass can be started with the `snowballEdge start-service` command\. To get the service ID for each service, you can use the `snowballEdge list-services` command\. Before you run this command, create a single virtual network interface to bind to the service that you're starting\. For more information, see [Creating a Virtual Network Interface](using-ec2-edge-client.md#ec2-edge-create-vnic)\. **Usage \(configured Snowball client\)** 

```
snowballEdge start-service --service-id service_id --virtual-network-interface-arns virtual-network-interface-arn
``` 

**Example Output**  

```
[snowballEdge]
aws_access_key_id = AKIAIOSFODNN7EXAMPLE
aws_secret_access_key = wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
```   Stopping a Service on your Snowball Edge  To stop a service running on your Snowball Edge, you can use the `snowballEdge stop-service` command\. The Amazon S3 and Amazon EC2 services cannot be stopped\.  Data loss can occur if the file interface is stopped before remaining buffered data is written to the device\. For more information on using the file interface, see [Using the File Interface for the AWS Snowball Edge](using-fileinterface.md)\.  **Usage \(configured Snowball client\)** 

```
snowballEdge stop-service --service-id service_id
``` 

**Example Output**  

```
Stopping the AWS service on your Snowball Edge. You can determine the status of the AWS service using the describe-service command.
```   Getting Your Certificate for Transferring Data  To transfer data to a Snowball Edge, use the Amazon S3 Adapter for Snowball\. To use the adapter over the HTTPS protocol, you must provide a certificate\. The certificates are generated by each Snowball Edge device\. If you unlock your Snowball Edge device with a different IP address, a new certificate is generated and the old certificate is no longer valid to use with the endpoint\. You can get the new, updated certificate from the Snowball Edge again using the `get-certificate` command\. You can list these certificates and download them from your Snowball Edge device with the following commands:   `list-certificates` – Lists the Amazon Resource Names \(ARNs\) for the certificates available for use\. **Usage \(configured Snowball client\)** 

  ```
  snowballEdge list-certificates
  ``` 

**Example Output**  

  ```
  {
    "Certificates" : [ {
      "CertificateArn" : "arn:aws:snowball-device:::certificate/78EXAMPLE516EXAMPLEf538EXAMPLEa7",
      "SubjectAlternativeNames" : [ "192.0.2.0" ]
    } ]
  }
  ```   `get-certificate` – Gets a specific certificate, based on the ARN provided\. **Usage \(configured Snowball client\)** 

  ```
  snowballEdge get-certificate --certificate-arn arn:aws:snowball-device:::certificate/78EXAMPLE516EXAMPLEf538EXAMPLEa7
  ``` 

**Example Output**  

  ```
  -----BEGIN CERTIFICATE-----
  Certificate
  -----END CERTIFICATE-----
  ```
For information on configuring your certificate, see [Specifying the Adapter as the AWS CLI Endpoint](using-adapter.md#using-adapter-cli-endpoint)\.     AWS Snowball Edge Logs  When you transfer data between your on\-premises data center and a Snowball Edge, logs are automatically generated\. If you encounter unexpected errors during data transfer to the device, you can use the following commands to save a copy of the logs to your local server\. There are three commands related to logs:   `list-logs` – Returns a list of logs in JSON format\. This list reports the size of the logs in bytes, the ARN for the logs, the service ID for the logs, and the type of logs\.  **Usage \(configured Snowball client\)** 

  ```
  snowballEdge list-logs
  ``` 

**Example Output**  

  ```
  {
    "Logs" : [ {
      "LogArn" : "arn:aws:snowball-device:::log/s3-storage-JIEXAMPLE2f-1234-4953-a7c4-dfEXAMPLE709",
      "LogType" : "SUPPORT",
      "ServiceId" : "s3",
      "EstimatedSizeBytes" : 53132614
    }, {
      "LogArn" : "arn:aws:snowball-device:::log/fileinterface-JIDEXAMPLEf-1234-4953-a7c4-dfEXAMPLE709",
      "LogType" : "CUSTOMER",
      "ServiceId" : "fileinterface",
      "EstimatedSizeBytes" : 4446
    }]
  }
  ```   `get-log` – Downloads a copy of a specific log from the Snowball Edge to your server at a specified path\. `CUSTOMER` logs are saved in the `.zip` format, and you can extract this type of log to view its contents\. `SUPPORT` logs are encrypted and can only be read by AWS Support engineers\. You have the option of specifying a name and a path for the log\.  **Usage \(configured Snowball client\)** 

  ```
  snowballEdge get-log --log-arn arn:aws:snowball-device:::log/fileinterface-JIDEXAMPLEf-1234-4953-a7c4-dfEXAMPLE709
  ``` 

**Example Output**  

  ```
  Logs are being saved to download/path/snowball-edge-logs-1515EXAMPLE88.bin
  ```   `get-support-logs` – Downloads a copy of all the `SUPPORT` type of logs from the Snowball Edge to your service at a specified path\. **Usage \(configured Snowball client\)** 

  ```
  snowballEdge get-support-logs
  ``` 

**Example Output**  

  ```
  Logs are being saved to download/path/snowball-edge-logs-1515716135711.bin
  ```    `CUSTOMER` type might contain sensitive information about your own data\. To protect this potentially sensitive information, we strongly suggest that you delete these logs once you're done with them\.    Getting Device Status  You can determine the status and general health of your Snowball Edge devices with the following Snowball client commands:   `describe-device` **Usage \(configured Snowball client\)** 

  ```
  snowballEdge describe-device
  ``` 

**Example Output**  

  ```
  {
    "DeviceId" : "JID-EXAMPLE12345-123-456-7-890",
    "UnlockStatus" : {
      "State" : "UNLOCKED"
    },
    "ActiveNetworkInterface" : {
      "IpAddress" : "192.0.2.0"
    },
    "PhysicalNetworkInterfaces" : [ {
      "PhysicalNetworkInterfaceId" : "s.ni-EXAMPLEd9ecbf03e3",
      "PhysicalConnectorType" : "RJ45",
      "IpAddressAssignment" : "STATIC",
      "IpAddress" : "0.0.0.0",
      "Netmask" : "0.0.0.0",
      "DefaultGateway" : "192.0.2.1",
      "MacAddress" : "EX:AM:PL:E0:12:34"
    }, {
      "PhysicalNetworkInterfaceId" : "s.ni-EXAMPLE4c3840068f",
      "PhysicalConnectorType" : "QSFP",
      "IpAddressAssignment" : "STATIC",
      "IpAddress" : "0.0.0.0",
      "Netmask" : "0.0.0.0",
      "DefaultGateway" : "192.0.2.2",
      "MacAddress" : "EX:AM:PL:E0:56:78"
    }, {
      "PhysicalNetworkInterfaceId" : "s.ni-EXAMPLE0a3a6499fd",
      "PhysicalConnectorType" : "SFP_PLUS",
      "IpAddressAssignment" : "DHCP",
      "IpAddress" : "192.168.1.231",
      "Netmask" : "255.255.255.0",
      "DefaultGateway" : "192.0.2.3",
      "MacAddress" : "EX:AM:PL:E0:90:12"
    } ]
  }
  }
  ```   `describe-cluster` **Usage \(configured Snowball client\)** 

  ```
  snowballEdge describe-cluster
  ``` 

**Example Output**  

  ```
  {
    "ClusterId" : "CIDEXAMPLE7-5402-4c19-9feb-7c9EXAMPLEd5",
    "Devices" : [ {
      "DeviceId" : "JIDEXAMPLE2-bc53-4618-a538-917EXAMPLE94",
      "UnlockStatus" : {
        "State" : "UNLOCKED"
      },
      "ActiveNetworkInterface" : {
        "IpAddress" : "192.0.2.0"
      },
      "ClusterAssociation" : {
        "State" : "ASSOCIATED",
        "ClusterId" : "CIDEXAMPLE7-5402-4c19-9feb-7c9EXAMPLEd5"
      },
      "NetworkReachability" : {
        "State" : "REACHABLE"
      }
    }, {
      "DeviceId" : "JIDEXAMPLE2-bc53-4618-a538-917EXAMPLE94",
      "UnlockStatus" : {
        "State" : "UNLOCKED"
      },
      "ActiveNetworkInterface" : {
        "IpAddress" : "192.0.2.1"
      },
      "ClusterAssociation" : {
        "State" : "ASSOCIATED",
        "ClusterId" : "CIDEXAMPLE7-5402-4c19-9feb-7c9EXAMPLEd5"
      },
      "NetworkReachability" : {
        "State" : "REACHABLE"
      }
    }, {
      "DeviceId" : "JIDEXAMPLE2-bc53-4618-a538-917EXAMPLE94",
      "UnlockStatus" : {
        "State" : "UNLOCKED"
      },
      "ActiveNetworkInterface" : {
        "IpAddress" : "192.0.2.2"
      },
      "ClusterAssociation" : {
        "State" : "ASSOCIATED",
        "ClusterId" : "CIDEXAMPLE7-5402-4c19-9feb-7c9EXAMPLEd5"
      },
      "NetworkReachability" : {
        "State" : "REACHABLE"
      }
    }, {
      "DeviceId" : "JIDEXAMPLE2-bc53-4618-a538-917EXAMPLE94",
      "UnlockStatus" : {
        "State" : "UNLOCKED"
      },
      "ActiveNetworkInterface" : {
        "IpAddress" : "192.0.2.3"
      },
      "ClusterAssociation" : {
        "State" : "ASSOCIATED",
        "ClusterId" : "CIDEXAMPLE7-5402-4c19-9feb-7c9EXAMPLEd5"
      },
      "NetworkReachability" : {
        "State" : "REACHABLE"
      }
    }, {
      "DeviceId" : "JIDEXAMPLE2-bc53-4618-a538-917EXAMPLE94",
      "UnlockStatus" : {
        "State" : "UNLOCKED"
      },
      "ActiveNetworkInterface" : {
        "IpAddress" : "192.0.2.4"
      },
      "ClusterAssociation" : {
        "State" : "ASSOCIATED",
        "ClusterId" : "CIDEXAMPLE7-5402-4c19-9feb-7c9EXAMPLEd5"
      },
      "NetworkReachability" : {
        "State" : "REACHABLE"
      }
    } ]
  }
  ```     Getting Service Status  You can determine the status and general health of the services running on Snowball Edge devices with the `describe-service` command\. You can first run the `list-services` command to see what services are running\.   `list-services` **Usage \(configured Snowball client\)** 

  ```
  snowballEdge list-services
  ``` 

**Example Output**  

  ```
  {
    "ServiceIds" : [ “greengrass”, "fileinterface", "s3", "ec2" ]
  }
  ```   `describe-service` This command returns a status value for a service\. It also includes state information that might be helpful in resolving issues you encounter with the service\. Those states are as follows\.   `ACTIVE` – The service is running and available for use\.   `ACTIVATING` – The service is starting up, but it is not yet available for use\.   `DEACTIVATING` – The service is in the process of shutting down\.   `INACTIVE` – The service is not running and is not available for use\.   **Usage \(configured Snowball client\)** 

  ```
  snowballEdge describe-service --service-id service-id
  ``` 

**Example Output**  

  ```
  {
  "ServiceId" : "s3",
    "Status" : {
      "State" : "ACTIVE"
    },
  "Storage" : {
  "TotalSpaceBytes" : 99608745492480,
  "FreeSpaceBytes" : 99608744468480
  },
  "Endpoints" : [ {
  "Protocol" : "http",
  "Port" : 8080,
  "Host" : "192.0.2.0"
  }, {
  "Protocol" : "https",
  "Port" : 8443,
  "Host" : "192.0.2.0",
  "CertificateAssociation" : {
  "CertificateArn" : "arn:aws:snowball-device:::certificate/6d955EXAMPLEdb71798146EXAMPLE3f0"
  }
  } ]
  }
  ```     Removing a Node from a Cluster  The `disassociate-device` command removes a node from a Snowball Edge cluster\. If you want to replace an unhealthy node, use this command\. For more information on clusters, see [Using an AWS Snowball Edge Cluster](UsingCluster.md)\.  Use the `disassociate-device` command only when you are removing an unhealthy node\. This command fails and returns an error if you try to remove a healthy node\.  Don't use this command to remove a node that was accidentally powered off or disconnected from the network and is therefore temporarily unavailable to the rest of the cluster\. Nodes removed with this command can't be added to any cluster, and must be returned to AWS\. If a node was accidentally powered off or disconnected from the network, simply plug the node back into power, and the network, and use the `associate-device` command\. You can't use the `disassociate-device` command to disassociate a node if it's powered on and healthy\. **Usage \(configured Snowball client\)** 

```
snowballEdge disassociate-device --device-id Job ID for the Device
``` 

**Example Output**  

```
Disassociating your Snowball Edge device from the cluster. Your Snowball Edge device will be disassociated from the cluster when it is in the "DISASSOCIATED" state. You can use the describe-cluster command to determine the state of your cluster.
```   Adding a Node to a Cluster  The `associate-device` command adds a node to a cluster of Snowball Edge devices\. If you power off a node, then it reverts from being unlocked to being locked\. To unlock that node, you can use this command\. You can use this command to replace an unavailable node with a new node that you ordered as a replacement\. For more information on clusters, see [Using an AWS Snowball Edge Cluster](UsingCluster.md)\. **Usage \(configured Snowball client\)** 

```
snowballEdge associate-device --device-ip-address IP Address
``` 

**Example Output**  

```
Associating your Snowball Edge device with the cluster. Your Snowball Edge device will be associated with the cluster when it is in the ASSOCIATED state. You can use the describe-cluster command to determine the state of your cluster.
```   ](using-client.md)\.
+ \(Optional\) We recommend that you configure a profile for the Snowball client\. For more information, see [Configuring a Profile for the Snowball Client](using-client-commands.md#client-configuration)\.

Now that you've completed those tasks, you can now download and install updates for Snowball Edge devices\.

## Downloading Updates<a name="download-updates"></a>

There are two primary ways that you can download an update for a Snowball Edge device: 
+ You can trigger manual updates at any time using specific Snowball client commands\.
+ You can programmatically determine a time to automatically update the device\.

The following procedure outlines the process of manually downloading updates\. For information on automatically updating your Snowball Edge device, see `snowballEdge configure-auto-update-strategy` in [Updating a Snowball Edge](using-client-commands.md#update-client-commands)\.

**To check for and download Snowball Edge software updates**

1. Open a terminal window, and ensure that the Snowball Edge device is unlocked with the `snowballEdge describe-device` command\. If the device is locked, use the `snowballEdge unlock-device` command to unlock it\.

1. When the device is unlocked, run the `snowballEdge check-for-updates` command\. This command returns the latest available version of the Snowball Edge software, and also the current version installed on the device\.

1. If your device software is out of date, run the `snowballEdge download-updates` command\.

1. You can check the status of this download with the `snowballEdge describe-device-software` command\. While an update is downloading, the status displayed with this command\. 

**Example output**  
 `Install State: Downloading`

## Installing Updates<a name="install-updates"></a>

After you've downloaded updates, you need to install them and restart your device for the updates to take effect\. The following procedure guides you through how to manually install updates\.

**To install Snowball Edge software updates that were already downloaded**

1. Open a terminal window, and ensure that the Snowball Edge device is unlocked with the `snowballEdge describe-device` command\. If the device is locked, use the `snowballEdge unlock-device` command to unlock it\.

1. Run the `snowballEdge install-updates` command\.

1. You can check the status of this installation with the `snowballEdge describe-device-software` command\. While an update is installing, the status is displayed with this command\.

**Example output**  
`Install State: Installing //Possible values[NA, Installing, Requires Reboot]`

   You’ve successfully installed a software update for your Snowball Edge device\. Installing an update does not automatically apply the update to the device\. To finish installing the update, the device must be restarted\.

   We highly recommend that you suspend all activity on the device before you restart the device\. Restarting a device stops running instances, interrupts any writing to Amazon S3 buckets on the device, and stops any write operations from the file interface without clearing the cache\.
**Warning**  
Restarting your Snowball Edge device without stopping all activity on the device can result in lost data\.

1. Run the `snowballEdge list-services `command to list the currently running services on the device\.

1. Run the `snowballEdge describe-service` command for each of the running services, to see their status\.

1. Use this information to stop those services \(setting the services to the `INACTIVE` state\)\.

1. When all the services on the device have stopped, run the `snowballEdge reboot-device` command\. This command immediately power\-cycles the device to complete installation of the downloaded software updates\.

1. After the device powers on open a terminal window and use the `snowballEdge unlock-device` command to unlock the device\.

1. Run the `snowballEdge check-for-updates` command\. This command returns the latest available version of the Snowball Edge software, and also the current version installed on the device

You have now successfully updated your device and confirmed that your device is up\-to\-date with the latest Snowball Edge software\.