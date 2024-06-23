# Protocol Specification: CryptoARC

## 1. Introduction

CryptoARC (Automated Registration and Configuration) is a protocol designed to streamline the discovery, registration, and initial configuration of network devices, tailored initially for cryptocurrency mining devices. By leveraging DHCP options and RESTful communication, CryptoARC facilitates secure and automated integration of devices into managed networks.
## 2. Protocol Overview

CryptoARC operates on the principles of DHCP-based server discovery and secure RESTful communication for device registration. Upon network connection, devices listen for specific DHCP options to identify management servers and optionally receive encryption keys for secure data transmission. Devices initiate contact with designated servers, providing essential identification and configuration data, facilitating seamless integration and management.

## 3. Protocol Details

### 3.1 DHCP Options

1. Option 240 (Server IP Address): Specifies the IP address(es) of the management server to which devices should connect for registration.
2. Option 241 (Public Key): Optionally provides a base64-encoded public key for encrypting registration data, ensuring secure transmission between devices and servers.

### 3.2 Device Registration Process

1. DHCP Option Reception: Devices receive DHCP options 240 and optionally 241 upon network connection.
2. Server Identification: Devices initiate a RESTful POST request to the server IP specified in Option 240.
3. Registration Payload: Devices send registration data, including a mandatory unique identifier, as well as optional data such as the serial numbers, device types, MAC addresses, manufacturer details or model information.
4. Server Processing: The management server validates and stores device information in a central database. The server will return a JSON encoded payload containing a 'success' message that will be 'true' upon success. 
5. Optional Configuration: Upon successful registration, the server may push initial configuration settings to the device, facilitating rapid deployment and operational readiness.

### 3.3 Security Considerations

CryptoARC operates within trusted network environments where devices and servers are safeguarded against unauthorized access.
Option Y supports optional encryption of registration data, utilizing public-key cryptography to ensure confidentiality and integrity during transmission.

### 3.4 Operational Considerations

Retry Mechanism: Devices implement exponential backoff and retry mechanisms for registration attempts, adhering to configurable intervals (e.g., starting with 60 seconds and doubling each retry) to manage network traffic and server load effectively.
Usage in Fleet or System Management: CryptoARC is designed to scale across fleet or system management environments, enabling centralized management and configuration of multiple devices. It supports seamless integration with existing network management tools and frameworks.


## Example DHCP Configuration File
```
# Standard DHCP options should be defind as required with your network

# CryptoARC enabled example subnet definition 
subnet 192.168.1.0 netmask 255.255.255.0 {
    range 192.168.1.100 192.168.1.200;
    option routers 192.168.1.1;
    option subnet-mask 255.255.255.0;
    # Define CryptoARC options
    option CryptoARC-Server-IP code 230 = string;
    option CryptoARC-Public-Key code 231 = text;
    # DHCP pool configuration
    pool {
        allow unknown-clients;
        option CryptoARC-Server-IP "192.168.1.10, 192.168.1.11, 192.168.1.12";  # Replace with your management server IPs
        option CryptoARC-Public-Key "base64_encoded_public_key_here"; # Define only if you want to use the optional crypto validation, otherwise ommit this line 
        range 192.168.1.100 192.168.1.150;
    }
}
```
