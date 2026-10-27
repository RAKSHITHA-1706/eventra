EVENTRA – MULTI-TENANT EVENT & TICKET RESERVATION SYSTEM

1. PROJECT OVERVIEW

Eventra is a web-based event management and ticket reservation platform that allows multiple organizers to create events, manage ticket categories, and monitor ticket sales. Attendees can explore events, reserve tickets, and complete checkout through a simulated payment system.

The main objective is to provide a reliable ticket booking experience while preventing double-booking when multiple users attempt to reserve tickets simultaneously.

2. OBJECTIVES

- Provide role-based access for Admins, Organizers, and Attendees.
- Allow organizers to create and manage events.
- Support multiple ticket categories, prices, and quantities.
- Implement temporary ticket reservations with a 10-minute expiry period.
- Prevent overselling through database transactions and concurrency control.
- Simulate asynchronous payment approval and rejection.
- Automatically release tickets from expired reservations.
- Provide analytics for revenue, ticket sales, and booking conversion rates.
- Improve performance through caching and background jobs.

3. TECHNOLOGY STACK

Frontend:
- React.js – User interface and dashboards.
- Vite – Frontend development and build tooling.
- CSS – Responsive layouts and visual styling.
- Recharts – Analytics charts.

Backend:
- Node.js – Server-side runtime.
- Express.js – REST API development.
- JavaScript – Application logic.
- JWT – Authentication and protected API access.
- bcryptjs – Password hashing.

Database and Processing:
- PostgreSQL – Stores users, events, ticket tiers, reservations, orders, and payment transactions.
- Redis – Caches frequently accessed event details.
- BullMQ – Handles background jobs and reservation expiry.
- PostgreSQL Transactions – Maintains consistent ticket inventory during concurrent bookings.

Testing:
- Postman – API testing and validation of edge cases.

4. USER ROLES

ADMIN:
- Manages platform users and administrative operations.
- Monitors platform activity.
- Accesses authorized administrative analytics.

ORGANIZER:
- Creates and manages events.
- Defines ticket categories, prices, quantities, and sales deadlines.
- Monitors ticket sales and revenue.
- Views event-specific analytics.

ATTENDEE:
- Browses available events.
- Selects ticket categories and quantities.
- Reserves tickets temporarily.
- Completes simulated payments.
- Views reservation and order details.

5. KEY FEATURES

EVENT MANAGEMENT:
Organizers can create events by entering event details, venue, date, ticket categories, prices, quantities, and sales cut-off dates. Each organizer can manage their own events through role-based access control.

TEMPORARY TICKET RESERVATION:
Attendees can reserve tickets for up to 10 minutes while completing checkout. During this period, the reserved inventory is unavailable to other attendees. If the reservation expires without successful completion, the tickets become available again.

CONCURRENCY CONTROL:
The system uses PostgreSQL transactions and row-level locking to manage simultaneous booking requests. Before creating a reservation, the backend checks ticket availability and updates inventory within a transaction. This prevents multiple users from reserving more tickets than are available.

SIMULATED PAYMENT PROCESSING:
A mock payment gateway simulates asynchronous payment approval and rejection. Successful payments lead to order confirmation, while failed payments trigger appropriate reservation handling.

AUTOMATIC RESERVATION EXPIRY:
BullMQ schedules background jobs to process expired ticket holds. Before releasing tickets, the worker checks the current reservation status to ensure that confirmed bookings are not accidentally released.

ANALYTICS DASHBOARD:
Organizers can monitor total tickets sold, remaining inventory, revenue generated, booking conversion rates, and ticket sales trends through visual charts.

REDIS CACHING:
Redis caches frequently accessed event details to reduce repeated database queries and improve response times. PostgreSQL remains the primary source of truth for ticket inventory and reservation status.

6. DATABASE DESIGN

USERS:
Stores account details, roles, and authentication information.

EVENTS:
Stores event information and organizer ownership.

TICKET TIERS:
Stores ticket categories, prices, and inventory quantities.

RESERVATIONS:
Tracks temporary holds, quantities, expiry timestamps, and reservation status.

ORDERS:
Stores purchase details and order status.

ORDER ITEMS:
Associates ticket categories and quantities with individual orders.

PAYMENT TRANSACTIONS:
Records payment attempts, amounts, and transaction statuses.

7. SYSTEM WORKFLOW

1. An organizer creates an event and defines ticket categories.
2. An attendee browses events and selects tickets.
3. The backend checks availability and creates a temporary reservation using a database transaction.
4. The reservation remains active for up to 10 minutes.
5. The attendee proceeds to the simulated payment gateway.
6. If payment succeeds, the order is confirmed.
7. If payment fails or the reservation expires, the system releases the inventory when appropriate.
8. Organizers can view updated sales information through the analytics dashboard.

8. API DESIGN

POST /api/auth/login
Authenticates a user.

GET /api/events
Retrieves available events.

POST /api/events
Creates an event for an authorized user.

POST /api/reservations
Creates a temporary ticket reservation.

POST /api/reservations/:id/pay
Initiates simulated payment processing.

GET /api/reservations/:id
Retrieves reservation details and status.

GET /api/analytics
Retrieves analytics for authorized users.

GET /api/health
Checks backend service health.

9. TESTING AND VALIDATION

Postman is used to test API endpoints and validate different booking scenarios.

Important test cases include:
- Valid and invalid authentication attempts.
- Unauthorized access to protected routes.
- Successful ticket reservations.
- Multiple users attempting to reserve the same remaining tickets.
- Requests exceeding available inventory.
- Successful and rejected payments.
- Expired reservation handling.
- Verification that confirmed tickets are not released by delayed background jobs.
- Database and Redis connectivity failures.

These tests help validate the reliability, security, and consistency of the reservation process.

10. DEPLOYMENT

The frontend can be deployed using Vercel or another static hosting platform. The backend and background worker can be hosted on a service that supports persistent Node.js processes. Managed PostgreSQL and Redis services can be used for production data storage and background processing.

Deployment requires configuring environment variables, database connections, authentication secrets, API URLs, and frontend access settings. The application should be tested in the deployed environment before its public URL is submitted.

11. FUTURE ENHANCEMENTS

- Integration with real payment gateways.
- Email confirmations and booking notifications.
- QR-code-based digital tickets.
- Interactive seat selection.
- Event recommendations based on attendee preferences.
- Advanced sales reports and organizer analytics.
- Audit logs, rate limiting, and enhanced security monitoring.
- Automated integration and load testing.

12. CONCLUSION

Eventra provides a foundation for a multi-tenant event management and ticket reservation platform. By combining role-based access control, temporary reservations, database transactions, Redis caching, background jobs, simulated payments, and analytics, the system addresses key challenges in online ticket booking.

Its primary focus is to ensure reliable ticket allocation under concurrent requests while providing organizers and attendees with a simple and efficient event management and booking experience.
