# Eventra ER Diagram

```mermaid
erDiagram
  USERS ||--o{ EVENTS : organizes
  USERS ||--o{ RESERVATIONS : places
  EVENTS ||--|{ TICKET_TIERS : offers
  TICKET_TIERS ||--o{ RESERVATIONS : held_as
  RESERVATIONS ||--o| ORDERS : converts_to
  USERS ||--o{ ORDERS : purchases
  ORDERS ||--o{ PAYMENT_TRANSACTIONS : attempts
  RESERVATIONS ||--o{ PAYMENT_TRANSACTIONS : records
  USERS { int id PK string name string email string role }
  EVENTS { int id PK int organizer_id FK string title string venue datetime event_date datetime sales_cutoff }
  TICKET_TIERS { int id PK int event_id FK string name decimal price int total_quantity int available_quantity int sold_quantity }
  RESERVATIONS { uuid id PK int user_id FK int tier_id FK int quantity string status datetime expires_at }
  ORDERS { int id PK uuid reservation_id FK int user_id FK decimal total string status }
  PAYMENT_TRANSACTIONS { int id PK int order_id FK uuid reservation_id FK decimal amount string status string provider_reference }
```

`available_quantity` is decremented inside a PostgreSQL transaction after locking the ticket-tier row. Reservations transition through `HELD`, `PAYMENT_PENDING`, and one terminal state: `CONFIRMED`, `EXPIRED`, or `FAILED`.
