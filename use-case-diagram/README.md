# Design the Use Case Diagram of the Features and Functionalities

## 🎯 Key Actors

- Guest (User role: guest) — searches for listings, books, pays, reviews.
- Host (User role: host) — lists properties, manages listings, views bookings, receives payments.
- Admin — manages users, properties, bookings, payments, reviews system-wide.
- Payment Gateway / External Service — represented if you wish to show external system involvement (optional actor).

## ✅ Core Use Cases (sample list)

- Register / Login
- Update Profile
- Search Properties
- View Property Details
- Create Listing
- Edit/Delete Listing
- Book Property
- Cancel Booking
- Make Payment
- Leave Review
- Manage Users (Admin)
- Manage Listings (Admin)
- Manage Bookings & Payments (Admin)

## 📐 Diagram Instructions

- Draw a system boundary (rectangle) labelled “Airbnb Clone Backend”.
- Place actors (stick figures) outside the boundary.
- Inside the boundary, draw oval shapes for each use case and label accordingly.
- Connect actors to relevant use cases using straight lines.
- Use «include» or «extend» relationships where appropriate. For example:

> - Booking → «include» Make Payment
> - Cancel Booking → «extend» Book Property

- Ensure key actors and their interactions with the system are covered.
