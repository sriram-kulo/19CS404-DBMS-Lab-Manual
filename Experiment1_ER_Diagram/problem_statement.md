# ER Diagram Workshop – Submission

## Objective
To understand and apply ER modeling concepts by creating ER diagrams for real-world applications.

## Purpose
Gain hands-on experience in designing ER diagrams that represent database structure including entities, relationships, attributes, and constraints.

---

# Scenario A: City Fitness Club Management

**Business Context:**  
FlexiFit Gym wants a database to manage its members, trainers, and fitness programs.

**Requirements:**  
- Members register with name, membership type, and start date.  
- Each member can join multiple programs (Yoga, Zumba, Weight Training).  
- Trainers assigned to programs; a program may have multiple trainers.  
- Members may book personal training sessions with trainers.  
- Attendance recorded for each session.  
- Payments tracked for memberships and sessions.

### ER Diagram:
![ER Diagram](er_diagram_flexifit.png)

### Entities and Attributes

| Entity | Attributes (PK, FK) | Notes |
|--------|--------------------|-------|
| Member | **MemberID (PK)**, Name, MembershipType, StartDate | Registered gym member |
| Program | **ProgramID (PK)**, ProgramName, Fee | Yoga, Zumba, Weight Training, etc. |
| Trainer | **TrainerID (PK)**, Name, Specialization, Phone | Staff who teach programs and give personal training |
| Session | **SessionID (PK)**, MemberID (FK), TrainerID (FK), SessionDate, Time | A personal training session booked by a member |
| Attendance | **AttendanceID (PK)**, SessionID (FK), Status, CheckInTime | Records whether the member attended a session |
| Payment | **PaymentID (PK)**, MemberID (FK), SessionID (FK, nullable), Amount, PaymentType, PaymentDate | PaymentType = Membership or Session; SessionID is empty for membership payments |
| Enrollment *(junction)* | **MemberID (PK, FK)**, **ProgramID (PK, FK)**, JoinDate | Resolves Member–Program M:N |
| Program_Trainer *(junction)* | **ProgramID (PK, FK)**, **TrainerID (PK, FK)** | Resolves Trainer–Program M:N |

### Relationships and Constraints

| Relationship | Cardinality | Participation | Notes |
|--------------|------------|---------------|-------|
| Member joins Program | M:N | Member: optional, Program: optional | Implemented through Enrollment |
| Trainer is assigned to Program | M:N | Trainer: total, Program: total | Every program has at least one trainer; implemented through Program_Trainer |
| Member books Session | 1:M | Member: optional, Session: total | A session must belong to one member |
| Trainer conducts Session | 1:M | Trainer: optional, Session: total | A session must have one trainer |
| Session has Attendance | 1:M | Session: optional, Attendance: total | Attendance cannot exist without a session |
| Member makes Payment | 1:M | Member: optional, Payment: total | Every payment is made by one member |
| Payment pays for Session | M:1 | Payment: optional, Session: optional | Only session payments link to a session |

### Assumptions
- A member has exactly one membership type at a time (e.g., Monthly, Quarterly, Annual).
- A personal training session is one member with one trainer.
- Membership payments are linked only to the member; session payments are linked to both member and session.

---

# Scenario B: City Library Event & Book Lending System

**Business Context:**  
The Central Library wants to manage book lending and cultural events.

**Requirements:**  
- Members borrow books, with loan and return dates tracked.  
- Each book has title, author, and category.  
- Library organizes events; members can register.  
- Each event has one or more speakers/authors.  
- Rooms are booked for events and study.  
- Overdue fines apply for late returns.

### ER Diagram:
![ER Diagram](er_diagram_library.png)

### Entities and Attributes

| Entity | Attributes (PK, FK) | Notes |
|--------|--------------------|-------|
| Member | **MemberID (PK)**, Name, Email, Phone, JoinDate | Library member |
| Book | **BookID (PK)**, Title, Author, Category | Each physical copy has its own BookID |
| Loan | **LoanID (PK)**, MemberID (FK), BookID (FK), LoanDate, DueDate, ReturnDate | Resolves Member–Book M:N; ReturnDate is empty until returned |
| Fine | **FineID (PK)**, LoanID (FK), Amount, PaidStatus | Created only for late returns |
| Event | **EventID (PK)**, Title, EventDate, StartTime, Description | Cultural events organized by the library |
| Speaker | **SpeakerID (PK)**, Name, Bio | Speaker or author at events |
| Event_Speaker *(junction)* | **EventID (PK, FK)**, **SpeakerID (PK, FK)** | Resolves Event–Speaker M:N |
| Event_Registration *(junction)* | **MemberID (PK, FK)**, **EventID (PK, FK)**, RegistrationDate | Resolves Member–Event M:N |
| Room | **RoomID (PK)**, RoomName, Capacity | Rooms available for events and study |
| Room_Booking | **BookingID (PK)**, RoomID (FK), MemberID (FK), EventID (FK, nullable), BookingDate, StartTime, EndTime, Purpose | Purpose = Event or Study; EventID is empty for study bookings |

