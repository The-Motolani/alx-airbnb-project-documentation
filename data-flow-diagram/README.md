# Design a Data Flow Diagram for Your Features and Functionalities

## ✅ What is a DFD?

A DFD is a visual representation of how data flows through a system—how it’s received (inputs), processed (processes), stored (data stores), and output.

- Key components include:

> - External Entities – actors or systems outside the main system boundary.
> - Processes – actions or transformations applied to data.
> - Data Stores – where data rests (e.g., database tables).
> - Data Flows – arrows showing movement of data between entities, processes, and stores.

## 🔍 DFD for Airbnb-Clone Backend

For your system (covering sign-up, listings, booking, payments etc.), you should create a DFD that includes:

- External Entities: Guest/User, Host, Admin, Payment Gateway.

### - **Processes:**

> - User Authentication & Profile Management
> - Listing Creation & Management
> - Search & Filter Listings
> - Booking Request & Reservation
> - Payment Processing
> - Review Submission

- Data Stores: Users table, Properties table, Bookings table, Payments table, Reviews table.

- Data Flows: e.g., Guest → “Search Listings” → Properties data store → Guest view; Guest → “Create Booking” → Bookings store; Booking → “Process Payment” → Payments store; etc.

## 🛠️ Instructions

1. Open Draw.io (or Diagrams.net) and create a new diagram.

2. Set up a Context diagram (Level 0 DFD) showing the system boundary and major external entities interacting with your system.

3. Then create a Level 1 DFD: break into major processes and show data stores and flows.

4. Ensure you name each process, flow, and data store clearly (e.g., “Validate Booking Dates”, “Store Payment Record”).

5. Export the diagram as PNG with filename: `data-flow.png`.

6. Save the file to directory: `data-flow-diagram/` in your GitHub repository `alx-airbnb-project-documentation`.

## 📂 Repository Structure Reference

alx-airbnb-project-documentation/
├── …
├── data-flow-diagram/
│   └── data-flow.png
└── …

## 🧾 Deliverable

A clean, well-labelled DFD (PNG) that covers the core backend operations and data movements for the Airbnb Clone system.
