# Unified Bharat e-Charge (UBC) - Technical Specification Document v0.9 <!-- omit from toc -->

<div align="center">
  <img width="600" alt="image" src="https://github.com/user-attachments/assets/188b1aca-f22f-4cb8-9d54-67949118c74d" />
</div>


## Table of Contents <!-- omit from toc -->
* [Disclaimer](#1-disclaimer)
* [Abstract](#2-abstract)
* [Introduction](#3-introduction)
* [Scope](#4-scope)
* [Intended Audience](#5-intended-audience)
* [Conventions and Terminology](#6-conventions-and-terminology)
* [Terminology](#7-terminology)
* [Reference Architecture](#8-reference-architecture)
* [Creating an Open Network for EV Charging](#9-creating-an-open-network-for-ev-charging)
* [Implementing EV Charging Semantics on Beckn Protocol](#10-implementing-ev-charging-semantics-on-beckn-protocol)
* [User Stories](#11-user-stories)

---

## 1. Disclaimer
This is a TSD for implementing EV charging use cases using the Beckn Protocol. It provides implementation guidance for anyone to build interoperable EV charging applications that integrate with each other on a decentralized network while maintaining compatibility with OCPI standards for CPO communication.

## 2. Abstract
This document proposes a practical way to make EV charging services easier to find and use by applying the Beckn Protocol's distributed commerce model. Instead of juggling multiple apps and accounts for different charging networks, EV users on any Beckn protocol-enabled consumer platform (a.k.a BAPs) can discover and book charging services from Beckn protocol-enabled provider platforms (a.k.a BPPs) that have onboarded one or more Charge Point Operators (CPOs).

EV users can discover, compare options, view transparent pricing, and reserve a slot at any eligible charging station owned or operated by CPOs. By standardizing discovery, pricing, and booking, the approach tackles three persistent barriers to EV adoption: charging anxiety, network fragmentation, and payment complexity.

Built on Beckn's commerce capabilities and aligned with OCPI for technical interoperability, the implementation lets e-Mobility Service Providers (eMSPs) aggregate services from multiple CPOs while delivering a consistent, app-agnostic experience to consumers.

## 3. Introduction
This document provides an implementation guidance for deploying EV charging services using the Beckn Protocol ecosystem. It specifically addresses how consumer applications can provide unified access to charging infrastructure across multiple Charge Point Operators while maintaining technical compatibility with existing OCPI-based systems.

## 4. Scope
**This document covers:**
* Architecture patterns for EV charging marketplace implementation using Beckn Protocol
* Discovery and charging mechanisms for charging EVs across multiple CPOs
* Some recommendations for BAPs, BPPs and NOs on how to map protocol API calls to internal systems (or vice-versa)
* Real-time availability and pricing integration with OCPI-based systems
* Session management and billing coordination between Beckn and OCPI protocols

**This document does NOT cover:**
* Detailed OCPI protocol specifications (refer to OCPI 2.2.1 documentation)
* Physical charging infrastructure requirements and standards
* Regulatory compliance beyond technical implementation (varies by jurisdiction)
* Smart grid integration and load management systems

## 5. Intended Audience
* **Consumer Application Developers (BAPs):** Building EV driver-facing charging applications with unified cross-network access
* **e-Mobility Service Providers (eMSPs/BPPs):** Implementing charging service aggregation platforms across multiple CPO networks
* **Charge Point Operators (CPOs):** Understanding integration requirements for Beckn-enabled marketplace participation
* **Technology Integrators:** Building bridges between existing OCPI infrastructure and new Beckn-based marketplaces
* **System Architects:** Designing scalable, interoperable EV charging ecosystems
* **Business Stakeholders:** Understanding technical capabilities and implementation requirements for EV charging marketplace strategies
* **Standards Organizations:** Evaluating interoperability approaches for future EV charging standards development

## 6. Conventions and Terminology
The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described below:

* **MUST:** The term "MUST" implies an absolute requirement.
* **MUST NOT:** The term "MUST NOT" indicates an absolute prohibition.
* **REQUIRED:** The term "REQUIRED" is synonymous with "MUST".
* **SHALL:** The term "SHALL" is equivalent to "MUST".
* **SHALL NOT:** The term "SHALL NOT" is equivalent to "MUST NOT".
* **SHOULD:** The term "SHOULD" indicates a strong recommendation.
* **SHOULD NOT:** The term "SHOULD NOT" indicates a strong recommendation against.
* **RECOMMENDED:** The term "RECOMMENDED" is synonymous with "SHOULD".
* **MAY:** The term "MAY" indicates that an item is truly optional.
* **OPTIONAL:** The term "OPTIONAL" is synonymous with "MAY".

## 7. Terminology

| Acronym | Full Form/Description | Description |
| :--- | :--- | :--- |
| **BAP** | Buyer Application Platform | Consumer-facing application that initiates transactions. Mapped to EV users and eMSPs. |
| **BPP** | Buyer Provider Platform | Service provider platform that responds to BAP requests. Mapped to CPOs. |
| **NO** | Network Operator | Organization responsible for the adoption and growth of the network. Usually the custodian of the network's registry. |
| **CDS** | Catalog Discovery Service | Enables discovery of charging services from BPPs in the network. |
| **eMSP** | e-Mobility Service Provider | Service provider that aggregates multiple CPOs. Generally onboarded by BAPs. |
| **CPO** | Charge Point Operator | Entity that owns and operates charging infrastructure. Generally onboarded by BPPs. |
| **EVSE** | Electric Vehicle Supply Equipment | Individual charging station unit. Owned and operated by CPOs. |
| **OCPI** | Open Charge Point Interface | Protocol for communication between eMSPs and CPOs. |

> **Note:** This document does not detail the mapping between Beckn Protocol and OCPI. Please refer to this document for the same. BPPs are NOT aggregators. Any CPO that has implemented a Beckn Protocol endpoint is a BPP. For all sense and purposes, CPOs are essentially BPPs and eMSPs are essentially BAPs.
> Network Operator (NO) would be NPCI BHIM SERVICES LIMITED (NBSL). Both NO and NBSL may be used interchangeably.

## 8. Reference Architecture
The section defines the reference ecosystem architecture that is used for building this implementation guide.

### 8.1. Architecture Diagram
<img width="2818" height="1200" alt="image" src="https://github.com/user-attachments/assets/6aecfdc2-3d80-4ce1-ad95-1346e4096200" />


### 8.2. Actors
* Catalog Discovery Service
* Buyer Application Platforms
* Buyer Provider Platforms
* Distributed Registry

### 8.3. Roles and Responsibilities of the Network Participants (BAP & BPP)
**The BAP is responsible for:**
* **Service Discovery:** Discovers available charging services and chargers on the network.
* **Booking and Transactions:** Interfaces with the BPP to avail offers, check dynamic pricing, and book charging services.
* **Session Management:** Views and tracks active charging sessions.
* **User Actions:** Allows customers to cancel orders, rate services, and contact support.

**The BPP is responsible for:**
* **Inventory & Services:** Maintains a live inventory of chargers and charging services.
* **Service Publication:** Publishes a catalog of its businesses, products, and services to other network participants.
* **Pricing & Terms:** Calculates pricing (statically or dynamically) and publishes its terms of service.
* **Bookings & Updates:** Receives bookings from Applications for charging services.
* **Customer Interaction:** Provides status updates on charging sessions, offers tracking information, and provides support details.
* **Feedback:** Allows its services to be rated by users and can request feedback.

### 8.4. Roles and Responsibilities of the Network Operator (NBSL)
* **Certifications:** As a network operator, we are responsible for certifying the BAP and BPP platforms to ensure they meet our standards for participation in the network. This process ensures trust and reliability within the ecosystem.
* **Registry:**
    * The network maintains a distributed registry that stores the certifications for both the Application (BAP) and the BPP (CPO). This registry holds the general schema of each BAP & BPP, including key links and the certificates we provide to validate their existence on the network.
    * The distributed nature of the registry means both the BAP & BPP are accessing this data from the same network they are a part of.
* **Data Handling:** As a network operator, NBSL would be requiring specific data to facilitate smooth and efficient transactions. Caching would help to improve performance and responsiveness across the network.
* **Endpoint:** The endpoints of the network participants (BAPs, BPPs) are stored to ensure requests are routed correctly and quickly.
* **Public Keys:** The public keys of the network participants are stored for message validation and security.
* **Search Catalogs:** We cache the search stored from various CPOs to speed up the discovery process when a user searches for a charging station in terms of presence and availability.

### 8.5. CDS - The Catalog Discovery Service
The CDS is responsible for handling discover API calls initiated by a BAP. It serves as the intermediary service that facilitates the discovery of available catalogs from BPPs within the network.

The CDS takes in the discover call from the BAP. Now the CDS has dual functionalities, where in, it would act as a fan-out feature and would have the choice to cache the presence and availability of BPPs. This is enabled with the publish API, where the BPPs would upload their catalog information. Here the **charging connectors** are placed as items. The items uploaded, along with their corresponding offers, would have time-based and expiry bound validity associated with them. The catalog, in terms of availability, is categorized as available and unavailable.

**CDS with respect to the flow:**
1. The discover API call is made to the CDS. The CDS will validate the sender BAP.
2. The CDS looks at the needs of the discover call and then would correspond that with the internal cache first and sends back the response to the BAP with the corresponding catalog through the on_discover call. If there is no availability currently, then there would be a fan-out, and the CDS will send out the same request to the BPPs who aren't caching their data.
3. The on_discover is sent to the BAP through the CDS even if a fan out occurs and BPPs individually respond with on_discover.

## 9. Creating an Open Network for EV Charging
The open network for EV charging requires all the EV charging BAPs, BPPs, to be able to discover each other and become part of a common network. This network is manifested in the form of a Distributed Registry maintained by NBSL (the Network Operator).

### 9.1. Distributed Registry
A Distributed Registry is a decentralized registry accessible to all network participants (NPs). It provides an authoritative source for network-wide identity, addressability, and certification of participants.
The Distributed Registry acts as the root of addressability and trust within the network. It maintains comprehensive details for each participant, including:
* The participant's globally unique identifier (ID)
* Network Address (Beckn API URL)
* Public keys for digital signatures
* Operational domains
* Assigned roles (e.g. BAP or BPP)

In addition to managing participant registration, authentication, authorization and permission control, the Registry also oversees participant verification, activation, and lifecycle management. This ensures that only validated and authorized entities can operate within the network.

Each participant published their details (including public keys and endpoints) in their respective registries. They public keys facilitate secure validation and signed communication between BAPs and BPPs.

We at NBSL shall be onboarding the participants post certification. Within the registry all the participants will place the session identifiers and their corresponding public keys. The public keys will help during validation of the rightful Network participant may they be a BAP or a BPP. Within the registry you will also have the space to put in the corresponding URLs as endpoints. Network Participants must create these records within the registry, post certification they will be added to NBSL's namespace through which they will be officially part of the network.

#### 9.1.1. For a Network Participant

**9.1.1.1. Step 1: Claiming a Namespace**
To ensure global addressability, all Network Participants (NPs)—including BAPs and BPPs—must register as users on publish.dedi.global. As part of this registration, participants are required to claim a unique namespace corresponding to their Fully Qualified Domain Name (FQDN).
To successfully claim a namespace, the user must prove domain ownership through the following whitelisting process:

***Registry Addition**: The domain must be sent in for whitelisting to the network operator.

***DNS Verification**: Post whitelisting the user must retrieve the DNS TXT record generated within publish.dedi.global and publish it as a record within their own whitelisted domain's DNS configuration

**9.1.1.2. Step 2: Setting up a Registry**
Once the namespace is claimed, each NP MUST create a Beckn NP registry in the namespace to list their subscriber details. While creating the registry, the user MUST configure it with the subscriber schema.

**9.1.1.3. Step 3: Publishing subscriber details**
In the registry that is created, NPs MUST publish their subscription details including their ID, network endpoints, public keys, operational domains and assigned roles (BAP, BPP) as records.
Detailed steps to create namespaces and registries in publish.dedi.global can be found here.

**9.1.1.4. Step 4: Share details of the registry created with the NBSL team**
Once the registry is created and details are published, the namespace and the registry name of the newly created registry should be shared with the beckn one team.

### 9.2. Setting up the Protocol Endpoints
This section contains instructions to set up and test the protocol stack for EV charging transactions.

#### 9.2.1. Installing Beckn ONIX
All NPs SHOULD install the Beckn ONIX adapter to quickly get set up and become Beckn Protocol compliant. Click here to learn how to set up Beckn ONIX.

#### 9.2.2. Configuring Beckn ONIX for EV Charging Transactions
A detailed Configuration Guide is available here. A quick read of key concepts from the link is recommended.
Specifically, for EV Charging, please use the Configuration Guide for the following configuration:
* Configure dediregistry plugin instead of registry plugin.
* Start with using Simplekeymanager plugin during development. For production deployment, you may setup vault.

#### 9.2.3. Performing a test EV charging transaction
**Step 1:** Download the postman collection, from here.
**Step 2:** Run API calls

**If you are a BAP:**
* Configure the collection/environment variables to the newly installed Beckn ONIX adapter URL and other variables in the collection.
* Select the discover example and hit send
* You should see the EV charging service catalog response

**If you are a BPP:**
* Configure the collection/environment variables to the newly installed Beckn ONIX adapter URL and other variables in the collection.
* Select the on_status example and hit send
* You should see the response in your console

## 10. Implementing EV Charging Semantics on Beckn Protocol
This section contains recommendations and guidelines on how to implement EV Charging Services on Beckn Protocol enabled networks. To ensure global interoperability between actors of the EV charging network, the semantics of the EV charging industry need to be mapped to the core schema of Beckn Protocol. The below table summarizes key semantic mappings between the EV Charging Domain and Beckn Protocol domain.

### 10.1. Key Assumptions
* **Assumption 1:** EV charging is treated as a service, not as a physical object.
* **Assumption 2:** All CPOs have implemented OCPI interfaces

Each entity in the charging lifecycle — the service, the commercial terms, and the usage instance — maps to a well-defined semantic concept, enabling platforms to exchange information in a standardized, machine-readable way.

### 10.2. Semantic Model

| EV Charging Domain Entity | Charging Example | Semantically maps to |
| :--- | :--- | :--- |
| **Charging Station** | "DC Fast-Charging (60 kW CCS2)" | **Item** |
| **Charging Service** | "₹18 per kWh", "₹150 per hour", "₹999 monthly pass", "Off-peak discount 2 AM–5 AM" | **Offer** |
| **Charging Session** | A specific booking or usage instance created when the user plugs in or reserves a slot | **Order** |

### 10.3. Example Category Codes
The following section contains example category codes that can be used:

#### Charger types
| Code | What it means |
| :--- | :--- |
| **AC_SLOW** | AC charge points in the "slow" band (≈ 3–7 kW). |
| **AC_FAST** | AC "fast" public charging (≈ 7–22 kW; UK fast band 8–49 kW covers AC up to ~22 kW). |
| **DC_FAST** | DC "rapid/fast" typically ~50–149 kW. |
| **DC_ULTRA** | DC "ultra-rapid/ultra-fast", ≥ 150 kW (also aligns with AFIR focus on ≥ 150 kW corridors). |

#### Connector types
| Code | What it means |
| :--- | :--- |
| **TYPE1** | Type 1 (SAE J1772) AC connector (North America/JP usage). |
| **TYPE2** | Type 2 (IEC 62196-2) AC connector; EU standard. |
| **CCS2** | Combined Charging System "Combo 2" (IEC 62196 based) enabling high-power DC on Type 2. |
| **CHADEMO** | CHAdeMO DC fast-charging standard. |
| **GB_T** | China's GB/T charging standard (AC & DC). |

#### Service types
| Code | What it means |
| :--- | :--- |
| **GREEN_ENERGY_CERTIFIED** | Flag that the site/session is supplied by verified green energy per catalog metadata (e.g., OCPI EnergyMix.is_green_energy). |
| **GO_EU** | Energy backed by EU Guarantees of Origin (GOs). |
| **REGO_UK** | Energy backed by the UK Renewable Energy Guarantees of Origin (REGO) scheme. |
| **I_REC** | Energy backed by I-REC certificates (international EACs). |
| **V2G_ENABLED** | Charger/site supports bidirectional power transfer (V2G), e.g., per ISO 15118-20 implementations. |
| **REMOTE_START_STOP** | Remote start/stop of sessions exposed via roaming interface (OCPI Commands module). |



## 11. User Stories

### 11.1. User Story – 1 - Walk-In to a charging station without reservation
This section covers a walk-in case where users discover the charger using third-party apps, word of mouth, or Beckn API, and then drive to the location, plug in their EV and charge their vehicle.

#### 11.1.1. Consumer User Journey
A sales manager, Arjun, drives an EV to client meetings. He's time-bound, cost-conscious, and prefers simple, scan-and-go experiences. Arjun arrives at a large dine-in restaurant for a one-hour client meeting. He notices a charging bay in the parking lot and decides to top up while he's inside.

**Discovery and Selection:**
Arjun opens his EV app, taps "Scan & Charge," and immediately scans the QR code on the charger unit. The app rapidly retrieves all critical charger details:
* Connector Type: CCS2
* Power Rating: 50 kW DC Fast Charger
* Live Status: Available
* Tariff: ₹18.00/kWh
* Active Offers: A Lunch-Hour Promo is highlighted, offering 10% off between 12:00 PM and 2:00 PM.

**Order and Authorization**
Arjun opts for a cost-based top-up to manage his budget effectively.
* **Session Selection:** Arjun selects a session limit of ₹450.00.
* **Review and Confirmation:** He reviews the session terms, which clearly state:
    * Rate: ₹18.00/kWh (with a 10% auto-applied discount expected during the session).
    * Idle Fee Window: 10 minutes grace period post-session completion to avoid surcharges.
    * Cancellation Rules: Free cancellation is allowed before physically plugging in and initiating charging.
* **Payment Authorization:** He confirms the order and chooses UPI for authorization. The app instantly processes an authorization hold of ₹450.00 on his account.
* **ID Generation:** The app instantly returns a unique Booking/Transaction ID for the confirmed session reservation.

**Fulfilment and Real-time Monitoring**
Arjun plugs the cable into his EV's charging port and initiates the session directly from the app before heading into his meeting.
* **Session Start:** The charging session begins, and since it is within the promotional time window, the Lunch-Hour Promo discount (10%) is automatically applied, setting the effective rate to ₹16.20/kWh.
* **Live Progress:** While Arjun is in his meeting, the app provides a live dashboard showing the session's progress:
    * Energy Delivered (kWh): Real-time consumption, increasing second-by-second.
    * Cost Consumed (₹): The monetary value spent, tracking towards the ₹450.00 limit.
    * Estimated Kilowatt-Hours Remaining: An estimation of how much more energy can be delivered within the cost limit.
* **Status Alerts:** Immediate notification if the session speed is derated or if charging is unexpectedly interrupted.

**Post-Fulfilment and Reconciliation**
The charging session will terminate when the ₹450.00 cost limit is reached, or if the vehicle's SOC reaches 100%, whichever occurs first.
* **Session Stop:** Upon reaching the cost limit, the charging session automatically ceases. A notification is sent to Arjun's phone reminding him to unplug his vehicle promptly.
* **Digital Summary:** Arjun immediately receives a digital invoice and a comprehensive session summary in his app, which details:
    * Total Energy Delivered (e.g., 27.78 kWh).
    * Discount Applied (10% on the total energy cost).
    * Final Billed Amount (₹450.00).
* **Automatic Reconciliation:** If the session is interrupted or stops before the full ₹450.00 is consumed (e.g., only ₹325.00 worth of energy was delivered), the system automatically reconciles the payment:
    * The system calculates the exact cost for the energy delivered (e.g., ₹325.00).
    * It initiates an automatic refund/adjustment (e.g., ₹125.00) from the initial ₹450.00 authorization hold back to Arjun's UPI source, ensuring he is billed only for the energy actually consumed.

#### 11.1.2. API Calls and Schema
*Note: The API calls and schema for walk-in charging are identical to the advance reservation use case with minor differences in timing and availability. Where sections reference Use Case 2, the same API structure, field definitions, and examples apply unless specifically noted otherwise.*

**11.1.2.1. action: discover**
* **Method:** POST
* **Use Cases:** Raghav scans QR code on charger using his BAP user app.
<details>
  <summary>Click to view the schema links</summary>
  
  - [Discovery by QR Schema](../Example-schemas/01_discover/discovery-by-QR.json)

</details>

**11.1.2.2. action: on_discover**
* **Method:** POST
* **Use Cases:** The app receives the charger's details (connector, power rating, live status, tariff, any active time-bound offer).

**11.1.2.3. action: select**
* **Method:** POST
* **Use Cases:** Raghav selects a service offering from the options he gets. He chooses a 100 INR top-up.

**11.1.2.4. action: on_select**
* **Method:** POST
* **Use Cases:** Raghav receives estimated quotations for the selected service.

**11.1.2.5. action: init**
* **Method:** POST
* **Use Cases:** Raghav provides his billing information.

**11.1.2.6. action: on_init**
* **Method:** POST
* **Use Cases:** Raghav receives the charging session terms (rate, idle fee window, cancellation rules, payment terms etc). He reviews the terms. He chooses UPI and authorizes payment.

**11.1.2.6.1. action: on_status**
* **Method:** POST
* **Use Cases:** Application then receives a notification on the completed status of the payment for the charging session.

**11.1.2.7. action: confirm**
* **Method:** POST
* **Use Cases:** Raghav confirms the order.

**11.1.2.8. action: on_confirm**
* **Method:** POST
* **Use Cases:** The application then returns a booking/transaction ID along with the other charging session details.

**11.1.2.8.1 action: status**
* **Method:** GET
* **Use Cases:** Raghav is asked to plug in the charger by the application.

**11.1.2.8.2 action: on_status**
* **Method:** POST
* **Use Cases:** The application recieves a notification that the gun is successfully connected to the vehicle.

**11.1.2.9. action: update (start charging)**
* **Method:** POST
* **Use Cases:** Raghav plugs in and starts the session from the app.

**11.1.2.10. action: on_update (start charging)**
* **Method:** POST
* **Use Cases:** Response for the charging session initiation.

**11.1.2.11. action: track (charging-session progress)**
* **Method:** POST
* **Use Cases:** Raghav requests to track the live status of the charging session. state of charge (how much charging has been done).

**11.1.2.12. action: on_track**
* **Method:** POST
* **Use Cases:** Raghav receives the state of charge (how much charging has been done) of the vehicle.

**11.1.2.13. async action: on_status**
* **Method:** POST
* **Use Cases:** Raghav receives a notification if there is any error during the charging session.

**11.1.2.14. action: on_update (stop-charging)**
* **Method:** POST
* **Use Cases:** Raghav initiates a stop charging request when his requirement is met. Note: In practice it is not necessary that an EV user initiates a charging session stop. Based on actual scenario, a charging session can be stopped by the CPO as well.

**11.1.2.15. async action: on_update (stop-charging)**
* **Method:** POST
* **Use Case:** At ~60 minutes (or upon the EV user request), the session stops (or notifies the EV user to unplug). He receives a digital invoice and session summary in-app. If anything went wrong (e.g., session interrupted, SOC reaches 100%, etc.), the app reconciles to bill only for energy delivered and issues any adjustment or refund automatically.

**11.1.2.16. action: rating**
* **Method:** POST
* **Use Cases:** Raghav provides rating for the charging session.

**11.1.2.17. action: on_rating**
* **Method:** POST
* **Use Cases:** Raghav receives an achievement after providing a rating.

**11.1.2.18. action: support**
* **Method:** POST
* **Use Cases:** Raghav reaches out for support.

**11.1.2.19. action: on_support**
* **Method:** POST
* **Use Cases:** Raghav receives a response to his support request.

### 11.2. User Story – 2 - Reservation of an EV charging time slot
This section covers advance reservation of a charging slot where users discover and book a charger before driving to the location.

#### 11.2.1. Consumer User Journey
Aisha is driving her electric vehicle along the highway when she notices that her battery level is getting low. Using an EV Charging application (BAP), Aisha discovers nearby charging stations that are compatible with her vehicle. The application retrieves available slots and charger specifications from the available provider's (BPP). Aisha selects a preferred charger and books a slot through the application via Beckn APIs to avoid waiting on arrival.

**Discovery and Selection:**
Aisha opens her EV Charging BAP (powered by a Beckn-enabled discovery network). She filters for:
* **Connector Type:** CCS2
* **Location:** DC Fast chargers within 5 km
* **Amenities:** Restaurants or cafes
* **Tariff Model:** Per kWh

The app queries multiple charging providers and returns options showing:
* **ETA:** Estimated time from current location
* **Real-time Availability:** Status of slots
* **Tariff:** Cost per kWh
* **Active Offers:** Lunch-hour discounts (e.g., promotional pricing)

She compares them and selects "EcoPower Highway Hub – Mandya Food Court".

**Order and Reservation:**
Aisha taps "Reserve Slot" for the 12:45 PM – 1:15 PM window.
* **Session Review:** The app displays the session terms:
    * **Tariff:** ₹18 / kWh
    * **Grace Period:** 10 min post-session
    * **Idle Fee Policy:** ₹2/min after grace
    * **Cancellation Rules:** Free up to 15 min before
    * **Payment Options:** Hold, Pre-pay, Post-pay
* **Payment and Confirmation:** She pays ₹500 via her credit card. Upon confirmation of payment receipt, the provider returns a unique Reservation ID.

**Fulfilment and Real-time Monitoring:**
On arrival at the station, Aisha initiates "Start Charging" on her application.
* **Authentication:** The backend matches the request to her Reservation ID and instructs her to plug in the charging gun.
* **Session Start:** Once connected, charging begins.
* **Live Telemetry:** While she enjoys lunch, the app provides live tracking:
    * **Energy Dispensed:** kWh delivered
    * **Elapsed Time:** Duration of the session
    * **Running Cost:** Current bill amount (₹)
    * **Completion Estimate:** Time remaining until full/target

If she arrives a few minutes late, the charger holds the slot until the grace period expires.

**Post-Fulfilment and Reconciliation:**
Charging auto-stops when the requested amount (₹500) is reached or when she manually ends the session.
* **Session Stop:** The system terminates the power flow.
* **Digital Summary:** The system issues a digital invoice and reimburces back the amount to her account.
* **Feedback:** The app prompts for quick feedback:
    * Amenity rating (1–5)
    * Overall experience (1–5)
    * Optional text comments

Satisfied, Aisha resumes her trip with time to spare.

#### 11.2.2. API Calls and Schema
> **Note:** The API calls and schema for reservation follow a similar flow to walk-in but include specific checks for availability and slot booking.*

**11.2.2.1. action: discover**
* **Method:** POST
* **Use Cases:** Aisha searches for chargers with specific filters (Location, Connector type and a time slot).

**11.2.2.2. action: on_discover**
* **Method:** POST
* **Use Cases:** The app receives a list of charging stations matching criteria, including ETA, availability, and active offers.

**11.2.2.3. action: select**
* **Method:** POST
* **Use Cases:** Aisha selects "EcoPower Highway Hub" and chooses a time slot (12:45–1:15 PM).

**11.2.2.4. action: on_select**
* **Method:** POST
* **Use Cases:** The app returns the specific terms for that slot (Tariff, Grace Period, Cancellation Rules).

**11.2.2.5. action: init**
* **Method:** POST
* **Use Cases:** Aisha provides her billing information (Credit Card details) for the ₹500 payment.

**11.2.2.6. action: on_init**
* **Method:** POST
* **Use Cases:** The app presents the final payment terms and awaits authorization.

**11.2.2.6.1. action: on_status**
* **Method:** POST
* **Use Cases:** Application receives a notification on the completed status of the payment for the reservation.

**11.2.2.7. action: confirm**
* **Method:** POST
* **Use Cases:** Aisha confirms the reservation order.

**11.2.2.8. action: on_confirm**
* **Method:** POST
* **Use Cases:** The application returns a unique Reservation ID confirming the slot is booked.

**11.2.2.9. action: status**
* **Method:** GET
* **Use Cases:** Upon arrival, the application checks the status of the reserved charger.

**11.2.2.10. action: on_status**
* **Method:** POST
* **Use Cases:** The application confirms the charger is ready and prompts Aisha to "Start Charging" in the app.

**11.2.2.11. action: update (start charging)**
* **Method:** POST
* **Use Cases:** Aisha taps "Start Charging" and the app instructs the backend to energize the gun.

**11.2.2.12. action: on_update (start charging)**
* **Method:** POST
* **Use Cases:** Response confirming the session has started successfully.

**11.2.2.13. action: track (charging-session progress)**
* **Method:** POST
* **Use Cases:** Aisha requests live telemetry (Energy dispensed, running cost) while at lunch.

**11.2.2.14. action: on_track**
* **Method:** POST
* **Use Cases:** The app receives real-time updates on the charging progress.

**11.2.2.15. async action: on_status**
* **Method:** POST
* **Use Cases:** Aisha receives a notification if there is any error or interruption during the session.

**11.2.2.16. action: on_update (stop-charging)**
* **Method:** POST
* **Use Cases:** The system stops charging automatically when the ₹500 limit is reached (or manually by Aisha).

**11.2.2.17. async action: on_update (stop-charging)**
* **Method:** POST
* **Use Case:** The session terminates. Aisha receives the digital invoice and updated wallet balance.

**11.2.2.18. action: rating**
* **Method:** POST
* **Use Cases:** Aisha provides a rating for the amenities and experience.

**11.2.2.19. action: on_rating**
* **Method:** POST
* **Use Cases:** Aisha receives an acknowledgement for her feedback.

**11.2.2.20. action: support**
* **Method:** POST
* **Use Cases:** Aisha reaches out for support if needed.

**11.2.2.21. action: on_support**
* **Method:** POST
* **Use Cases:** Aisha receives a response to her support request.