### Relationships and Constraints

| Relationship | Cardinality | Participation | Notes |
|--------------|------------|---------------|-------|
| Member borrows Book | M:N | Member: optional, Book: optional | Implemented through Loan |
| Loan incurs Fine | 1:0..1 | Loan: optional, Fine: total | Fine exists only if ReturnDate is after DueDate |
| Member registers for Event | M:N | Member: optional, Event: optional | Implemented through Event_Registration |
| Event features Speaker | M:N | Event: total, Speaker: total | Each event has at least one speaker |
| Room has Room_Booking | 1:M | Room: optional, Room_Booking: total | A booking is for exactly one room |
| Member makes Room_Booking | 1:M | Member: optional, Room_Booking: total | Study or event bookings are made by a member |
| Event uses Room_Booking | 1:M | Event: optional, Room_Booking: optional | Study bookings have no event |

### Assumptions
- Each book copy is tracked separately, so a copy can be on loan to only one member at a time.
- Fine amount is calculated as a per-day rate multiplied by days overdue.
- Rooms cannot be double-booked for overlapping time slots.

---

# Scenario C: Restaurant Table Reservation & Ordering

**Business Context:**  
A popular restaurant wants to manage reservations, orders, and billing.

**Requirements:**  
- Customers can reserve tables or walk in.  
- Each reservation includes date, time, and number of guests.  
- Customers place food orders linked to reservations.  
- Each order contains multiple dishes; dishes belong to categories (starter, main, dessert).  
- Bills generated per reservation, including food and service charges.  
- Waiters assigned to serve reservations.

### ER Diagram:
![ER Diagram](er_diagram_restaurant.png)

### Entities and Attributes

| Entity | Attributes (PK, FK) | Notes |
|--------|--------------------|-------|
| Customer | **CustomerID (PK)**, Name, Phone | Person who reserves or walks in |
| Table | **TableID (PK)**, TableNumber, Capacity | Physical restaurant table |
| Reservation | **ReservationID (PK)**, CustomerID (FK), TableID (FK), ReservationDate, ReservationTime, NumGuests, Type | Type = Reserved or Walk-in; walk-ins are recorded as same-day reservations |
| Waiter | **WaiterID (PK)**, Name, Phone | Serving staff |
| Reservation_Waiter *(junction)* | **ReservationID (PK, FK)**, **WaiterID (PK, FK)** | Resolves Waiter–Reservation M:N |
| Order | **OrderID (PK)**, ReservationID (FK), OrderTime | Food order linked to a reservation |
| Category | **CategoryID (PK)**, CategoryName | Starter, Main, Dessert |
| Dish | **DishID (PK)**, CategoryID (FK), DishName, Price | Menu item |
| Order_Item *(junction)* | **OrderID (PK, FK)**, **DishID (PK, FK)**, Quantity | Resolves Order–Dish M:N |
| Bill | **BillID (PK)**, ReservationID (FK), FoodTotal, ServiceCharge, TotalAmount, BillDate | One bill per reservation |

### Relationships and Constraints

| Relationship | Cardinality | Participation | Notes |
|--------------|------------|---------------|-------|
| Customer makes Reservation | 1:M | Customer: optional, Reservation: total | Every reservation belongs to one customer |
| Reservation reserves Table | M:1 | Reservation: total, Table: optional | A table can be used by many reservations over time |
| Waiter serves Reservation | M:N | Waiter: optional, Reservation: total | Implemented through Reservation_Waiter; each reservation has at least one waiter |
| Reservation has Order | 1:M | Reservation: optional, Order: total | A guest may place several orders during one visit |
| Order contains Dish | M:N | Order: total, Dish: optional | Implemented through Order_Item with Quantity |
| Category classifies Dish | 1:M | Category: optional, Dish: total | Every dish belongs to exactly one category |
| Reservation generates Bill | 1:1 | Reservation: optional, Bill: total | One bill per reservation covering food and service charges |

### Assumptions
- Walk-in customers are recorded as reservations with Type = Walk-in so that orders and bills work the same way.
- TotalAmount = FoodTotal + ServiceCharge, and FoodTotal is the sum of Quantity × Price over the reservation's orders.
- A table cannot be assigned to two reservations at overlapping times.

---

## Instructions for Students

1. Complete **all three scenarios** (A, B, C).  
2. Identify entities, relationships, and attributes for each.  
3. Draw ER diagrams using **draw.io / diagrams.net** or hand-drawn & scanned.  
4. Fill in all tables and assumptions for each scenario.  
5. Export the completed Markdown (with diagrams) as **a single PDF**
