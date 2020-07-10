# GuestiFi <!-- omit in toc -->

- [Abstract](#abstract)
- [Problem](#problem)
  - [Open Wi-Fi + Captive Portal](#open-wi-fi--captive-portal)
  - [WPA2 PSK (Personal)](#wpa2-psk-personal)
  - [WPA2 Enterprise](#wpa2-enterprise)
- [Implementation](#implementation)
  - [Operation Mode 1: One Key Per Device](#operation-mode-1-one-key-per-device)
  - [Operation Mode 2: Time-Based Password Generation](#operation-mode-2-time-based-password-generation)
  - [Operation Mode 3: Fully Managed EAP-TLS](#operation-mode-3-fully-managed-eap-tls)
  - [Operation Mode 4: SCEP + EAP-TLS](#operation-mode-4-scep--eap-tls)
- [Advantages](#advantages)

## Abstract

The GuestiFi solution addresses the problem of WiFi PSK sharing or leaking by assigning every device (MAC address) a set of username and password at a lower cost as compared to a full fledged WPA2 Enterprise. This solution is faster and easier to deploy, has a lower cost to maintain, and is thus a good option for small businesses or homes with higher security demands.

## Problem

### Open Wi-Fi + Captive Portal

Several guest Wi-Fi solutions from companies like Datavalet (e.g. Starbucks), Cisco Systems and Aruba Networks use the combination of open Wi-Fi and captive portal (web authentication). Although users can be authenticated via a web portal before granted access to internet or LAN, data transmission is not secured.

### WPA2 PSK (Personal)

WPA2 PSK, or WPA2 Personal, as the name suggests, is originally intended for personal use. It is not very secure of a solution for businesses or homes with higher security requirements due to the shareability of the single pre-shared key. The key can be extracted by users or malicious apps and shared with unintended parties. This can even be done on iOS platforms.

### WPA2 Enterprise

WPA2 Enterprise is a safer solution, as each user needs to have an account on the RADIUS server. However, it might not feasible in all scenarios for the host to create a set of credentials for every guest, especially at businesses with high-flow of guests.

## Implementation

There will be several modes of operation. One key per device for maximum security, or time-based password generation for better accessibility. Clients can scan QR codes generated based on the [ISO/IEC 18004:2015: QR Code bar code symbology specification](https://www.iso.org/standard/62021.html) for easy login without having to enter the username and password manually.

### Operation Mode 1: One Key Per Device

1. Controller generates new username and password
1. Controller writes username and password into RADIUS database
1. User logs in with username password
1. Upon receiving authentication request
   1. Check if username and password matches
   1. Check if there's a MAC address associated with the credentials
      - **Yes**: Check if MAC current MAC address matches the MAC address registered. If yes, permit login request
      - **No**: If username and password matches and no MAC address is associated with the current authenticated account, permit login and associate credentials with current MAC address
1. Controller rotates and generates a new set of credentials
1. New cycle begins

### Operation Mode 2: Time-Based Password Generation

1. Controller generates new username and password via time-based password generator
1. Controller writes username and password into RADIUS database
1. User logs in with username password
1. Upon receiving authentication request
   1. Check if username and password matches
   1. Check if there's a MAC address associated with the credentials
      - **Yes**: Check if MAC current MAC address matches the MAC address registered. If yes, permit login request
      - **No**: If username and password matches and no MAC address is associated with the current authenticated account, permit login and associate credentials with current MAC address
1. Controller waits until the password timer expires, rotates and generates a new set of credentials
1. New cycle begins

### Operation Mode 3: Fully Managed EAP-TLS

1. Controller init CA
1. Controller generate (Private Key, Public Key, CA Cert) bundle, send to client device
1. Client device import certs
1. Perform EAP-TLS auth

### Operation Mode 4: SCEP + EAP-TLS

1. Controller init CA
1. Send cert template params (key length, algorithm) to client via side channel (iOS Configuration Profile)
1. Client device generate (private key, CSR)
1. Client device request certificate via SCEP server on Internet
1. Perform EAP-TLS auth

## Advantages

- **Better security**
  - WPA2 Enterprise provides significantly better security than PSK or plain portal
  - No shared accounts
  - GuestiFi credentials are harder to get shared
    - Guest credentials can be set to expire
    - One set of credentials may be corresponded to only one MAC address
    - WiFi key sharing applications typically do not support WPA2 Enterprise
  - Guests must have physical access to the kiosk to obtain valid credentials
- **Low cost to deploy**
  - Only a Raspberry Pi and several APs that support RADIUS are needed
  - Can fit right into current infrastructures as an addon
- **Low operation cost**
  - No manual user creation process for guests
  - There's no need to rotate the passwords manually
- **Better manageability**
  - Provides the ability to log or invalidate individual devices
  - Different usernames can be used to separate devices into different VLANs if supported by AP
