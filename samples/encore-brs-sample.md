---
title: "Apollo Broadcast — HZD Cargo Should Flow Auto from Master to Quotation"
permalink: /samples/encore-brs-sample/
layout: single
author_profile: false
toc: true
---

| | |
|---|---|
| **Created on** | 30-June-2015 |
| **Version** | 1.0 |
| **Document No.** | BRO000030 |
| **Broadcast Audience** | Customer Care, Documentation and Accounts Departments |
| **Objective** | Hazardous Cargo must auto-flow from Master to Quotation |

## Change Summary — Ticket #769770

The following enhancements are made in the Apollo application:

- Develop a standard template for BL clauses such that the BL gets populated based on the Destination and Type of Cargo.
- When the user selects the POD (Port of Destination) and FPD (Final Place of Destination) as Nhava Sheva, the Stamp Duty gets reflected in the BL.
- The Type of Cargo is made mandatory at the time of Booking.

## Description

In the earlier business functionality of the Apollo application, all available clauses were loaded into the BL, and the user selected only the relevant clauses for that particular BL. The BL clauses did not flow automatically from Master into the Quotation.

The enhanced Apollo application has a standard template of BL (Bill of Lading) clauses, where the clauses can be loaded in the BL according to the Destination and the Type of Cargo, flowing from Master to the Quotation.

For example:
- If the user selects **One Door Open** container as the Type of Cargo, only the clauses pertaining to the selected type will be loaded in the BL.
- Similarly, if the user selects **Hazardous** cargo as the Type of Cargo, only the Hazardous clauses will flow from Master into the Quotation.

The enhancement also alters the Type of Cargo field to be mandatory in the Container Type Master.

## Impact

The changes in the application impact the following screens:

- **Liner Activity Export Booking:** All new bookings will consider the Type of Cargo as a mandatory field, and the system pulls only the relevant clauses in the BL.
- **Liner Export Job:** The system displays only the relevant clauses in the BL.
- **Container Type Master:** The Type of Cargo field is mandatory.

## Benefit

- The clauses auto-flow from Master to Quotation instead of the user pulling the clauses manually.
- Only the relevant clauses are populated in each BL.
- Details related to Cargo are recorded promptly in the Booking, as the Type of Cargo field is made mandatory.
