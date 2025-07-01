# Scope

This document references requirements and provide the usage examples for the OCP Service Baseline API v1.0.0.

# Requirements

As a Redfish-based interface, the required Redfish interface model elements are specified in a profile document.
For the OCP Service Baseline API v1.0.0, the profile is located at: https://github.com/opencomputeproject/HWMgmt-OCP-Profiles/blob/master/OCPServiceBaseline.v1_0_0.json

The [Redfish Interop Validator](#interop-validator) is an open-source conformance test that reads the profile, executes the tests against an implementation, and generates a test report in text or HTML format.

```
> rf_interop_validator -u user -p password -r host:port profileName
```

# Capabilities

The following table lists the capabilities prescribed in the OCP Service Baseline profile.

| Use Case                 | Management Task                                                                   | Requirement |
| :---                     | :---------                                                                        | :---        |
| User account management  | [Get user accounts](#get-user-accounts)                                           | Mandatory |
|                          | [Create a new user](#create-a-new-user)                                           | Mandatory |
|                          | [Remove a user](#remove-a-user)                                                   | Mandatory |
| Certificate management   | [Generate a CSR](#generate-a-csr)                                                 | Mandatory |
|                          | [Replace a certificate](#replace-a-certificate)                                   | Mandatory |
|                          | [Get a certificate](#get-a-certificate)                                           | Mandatory |
| Event notifications      | [Get an event subscription](#get-an-event-subscription)                           | Mandatory |
|                          | [Create an event subscription](#create-an-event-subscription)                     | Mandatory |
|                          | [Remove an event subscription](#remove-an-event-subscription)                     | Mandatory |
|                          | [Send an event to a subscriber](#send-an-event-to-a-subscriber)                   | Mandatory |
| Manager IP configuration | [Get manager IP configuration](#get-manager-ip-configuration)                     | Mandatory |
|                          | [Set manager to a static IP address](#set-manager-to-a-static-ip-address)         | IfImplemented |
|                          | [Set manager to DHCP](#set-manager-to-dhcp)                                       | Recommended |
| License management       | [Get a software license](#get-a-software-license)                                 | Recommended |
|                          | [Install a software license](#install-a-software-license)                         | Recommended |
|                          | [Uninstall a software license](#uninstall-a-software-license)                     | Recommended |
| Event logging            | [Get an event log](#get-an-event-log)                                             | Mandatory |
|                          | [Clear an event log](#clear-an-event-log)                                         | Mandatory |
| Software managemnent     | [Software inventory](#software-inventory)                                         | Mandatory |
|                          | [Firmware update](#firmware-update)                                               | Mandatory |
| Protocol configuration   | [Get manager network protocols](#get-manager-network-protocols)                   | Mandatory |
|                          | [Set manager network protocols](#set-manager-network-protocols)                   | Recommended |
| Service condition        | [Get service conditions](#get-service-conditions)                                 | Recommended |
| Registered client        | [Get a registered client](#get-a-registered-client)                               | Recommended |
|                          | [Create a registered client](#create-a-registered-client)                         | Recommended |
|                          | [Remove a registered client](#remove-a-registered-client)                         | Recommended |
| Session management       | [Get a session](#get-a-session)                                                   | Mandatory |
|                          | [Create a session](#create-a-session)                                             | Mandatory |
|                          | [Delete a session](#delete-a-session)                                             | Mandatory |
|                          | [Change session timeout](#change-session-timeout)                                 | Recommended |
| Task management          | [Get a task](#get-a-task)                                                         | IfImplemented |

# Use cases

This section describes how each capability is accomplished by interacting with the Redfish service.

## Get user accounts

The `ManagerAccount` resource represents an individual user that can access the Redfish service.
For the full schema definition, see the `ManagerAccount` sections of the reference guide in the [*Redfish Data Model Specification*](#dsp0268).

To access user account information, perform a `GET` operation on a `ManagerAccount` resource:

```http
GET /redfish/v1/AccountService/Accounts/1
```

```json
{
    "@odata.id": "/redfish/v1/AccountService/Accounts/1",
    "@odata.type": "#ManagerAccount.v1_13_0.ManagerAccount",
    "Id": "1",
    "Name": "User Account",
    "Description": "User Account",
    "Enabled": true,
    "Password": null,
    "PasswordChangeRequired": false,
    "AccountTypes": [
        "Redfish"
    ],
    "UserName": "Administrator",
    "RoleId": "Administrator",
    "Locked": false,
    "Links": {
        "Role": {
            "@odata.id": "/redfish/v1/AccountService/Roles/Administrator"
        }
    }
}
```

## Create a new user

The `ManagerAccount` resource represents an individual user that can access the Redfish service.
For the full schema definition, see the `ManagerAccount` sections of the reference guide in the [*Redfish Data Model Specification*](#dsp0268).

To add a new user account, perform a `POST` operation on the `ManagerAccountCollection` resource:

```http
POST /redfish/v1/AccountService/Accounts
Content-Type: application/json

{
    "UserName": "NewUser",
    "Password": "N3wP@ssw0RD",
    "RoleId": "Operator",
    "Enabled": true
}
```

## Remove a user

The `ManagerAccount` resource represents an individual user that can access the Redfish service.
For the full schema definition, see the `ManagerAccount` sections of the reference guide in the [*Redfish Data Model Specification*](#dsp0268).

To remove a user account, perform a `DELETE` operation on a `ManagerAccount` resource:

```http
DELETE /redfish/v1/AccountService/Accounts/3
```

## Generate a CSR

The `CertificateService` resource contains service-level actions for managing certificates and references to all certificates in the service.
For the full schema definition, see the `CertificateService` sections of the reference guide in the [*Redfish Data Model Specification*](#dsp0268).

To generate a certificate signing request for a new certificate, perform a `POST` operation on the `GenerateCSR` action:

```http
POST /redfish/v1/CertificateService/Actions/CertificateService.GenerateCSR HTTP/1.1
Content-Type: application/json

{
    "Country": "US",
    "State": "CA",
    "City": "San Jose",
    "Organization": "Contoso",
    "OrganizationalUnit": "HW Mgmt",
    "CommonName": "redfish-100.contoso.org",
    "AlternativeNames": [
        "redfish-100.contoso.org",
        "redfish-100.contoso.com",
        "redfish-100.contoso.us"
    ],
    "Email": "admin@contoso.org",
    "KeyPairAlgorithm": "TPM_ALG_RSA",
    "KeyBitLength": 4096,
    "KeyUsage": [
        "KeyEncipherment",
        "ServerAuthentication"
    ],
    "CertificateCollection": {
        "@odata.id": "/redfish/v1/Managers/1/NetworkProtocol/HTTPS/Certificates"
    }
}


HTTP/1.1 200 OK
Content-Type: application/json

{
    "CSRString": "-----BEGIN CERTIFICATE REQUEST-----...-----END CERTIFICATE REQUEST-----",
    "CertificateCollection": {
        "@odata.id": "/redfish/v1/Managers/1/NetworkProtocol/HTTPS/Certificates"
    }
}
```

## Replace a certificate

The `CertificateService` resource contains service-level actions for managing certificates and references to all certificates in the service.
For the full schema definition, see the `CertificateService` sections of the reference guide in the [*Redfish Data Model Specification*](#dsp0268).

To replace a certificate on the service with a new certificate, perform a `POST` operation on the `ReplaceCertificate` action:

```http
POST /redfish/v1/CertificateService/Actions/CertificateService.ReplaceCertificate HTTP/1.1
Content-Type: application/json

{
    "CertificateUri": {
        "@odata.id": "/redfish/v1/Managers/1/NetworkProtocol/HTTPS/Certificates/1"
    },
    "CertificateString": "-----BEGIN CERTIFICATE-----\n...\n-----END CERTIFICATE-----",
    "CertificateType": "PEM"
}


HTTP/1.1 204 No Content
Content-Type: application/json
Location: /redfish/v1/Managers/1/NetworkProtocol/HTTPS/Certificates/2
```

## Get a certificate

The `Certificate` resource represents an individual certificate availabe on the Redfish service.
For the full schema definition, see the `Certificate` sections of the reference guide in the [*Redfish Data Model Specification*](#dsp0268).

To view information about a certificate, perform a `GET` operation on a `Certificate` resource:

```http
GET /redfish/v1/Managers/1/NetworkProtocol/HTTPS/Certificates/1
```

```json
{
    "@odata.id": "/redfish/v1/Managers/1/NetworkProtocol/HTTPS/Certificates/1",
    "@odata.type": "#Certificate.v1_1_0.Certificate",
    "Id": "1",
    "Name": "HTTPS Certificate",
    "CertificateString": "-----BEGIN CERTIFICATE-----\n...\n-----END CERTIFICATE-----",
    "CertificateType": "PEM",
    "Issuer": {
        "Country": "US",
        "State": "CA",
        "City": "San Jose",
        "Organization": "Contoso",
        "OrganizationalUnit": "HW Mgmt",
        "CommonName": "redfish-100.contoso.org"
    },
    "Subject": {
        "Country": "US",
        "State": "CA",
        "City": "San Jose",
        "Organization": "Contoso",
        "OrganizationalUnit": "HW Mgmt",
        "CommonName": "redfish-100.contoso.org"
    },
    "ValidNotBefore": "2024-11-15T12:00:00Z",
    "ValidNotAfter": "2029-11-15T12:00:00Z",
    "KeyUsage": [
        "KeyEncipherment",
        "ServerAuthentication"
    ]
}
```

## Get an event subscription

The `EventDestination` resource represents an individual subscriber listening for Redfish events.
For the full schema definition, see the `EventDestination` section of the reference guide in the [*Redfish Data Model Specification*](#dsp0268).

To access subscription information for an event listener, perform a `GET` operation on a `EventDestination` resource:

```http
GET /redfish/v1/EventService/Subscriptions/1
```

```json
{
    "@odata.id": "/redfish/v1/EventService/Subscriptions/1",
    "@odata.type": "#EventDestination.v1_15_1.EventDestination",
    "Id": "1",
    "Name": "Subscription 'Data Center Monitor'",
    "Destination": "https://www.contoso.org/DataCenterListener",
    "SubscriptionType": "RedfishEvent",
    "DeliveryRetryPolicy": "TerminateAfterRetries",
    "Status": {
        "State": "Enabled",
        "Health": "OK"
    },
    "Context": "Data Center Monitor",
    "Protocol": "Redfish",
}
```

## Create an event subscription

The `EventDestination` resource represents an individual subscriber listening for Redfish events.
For the full schema definition, see the `EventDestination` section of the reference guide in the [*Redfish Data Model Specification*](#dsp0268).

To add a new subscription for events, perform a `POST` operation on the `EventDestinationCollection` resource:

```http
POST /redfish/v1/EventService/Subscriptions
Content-Type: application/json

{
    "Destination": "https://monitor.contoso.org/events",
    "SubscriptionType": "RedfishEvent",
    "Context": "Event Monitor",
    "Protocol": "Redfish",
}
```

## Remove an event subscription

The `EventDestination` resource represents an individual subscriber listening for Redfish events.
For the full schema definition, see the `EventDestination` section of the reference guide in the [*Redfish Data Model Specification*](#dsp0268).

To remove a subscription, perform a `DELETE` operation on an `EventDestination` resource:

```http
DELETE /redfish/v1/EventService/Subscriptions/3
```

## Send an event to a subscriber

The `Event` object is an event payload sent from a Redfish service to a subscriber.
For the full schema definition, see the `Event` section of the reference guide in the [*Redfish Data Model Specification*](#dsp0268).

To transmit an event from a Redfish service to an event listener, the service performs a `POST` operation on the URI contained by the `Destination` for each applicable `EventDestination` resource.

```http
POST /events
Content-Type: application/json

{
    "@odata.type": "#Event.v1_7_0.Event",
    "Id": "1",
    "Name": "Event Array",
    "Context": "Event Monitor",
    "Events": [
        {
            "EventType": "Other",
            "EventId": "346",
            "Severity": "Critical",
            "Message": "Temperature 'Ambient' reading of 50 degrees (C) is above the 45 upper critical threshold.",
            "MessageId": "Environmental.1.1.TemperatureAboveUpperCriticalThreshold",
            "MessageArgs": [
                "1",
                "1"
            ],
            "OriginOfCondition": {
                "@odata.id": "/redfish/v1/Chassis/1/Sensors/Ambient"
            },
            "LogEntry": {
                "@odata.id": "/redfish/v1/Managers/1/LogServices/EventLog/Entries/3"
            }
        }
    ]
}
```

## Get manager IP configuration

The `EthernetInterface` resources subordinate to the `Manager` resource represent the network interfaces available on the Redfish service.
For the full schema definition, see the `EthernetInterface` section of the reference guide in the [*Redfish Data Model Specification*](#dsp0268).

To access Ethernet interface information for an interface on the service, perform a `GET` operation on an `EthernetInterface` resource:

```http
GET /redfish/v1/Managers/1/EthernetInterfaces/1
```

```json
{
    "@odata.id": "/redfish/v1/Managers/1/EthernetInterfaces/1",
    "@odata.type": "#EthernetInterface.v1_12_3.EthernetInterface",
    "Id": "1",
    "Name": "Ethernet Interface 1",
    "Status": {
        "State": "Enabled",
        "Health": "OK"
    },
    "LinkStatus": "LinkUp",
    "PermanentMACAddress": "12:44:6A:3B:04:11",
    "MACAddress": "12:44:6A:3B:04:11",
    "SpeedMbps": 1000,
    "AutoNeg": true,
    "FullDuplex": true,
    "MTUSize": 1500,
    "HostName": "redfish100",
    "FQDN": "redfish100.contoso.com",
    "NameServers": [
        "names.contoso.com"
    ],
    "IPv4Addresses": [
        {
            "Address": "192.168.0.10",
            "SubnetMask": "255.255.252.0",
            "AddressOrigin": "DHCP",
            "Gateway": "192.168.0.1"
        }
    ],
    "IPv4StaticAddresses": [
        null
    ],
    "DHCPv4": {
        "DHCPEnabled": true,
        "UseDNSServers": true,
        "UseGateway": true,
        "UseNTPServers": false,
        "UseStaticRoutes": true,
        "UseDomainName": true
    },
    "VLAN": {
        "VLANEnable": false,
        "VLANId": 101
    }
}
```

## Set manager to a static IP address

To set an Ethernet interface to a static IP address, perform a `PATCH` operation on the desired `EthernetInterface` resource.
In the request body, specify a static IP address and disable DHCP.

```http
PATCH /redfish/v1/Managers/1/EthernetInterfaces/1
Content-Type: application/json

{
    "IPv4StaticAddresses": [
        {
            "Address": "192.190.40.55",
            "SubnetMask": "255.255.252.0",
            "Gateway": "192.190.40.1"
        }
    ],
    "DHCPv4": {
        "DHCPEnabled": false
    }
}
```

## Set manager to DHCP

To set an Ethernet interface to DHCP, perform a `PATCH` operation on the desired `EthernetInterface` resource.
In the request body, enable DHCP and optionally clear the static address array.

```http
PATCH /redfish/v1/Managers/1/EthernetInterfaces/1
Content-Type: application/json

{
    "IPv4StaticAddresses": [
        null
    ],
    "DHCPv4": {
        "DHCPEnabled": true
    }
}
```

## Get a software license

The `License` resource represents an individual license availabe on the Redfish service.
For the full schema definition, see the `License` section of the reference guide in the [*Redfish Data Model Specification*](#dsp0268).

To retrieve information about a license, perform a `GET` operation on a `License` resource:

```http
GET /redfish/v1/LicenseService/Licenses/RemotePresence
```

```json
{
    "@odata.id": "/redfish/v1/LicenseService/Licenses/RemotePresence",
    "@odata.type": "#License.v1_1_3.License",
    "Id": "RemotePresence",
    "Name": "Remote Presence Feature License",
    "Status": {
        "State": "Enabled",
        "Health": "OK"
    },
    "EntitlementId": "LIC2130213RP",
    "LicenseType": "Production",
    "Removable": false,
    "LicenseOrigin": "BuiltIn",
    "AuthorizationScope": "Service",
    "Manufacturer": "Contoso",
    "LicenseInfoURI": "http://licenses.contoso.org/licenses/remote-presence"
}
```

## Install a software license

The `License` resource represents an individual license availabe on the Redfish service.
For the full schema definition, see the `License` section of the reference guide in the [*Redfish Data Model Specification*](#dsp0268).

To install a new license, perform a `POST` operation on the `LicenseCollection` resource:

```http
POST /redfish/v1/LicenseService/Licenses
Content-Type: application/json

{
    "LicenseString": "PENvbnRvc29IZWFkZXI+PERhdGE9IkxpY2Vuc2VEYXRhIi8+PC9Db250b3NvSGVhZGVyPg=="
}
```

## Uninstall a software license

The `License` resource represents an individual license availabe on the Redfish service.
For the full schema definition, see the `License` section of the reference guide in the [*Redfish Data Model Specification*](#dsp0268).

To uninstall a license, perform a `DELETE` operation on a `License` resource:

```http
DELETE /redfish/v1/LicenseService/Licenses/Feature25
```

## Get an event log

The `LogEntry` resource represents a single entry in a given log with event details.
For the full schema definition, see the `LogEntry` section of the reference guide in the [*Redfish Data Model Specification*](#dsp0268).

To retrieve log entries for a given log service, perform a `GET` operation on a `LogEntryCollection` resource:

```http
GET /redfish/v1/Managers/1/LogServices/EventLog/Entries
```

```json
{
    "@odata.id": "/redfish/v1/Managers/1/LogServices/EventLog/Entries",
    "@odata.type": "#LogEntryCollection.LogEntryCollection",
    "Name": "Log Entry Collection",
    "Members@odata.count": 1,
    "Members": [
        {
            "@odata.id": "/redfish/v1/Managers/1/LogServices/EventLog/Entries/1",
            "@odata.type": "#LogEntry.v1_19_0.LogEntry",
            "Id": "1",
            "Name": "Log Entry 1",
            "EntryType": "Event",
            "Severity": "Warning",
            "Created": "2025-06-30T08:30:00Z",
            "Message": "Sensor 'Ambient' reading of 42 (Cel) is above the 40 upper caution threshold.",
            "MessageId": "Sensor.1.0.ReadingAboveUpperCautionThreshold",
            "MessageArgs": [
                "Ambient",
                "42",
                "Cel",
                "40"
            ],
            "Links": {
                "OriginOfCondition": {
                    "@odata.id": "/redfish/v1/Chassis/1U/Sensors/Ambient"
                }
            }
        }
    ]
}
```

## Clear an event log

The `LogService` resource represents a logging facility in the server.
For the full schema definition, see the `LogService` section of the reference guide in the [*Redfish Data Model Specification*](#dsp0268).

To clear the log entries for a given log service, perform a `POST` operation on the `ClearLog` action:

```http
POST /redfish/v1/Managers/1/LogServices/EventLog/Actions/LogService.ClearLog
Content-Type: application/json

{}
```

## Software inventory

The `SoftwareInventory` resource contains information about a software or firmware component installed on one or more device managed by the service.
The components are shown in two separate resource collections, divided by usage as either firmware and software.
For the full schema definition, see the `SoftwareInventory` section of the reference guide in the [*Redfish Data Model Specification*](#dsp0268).

To retrieve information about an individual software or firmware component, perform a `GET` operation on a `SoftwareInventory` resource:

```http
GET /redfish/v1/UpdateService/FirmwareInventory/BMC
```

```json
{
    "@odata.id": "/redfish/v1/UpdateService/FirmwareInventory/BMC",
    "@odata.type": "#SoftwareInventory.v1_10_2.SoftwareInventory",
    "Id": "BMC",
    "Name": "Contoso BMC Firmware",
    "Status": {
        "State": "Enabled",
        "Health": "OK"
    },
    "Updateable": true,
    "Manufacturer": "Contoso",
    "ReleaseDate": "2017-08-22T12:00:00",
    "Version": "1.45.455b66-rev4",
    "SoftwareId": "1624A9DF-5E13-47FC-874A-DF3AFF143089",
    "LowestSupportedVersion": "1.30.367a12-rev1",
    "UefiDevicePaths": [
        "BMC(0x1,0x0ABCDEF)"
    ],
    "RelatedItem": [
        {
            "@odata.id": "/redfish/v1/Managers/1"
        }
    ]
}
```

## Firmware update

The `UpdateService` resource contains methods for installing new firmware managed by the service.
It is recommended that services support both styles of performing firmware updates: the "push" method performed with a `POST` operation to the URI specified by the `MultipartHttpPushUri` propertu, and a "pull" method performed with the `SimpleUpdate` action.
For the full schema definition, see the `UpdateService` section of the reference guide in the [*Redfish Data Model Specification*](#dsp0268).
See the "Update service" clause of the [*Redfish Specification*](#dsp0266) for additional details.

To retrieve information about the supported update methods, perform a `GET` operation on the `UpdateService` resource:

```http
GET /redfish/v1/UpdateService
```

```json
{
    "@odata.id": "/redfish/v1/UpdateService",
    "@odata.type": "#UpdateService.v1_12_0.UpdateService",
    "Id": "UpdateService",
    "Name": "Update service",
    "Status": {
        "State": "Enabled",
        "Health": "OK",
    },
    "ServiceEnabled": true,
    "MultipartHttpPushUri": "/redfish/v1/UpdateService/FWUpdate",
    "FirmwareInventory": {
        "@odata.id": "/redfish/v1/UpdateService/FirmwareInventory"
    },
    "Actions": {
        "#UpdateService.SimpleUpdate": {
            "target": "/redfish/v1/UpdateService/Actions/SimpleUpdate",
            "@Redfish.ActionInfo": "/redfish/v1/UpdateService/SimpleUpdateActionInfo"
        }
    }
}
```

To install a new firmware image, perform a `POST` operation on the `SimpleUpdate` action:

```http
POST /redfish/v1/UpdateService/Actions/UpdateService.SimpleUpdate
Content-Type: application/json

{
    "ImageURI": "https://images.contoso.org/bmc_0260_2021.bin"
}
```

## Get manager network protocols

The `ManagerNetworkProtocol` resource settings for the external protocol provided by the service.
For the full schema definition, see the `For the full schema definition, see the `ManagerNetworkProtocol` section of the reference guide in the [*Redfish Data Model Specification*](#dsp0268).

To retrieve information about the manager's supported protocols and their configuration, perform a `GET` operation on the `ManagerNetworkProtocol` resource for that manager:

```http
GET /redfish/v1/Managers/1/NetworkProtocol
```

```json
{
    "@odata.id": "/redfish/v1/Managers/1/NetworkProtocol",
    "@odata.type": "#ManagerNetworkProtocol.v1_9_1.ManagerNetworkProtocol",
    "Id": "NetworkProtocol",
    "Name": "Manager Network Protocol",
    "Status": {
        "State": "Enabled",
        "Health": "OK"
    },
    "HostName": "rack42-pdu",
    "FQDN": "rack42-pdu.dmtf.org",
    "HTTP": {
        "ProtocolEnabled": true,
        "Port": 80
    },
    "HTTPS": {
        "ProtocolEnabled": true,
        "Port": 443
    },
    "SSH": {
        "ProtocolEnabled": true,
        "Port": 22
    },
    "SNMP": {
        "ProtocolEnabled": true,
        "Port": 161
    },
    "SSDP": {
        "ProtocolEnabled": true,
        "Port": 1900,
        "NotifyMulticastIntervalSeconds": 600,
        "NotifyTTL": 5,
        "NotifyIPv6Scope": "Site"
    }
}
```

## Set manager network protocols

The `ManagerNetworkProtocol` resource settings for the external protocol provided by the service.
For the full schema definition, see the `For the full schema definition, see the `ManagerNetworkProtocol` section of the reference guide in the [*Redfish Data Model Specification*](#dsp0268).

To configure the settings for protocols exposed by the service, perform a `PATCH` operation on the `ManagerNetworkProtocol` resource.
In the request body, specify the new protocol settings.

```http
PATCH /redfish/v1/Managers/1/NetworkProtocol
Content-Type: application/json

{
    "SNMP": {
        "ProtocolEnabled": false
    }
}
```

## Get service conditions

The `ServiceConditions` resource contains active conditions detected by the service.
For the full schema definition, see the `ServiceConditions` section of the reference guide in the [*Redfish Data Model Specification*](#dsp0268).

To retrieve active conditions detected by the service, perform a `GET` operation on the `ServiceConditions` resource:

```http
GET /redfish/v1/ServiceConditions
```

```json
{
    "@odata.id": "/redfish/v1/ServiceConditions",
    "@odata.type": "#ServiceConditions.v1_0_1.ServiceConditions",
    "Name": "Redfish Service Conditions",
    "HealthRollup": "Warning",
    "Conditions": [
        {
            "MessageId": "Sensor.1.0.ReadingAboveUpperCautionThreshold",
            "Timestamp": "2025-06-30T08:30:00Z",
            "Message": "Sensor 'Ambient' reading of 42 (Cel) is above the 40 upper caution threshold.",
            "Severity": "Warning",
            "MessageArgs": [
                "Ambient",
                "42",
                "Cel",
                "40"
            ],
            "OriginOfCondition": {
                "@odata.id": "/redfish/v1/Chassis/1U/Sensors/Ambient"
            },
            "LogEntry": {
                "@odata.id": "/redfish/v1/Managers/1/LogServices/EventLog/Entries/1"
            }
        }
    ]
}
```

## Get a registered client

The `RegisteredClient` resource contains information about a client that is monitoring or controling resources provided by the service.
For the full schema definition, see the `RegisteredClient` section of the reference guide in the [*Redfish Data Model Specification*](#dsp0268).

To retrieve information about an individual registered client, perform a `GET` operation on a `RegisteredClient` resource:

```http
GET /redfish/v1/RegisteredClients/1
```

```json
{
    "@odata.id": "/redfish/v1/RegisteredClients/1",
    "@odata.type": "#RegisteredClient.v1_1_2.RegisteredClient",
    "Id": "1",
    "Name": "Configuration Client",
    "ClientType": "Configure",
    "CreatedDate": "2025-05-15T12:00:00Z",
    "ManagedResources": [
        {
            "ManagedResourceURI": "/redfish/v1/Systems",
            "PreferExclusive": true,
            "IncludesSubordinates": true
        },
        {
            "ManagedResourceURI": "/redfish/v1/Chassis",
            "PreferExclusive": true,
            "IncludesSubordinates": true
        }
    ],
    "ClientURI": "https://192.168.68.113/ConfigurationManagerConsole",
    "Context": "Contoso Console 1568ABEF145"
}
```

## Create a registered client

The `RegisteredClient` resource contains information about a client that is monitoring or controling resources provided by the service.
For the full schema definition, see the `RegisteredClient` section of the reference guide in the [*Redfish Data Model Specification*](#dsp0268).

To register a new client, perform a `POST` operation on the `RegisteredClientCollection` resource:

```http
POST /redfish/v1/RegisteredClients
Content-Type: application/json

{
    "Name": "Monitoring Client",
    "ClientType": "Monitor",
    "ManagedResources": [
        {
            "ManagedResourceURI": "/redfish/v1/Chassis",
            "IncludesSubordinates": true
        }
    ],
    "ClientURI": "https://192.168.68.113/MonitoringConsole",
    "Context": "Contoso Console 89189FEAB"
}
```

## Remove a registered client

The `RegisteredClient` resource contains information about a client that is monitoring or controling resources provided by the service.
For the full schema definition, see the `RegisteredClient` section of the reference guide in the [*Redfish Data Model Specification*](#dsp0268).

To remove a registered client, perform a `DELETE` operation on the `RegisteredClient` resource:

```http
DELETE /redfish/v1/RegisteredClients/2
```

## Get a session

The `Session` resource represents a user session open with the service.
For the full schema definition, see the `Session` sections of the reference guide in the [*Redfish Data Model Specification*](#dsp0268).

To retrieve information about an individual session, perform a `GET` operation on a `Session` resource:

```http
GET /redfish/v1/SessionService/Sessions/1
```

```json
{
    "@odata.id": "/redfish/v1/SessionService/Sessions/1"
    "@odata.type": "#Session.v1_8_0.Session",
    "Id": "1",
    "Name": "User Session",
    "UserName": "admin",
    "Password": null,
    "ClientOriginIPAddress": "192.168.35.147"
}
```

## Create a session

The `Session` resource represents a user session open with the service.
For the full schema definition, see the `Session` sections of the reference guide in the [*Redfish Data Model Specification*](#dsp0268).

To create a new session, perform a `POST` operation on the `SessionCollection` resource:

```http
POST /redfish/v1/SessionService/Sessions
Content-Type: application/json

{
    "UserName": "Fred",
    "Password": "MyP@ssw0rd"
}
```

## Delete a session

The `Session` resource represents a user session open with the service.
For the full schema definition, see the `Session` sections of the reference guide in the [*Redfish Data Model Specification*](#dsp0268).

To delete a session, perform a `DELETE` operation on the `Session` resource:

```http
DELETE /redfish/v1/SessionService/Sessions/1
```

## Change session timeout

The `SessionService` resource contains the session policies and session information for the service.
For the full schema definition, see the `SessionService` section of the reference guide in the [*Redfish Data Model Specification*](#dsp0268).

To change the session timeout limit for the service, perform a `PATCH` operation on the `SessionService` resource:

```http
PATCH /redfish/v1/SessionService
Content-Type: application/json

{
    "SessionTimeout": 600
}
```

## Get a task

The `Task` resource represents an individual task maintained by the service.
For the full schema definition, see the `Task` section of the reference guide in the [*Redfish Data Model Specification*](#dsp0268).

To retrieve information about an individual task, perform a `GET` operation on a `Task` resource:

```http
GET /redfish/v1/TaskService/Tasks/5
```

```json
{
    "@odata.id": "/redfish/v1/TaskService/Tasks/5",
    "@odata.type": "#Task.v1_7_4.Task",
    "Id": "5",
    "Name": "Task 5",
    "TaskMonitor": "/redfish/v1/TaskService/TaskMonitors/5",
    "TaskState": "Running",
    "StartTime": "2025-06-30T08:24:30Z",
    "Messages": []
}
```

# References

* <a id="dsp0266"/>DMTF DSP0266, *Redfish Specification*: [https://www.dmtf.org/dsp/DSP0266](https://www.dmtf.org/dsp/DSP0266)
* <a id="dsp0268"/>DMTF DSP0268, *Redfish Data Model Specification*: [https://www.dmtf.org/dsp/DSP0268](#dsp0268)
* <a id="dsp0270"/>DMTF DSP0270, *Redfish Interoperability Profiles Specification*: [https://www.dmtf.org/dsp/DSP0270](https://www.dmtf.org/dsp/DSP0270)
* <a id="interop-validator"/>Redfish Interop Validator: [https://github.com/DMTF/Redfish-Interop-Validator](https://github.com/DMTF/Redfish-Interop-Validator)

# Revision 

| Revision | Date       | Description |
| :---     | :---       | :---        |
| 1.0.0    | TBD        | Initial release. |
