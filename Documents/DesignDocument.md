
# Design

## Problem statement

**"We need a comprehensive apartment booking system that supports individual and team accommodations with role-based allocation and administrative management"** 

The customer requires an application to facilitate individual and team-based accommodation bookings while allowing administrators to manage and allocate accommodations efficiently. The system must support flexible date management, gender-based restrictions, role-based accommodation assignment (Manager → Flat, Lead → Room, Engineer → Bed), and complex team booking logic with individual member cancellation capabilities. The application must integrate with the existing SIM tool built using React, Express, Node.js, and PostgreSQL.

## Implementation/Design

### Purpose and Usage
The application allows users to book accommodations for themselves or teams by selecting cities, dates, and team members. Administrators can review requests, allocate specific accommodations based on role and availability, and approve or reject bookings. The system enforces business rules including maximum 2-week stays, advance booking windows, and gender-based accommodation restrictions.

### Database Design Approach
- **Hierarchical Accommodation Structure**: Cities → Apartments → Flats → Rooms → Beds
- **Flexible Booking System**: Support for both individual and team bookings with polymorphic resource allocation
- **Role-Based Access Control**: Three-tier user system (Manager, Lead, Project Engineer) with admin privileges
- **Comprehensive Audit Trail**: All actions logged with user attribution and timestamps
- **Gender-Based Restrictions**: Enum constraints ensuring accommodation compatibility

### Database Schema

#### Core Entities
```sql
-- User Management
companies (company_id, company_name, description, created_at, updated_at, is_active)
users (user_id, company_id, first_name, last_name, email, phone, role, created_at, updated_at, is_active)

-- Location Hierarchy  
cities (city_id, city_name, state, country, created_at, updated_at, is_active)
apartments (apartment_id, city_id, apartment_name, address, total_flats, description, created_at, updated_at, is_active)
flats (flat_id, apartment_id, flat_number, floor, flat_type, total_rooms, max_occupancy, gender_restriction, created_at, updated_at, is_active)
rooms (room_id, flat_id, room_number, room_type, total_beds, max_occupancy, gender_restriction, created_at, updated_at, is_active)
beds (bed_id, room_id, bed_number, bed_type, gender_restriction, created_at, updated_at, is_active)

-- Booking Management
booking_requests (request_id, requester_id, city_id, is_team_booking, check_in_date, check_out_date, request_status, admin_remarks, requested_at, processed_at, processed_by)
booking_members (member_id, request_id, user_id, check_in_date, check_out_date, member_status, allocated_resource_type, allocated_resource_id, created_at, updated_at)
allocation_history (allocation_id, member_id, resource_type, resource_id, allocated_by, allocated_at, deallocated_at, allocation_status)

-- System Management
notifications (notification_id, user_id, request_id, notification_type, subject, message, is_read, sent_at, read_at)
audit_logs (log_id, user_id, action, table_name, record_id, old_values, new_values, ip_address, user_agent, created_at)
```

### Entity Relationship Diagram Code
```
// Database ERD for Apartment Booking System
// Use this code in eraser.io

User Management Group [color: blue] {
  Companies [icon: building] {
    + company_id (PK)
    + company_name
    + description
    + created_at, updated_at
    + is_active
  }
  
  Users [icon: users] {
    + user_id (PK)
    + company_id (FK)
    + first_name, last_name
    + email, phone
    + role (Manager/Lead/Engineer)
    + created_at, updated_at
    + is_active
  }
}

Location Management Group [color: green] {
  Cities [icon: map] {
    + city_id (PK)
    + city_name
    + state, country
    + created_at, updated_at
    + is_active
  }
  
  Apartments [icon: building-2] {
    + apartment_id (PK)
    + city_id (FK)
    + apartment_name
    + address
    + total_flats
    + created_at, updated_at
    + is_active
  }
  
  Flats [icon: home] {
    + flat_id (PK)
    + apartment_id (FK)
    + flat_number
    + floor, flat_type
    + total_rooms
    + gender_restriction
    + created_at, updated_at
    + is_active
  }
  
  Rooms [icon: door] {
    + room_id (PK)
    + flat_id (FK)
    + room_number
    + room_type
    + total_beds
    + gender_restriction
    + created_at, updated_at
    + is_active
  }
  
  Beds [icon: bed] {
    + bed_id (PK)
    + room_id (FK)
    + bed_number
    + bed_type
    + gender_restriction
    + created_at, updated_at
    + is_active
  }
}

Booking Management Group [color: orange] {
  BookingRequests [icon: calendar] {
    + request_id (PK)
    + requester_id (FK)
    + city_id (FK)
    + is_team_booking
    + check_in_date, check_out_date
    + request_status
    + admin_remarks
    + requested_at, processed_at
    + processed_by (FK)
  }
  
  BookingMembers [icon: user-group] {
    + member_id (PK)
    + request_id (FK)
    + user_id (FK)
    + check_in_date, check_out_date
    + member_status
    + allocated_resource_type
    + allocated_resource_id
    + created_at, updated_at
  }
  
  AllocationHistory [icon: history] {
    + allocation_id (PK)
    + member_id (FK)
    + resource_type
    + resource_id
    + allocated_by (FK)
    + allocated_at, deallocated_at
    + allocation_status
  }
}

System Management Group [color: purple] {
  Notifications [icon: bell] {
    + notification_id (PK)
    + user_id (FK)
    + request_id (FK)
    + notification_type
    + subject, message
    + is_read
    + sent_at, read_at
  }
  
  AuditLogs [icon: shield] {
    + log_id (PK)
    + user_id (FK)
    + action
    + table_name
    + record_id
    + old_values, new_values
    + ip_address, user_agent
    + created_at
  }
}

// Relationships
Companies ||--o{ Users : "employs"
Cities ||--o{ Apartments : "contains"
Apartments ||--o{ Flats : "contains"
Flats ||--o{ Rooms : "contains"
Rooms ||--o{ Beds : "contains"
Users ||--o{ BookingRequests : "creates"
Cities ||--o{ BookingRequests : "for location"
BookingRequests ||--o{ BookingMembers : "contains"
Users ||--o{ BookingMembers : "member of"
BookingMembers ||--o{ AllocationHistory : "allocated to"
Users ||--o{ Notifications : "receives"
BookingRequests ||--o{ Notifications : "about"
Users ||--o{ AuditLogs : "performs actions"
```

### API Design Structure
```typescript
// REST API Endpoints (35 total)

// Authentication (3 endpoints)
POST   /api/auth/login
POST   /api/auth/logout  
GET    /api/auth/me

// User Management (2 endpoints)
GET    /api/users/company-members
GET    /api/users/{user_id}

// Location (2 endpoints)
GET    /api/cities
GET    /api/cities/{city_id}/apartments

// Booking (9 endpoints)
GET    /api/availability
GET    /api/availability/team
POST   /api/bookings
POST   /api/bookings/team
GET    /api/bookings/my-requests
GET    /api/bookings/{request_id}
PUT    /api/bookings/{request_id}/cancel
PUT    /api/bookings/cancel-member/{member_id}
PUT    /api/bookings/{request_id}/cancel-team

// Admin (15 endpoints)
GET    /api/admin/requests
GET    /api/admin/team-requests/{request_id}
POST   /api/admin/allocate
POST   /api/admin/allocate/member
PUT    /api/admin/approve/{request_id}
PUT    /api/admin/approve-team/{request_id}
PUT    /api/admin/reject/{request_id}
GET    /api/admin/occupancy
GET    /api/admin/booking-history
GET    /api/admin/export/bookings
GET    /api/admin/export/occupancy
POST   /api/admin/cities
POST   /api/admin/apartments
PUT    /api/admin/apartments/{apartment_id}
DELETE /api/admin/apartments/{apartment_id}

// Notifications (2 endpoints)
GET    /api/notifications
PUT    /api/notifications/{notification_id}/read

// System (2 endpoints)
GET    /api/health
GET    /api/version
```

### Individual Booking Sequence Diagram Code
```
// Individual Booking Workflow
// Use this code in eraser.io

participant User [icon: user]
participant Frontend [icon: react]
participant "Backend API" [icon: server]
participant Database [icon: database]
participant "Email Service" [icon: mail]
participant Admin [icon: user-shield]

User -> Frontend: Login request
Frontend -> "Backend API": POST /api/auth/login
"Backend API" -> Database: Validate credentials
Database -> "Backend API": User data
"Backend API" -> Frontend: Authentication token
Frontend -> User: Login success

User -> Frontend: Select city, dates
Frontend -> "Backend API": GET /api/availability
"Backend API" -> Database: Check availability
"Backend API" -> "Backend API": Validate business rules\n(2 weeks max, 1 day-1 month advance)
Database -> "Backend API": Availability data
"Backend API" -> Frontend: Availability response

User -> Frontend: Submit booking request
Frontend -> "Backend API": POST /api/bookings
"Backend API" -> Database: Create booking request
"Backend API" -> "Email Service": Send user notification
"Backend API" -> "Email Service": Send admin notification
"Email Service" -> User: Request confirmation
"Email Service" -> Admin: New request alert

Admin -> Frontend: Login to admin panel
Frontend -> "Backend API": GET /api/admin/requests
"Backend API" -> Database: Fetch pending requests

Admin -> Frontend: Allocate accommodation\n(Role-based: Manager→Flat, Lead→Room, Engineer→Bed)
Frontend -> "Backend API": POST /api/admin/allocate
"Backend API" -> Database: Update allocation

Admin -> Frontend: Approve request
Frontend -> "Backend API": PUT /api/admin/approve
"Backend API" -> Database: Update booking status
"Backend API" -> "Email Service": Send approval email
"Email Service" -> User: Booking approved!
```

### Team Booking Sequence Diagram Code
```
// Team Booking Workflow with Conflict Prevention
// Use this code in eraser.io

participant "Team Leader" [icon: user-star]
participant "Team Member A" [icon: user]
participant "Team Member B" [icon: user]
participant Frontend [icon: react]
participant "Backend API" [icon: server]
participant Database [icon: database]
participant "Email Service" [icon: mail]
participant Admin [icon: user-shield]

"Team Leader" -> Frontend: Login & select team booking
Frontend -> "Backend API": GET /api/users/company-members
"Backend API" -> Database: Fetch company users

"Team Leader" -> Frontend: Add Member A & Member B
Frontend -> "Backend API": POST /api/bookings/validate-members
"Backend API" -> Database: Validate all members exist

"Team Leader" -> Frontend: Set dates (common/individual)
Frontend -> "Backend API": GET /api/availability/team
"Backend API" -> Database: Check availability for all
"Backend API" -> "Backend API": Apply business rules

"Team Leader" -> Frontend: Submit team booking
Frontend -> "Backend API": POST /api/bookings/team
"Backend API" -> Database: Create parent request
"Backend API" -> Database: Create member records
"Backend API" -> "Email Service": Notify all members

"Email Service" -> "Team Leader": Team request created
"Email Service" -> "Team Member A": Added to team booking
"Email Service" -> "Team Member B": Added to team booking

Admin -> Frontend: Review team request
Frontend -> "Backend API": GET /api/admin/team-requests

Admin -> Frontend: Allocate for Member A
Frontend -> "Backend API": POST /api/admin/allocate/member
"Backend API" -> Database: Check conflicts & allocate
"Backend API" -> "Backend API": Update available resources\n(Bed allocation excludes room/flat)

Admin -> Frontend: Allocate for Member B
Frontend -> "Backend API": POST /api/admin/allocate/member  
"Backend API" -> Database: Prevent conflicts & allocate\n(Room with allocated bed unavailable)

Admin -> Frontend: Approve team request
Frontend -> "Backend API": PUT /api/admin/approve-team
"Backend API" -> Database: Update all member statuses
"Backend API" -> "Email Service": Send approval to all

"Email Service" -> "Team Leader": Team booking approved
"Email Service" -> "Team Member A": Your booking approved
"Email Service" -> "Team Member B": Your booking approved

note over "Team Member A": Individual Cancellation Scenario
"Team Member A" -> Frontend: Cancel individual booking
Frontend -> "Backend API": PUT /api/bookings/cancel-member
"Backend API" -> Database: Cancel member only\n(preserves team structure)
"Backend API" -> "Email Service": Notify cancellation
```

### System Architecture Diagram Code
```
// System Architecture - Apartment Booking Application
// Use this code in eraser.io

cloud "External Services" [color: purple] {
  service "Email Service" [icon: mail] {
    label: "SMTP/SendGrid\nNotifications"
  }
  
  service "File Storage" [icon: database] {
    label: "AWS S3\nDocument Storage"
  }
  
  service "SIM Tool API" [icon: link] {
    label: "Integration Endpoint\n(Future)"
  }
}

cluster "Presentation Layer" [color: blue] {
  app "React Frontend" [icon: react] {
    label: "User Portal\nResponsive UI"
  }
  
  app "Admin Dashboard" [icon: shield] {
    label: "Admin Interface\nManagement Portal"
  }
}

cluster "API Gateway" [color: lightgreen] {
  service "Nginx/ALB" [icon: network] {
    label: "Load Balancer\nSSL, Rate Limiting"
  }
}

cluster "Application Layer" [color: green] {
  service "Express.js API" [icon: server] {
    label: "REST API\nMain Backend"
  }
  
  service "Auth Service" [icon: key] {
    label: "JWT Authentication\nRole Management"
  }
  
  service "Booking Service" [icon: calendar] {
    label: "Individual/Team\nBooking Logic"
  }
  
  service "User Service" [icon: users] {
    label: "User Management\nCompany Operations"
  }
  
  service "Accommodation Service" [icon: building] {
    label: "Cities, Apartments\nResource Management"
  }
  
  service "Notification Service" [icon: bell] {
    label: "Email & In-app\nNotifications"
  }
}

cluster "Database Layer" [color: orange] {
  database "PostgreSQL Primary" [icon: database] {
    label: "Main Data Store\n12 Core Tables"
  }
  
  service "Connection Pool" [icon: layers] {
    label: "pg-pool\nConnection Management"
  }
}

cluster "Security & Monitoring" [color: red] {
  service "Audit Logging" [icon: shield] {
    label: "Winston Logger\nAction Tracking"
  }
  
  service "Error Monitoring" [icon: alert] {
    label: "Error Tracking\nPerformance Monitor"
  }
}

// Connections
"React Frontend" -> "Nginx/ALB": "HTTPS"
"Admin Dashboard" -> "Nginx/ALB": "HTTPS"
"Nginx/ALB" -> "Express.js API": "Load Balanced"
"Express.js API" -> "Auth Service": "Internal"
"Express.js API" -> "Booking Service": "Internal"
"Express.js API" -> "User Service": "Internal"
"Express.js API" -> "Accommodation Service": "Internal"
"Express.js API" -> "Notification Service": "Internal"
"Express.js API" -> "Connection Pool": "Database Connection"
"Connection Pool" -> "PostgreSQL Primary": "SQL Queries"
"Notification Service" -> "Email Service": "SMTP"
"Express.js API" -> "File Storage": "API Calls"
"Express.js API" -> "SIM Tool API": "Integration"
"Express.js API" -> "Audit Logging": "Action Logs"
"Express.js API" -> "Error Monitoring": "Error Tracking"
```

### Business Process Flow Diagram Code
```
// Business Process Workflows
// Use this code in eraser.io

flowchart "Apartment Booking System Workflows" {
  
  subgraph "User Authentication" [color: blue] {
    start1[User Access System] -> login_check{Already Logged In?}
    login_check -> dashboard1[Show Dashboard] : "Yes"
    login_check -> login_form[Show Login Form] : "No"
    login_form -> credentials[Enter Email/Password]
    credentials -> validate[Validate Credentials]
    validate -> auth_success{Authentication Success?}
    auth_success -> dashboard1 : "Yes"
    auth_success -> auth_error[Show Error Message] : "No"
  }
  
  subgraph "Individual Booking" [color: green] {
    select_city[Select City] -> select_dates[Select Check-in/out Dates]
    select_dates -> validate_dates{Dates Valid?}
    validate_dates -> date_rules[Check: 2 weeks max, 1 day-1 month advance]
    date_rules -> check_availability[Check Availability]
    check_availability -> available{Available?}
    available -> submit_request[Submit Booking Request] : "Yes"
    submit_request -> notify_user[Send Confirmation Email]
    notify_user -> notify_admin[Notify Admin]
    available -> unavailable_error[Show Unavailable Message] : "No"
    validate_dates -> date_error[Show Date Error] : "Invalid"
  }
  
  subgraph "Team Booking" [color: lightgreen] {
    select_team[Add Team Members] -> validate_members[Validate Company Members]
    validate_members -> members_valid{All Members Valid?}
    members_valid -> date_option{Common or Individual Dates?} : "Yes"
    date_option -> common_dates[Set Common Dates] : "Common"
    date_option -> individual_dates[Set Individual Dates] : "Individual"
    common_dates -> team_availability[Check Team Availability]
    individual_dates -> team_availability
    team_availability -> team_available{All Available?}
    team_available -> create_team_request[Create Team Request] : "Yes"
    create_team_request -> notify_team[Notify All Members]
    team_available -> team_unavailable[Show Conflicts] : "No"
    members_valid -> member_error[Show Member Error] : "No"
  }
  
  subgraph "Admin Approval" [color: orange] {
    review_request[Review Request] -> request_type{Individual or Team?}
    request_type -> individual_allocation[Allocate Resource (Role-based)] : "Individual"
    request_type -> team_allocation[Allocate Each Member] : "Team"
    team_allocation -> check_conflicts[Prevent Allocation Conflicts]
    individual_allocation -> approve_reject{Approve or Reject?}
    check_conflicts -> approve_reject
    approve_reject -> approve_request[Approve Request] : "Approve"
    approve_reject -> reject_request[Reject with Remarks] : "Reject"
    approve_request -> send_approval[Send Approval Email]
    reject_request -> send_rejection[Send Rejection Email]
  }
  
  subgraph "Cancellation Process" [color: red] {
    cancel_request[User Requests Cancellation] -> cancel_type{Individual or Team Member?}
    cancel_type -> individual_cancel[Cancel Individual Request] : "Individual"
    cancel_type -> team_member_cancel[Cancel Team Member] : "Team Member"
    individual_cancel -> check_permissions{Has Permission?}
    team_member_cancel -> check_permissions
    check_permissions -> update_status[Update Booking Status] : "Yes"
    update_status -> release_resources[Release Allocated Resources]
    release_resources -> send_cancel_notification[Send Cancellation Email]
    check_permissions -> permission_error[Permission Denied] : "No"
  }
  
  subgraph "Business Rules" [color: purple] {
    rule_check[Business Rules Validation] -> max_duration{≤ 2 weeks?}
    max_duration -> advance_window{1 day - 1 month advance?} : "Yes"
    advance_window -> gender_restriction{Gender Compatible?} : "Yes"
    gender_restriction -> role_allocation[Manager→Flat, Lead→Room, Engineer→Bed] : "Yes"
    role_allocation -> conflict_check[Check Resource Conflicts]
    conflict_check -> rules_passed[Validation Passed]
    max_duration -> rules_failed[Validation Failed] : "No"
    advance_window -> rules_failed : "No"
    gender_restriction -> rules_failed : "No"
  }
}

// Integration Points
note over dashboard1: "Integration with SIM Tool"
note over notify_user: "Email Service Integration"
note over review_request: "Audit Logging"
note over rule_check: "PostgreSQL Constraints"
```

### User Interaction Flow
The React application will display a dashboard where users can:
1. **Login/Authentication**: JWT-based login with role verification
2. **City & Date Selection**: Interactive calendar with business rule validation
3. **Individual Booking**: Simple booking form with availability checking
4. **Team Booking**: Multi-member selection with flexible date options
5. **Request Management**: View, track, and cancel booking requests
6. **Admin Functions**: Review, allocate, approve/reject requests (admin only)
7. **Notification Center**: View in-app notifications and email alerts
8. **Export Reports**: Generate CSV reports for booking and occupancy data

### CRUD Operations
- **Cities, Apartments, Flats, Rooms, Beds**: Full CRUD operations (Admin only)
- **Booking Requests**: Create, Read, Update status, soft delete (cancellation)
- **Team Members**: Add/remove from team bookings with individual cancellation
- **Allocations**: Create, track history, release resources
- **Users**: Read operations for company members, profile updates
- **Notifications**: Create, read, mark as read

### How Business Logic is Processed
- **Date Validation**: Frontend validation with backend enforcement of 2-week max and advance booking window
- **Availability Checking**: Real-time queries against allocation history and active bookings
- **Role-Based Allocation**: Automatic suggestions with admin override capabilities
- **Conflict Prevention**: Database constraints and application logic prevent double allocations
- **Team Coordination**: Complex logic handles individual member dates within team bookings
- **Gender Restrictions**: Database-level constraints ensure accommodation compatibility
- **Audit Trail**: Every action logged with user attribution and timestamps

### Non-Functional Requirements (NFRs)
- **Scalability**: Microservice-ready architecture supporting 100+ concurrent users
- **Performance**: < 500ms response time for 95% of API requests with database optimization
- **Security**: JWT authentication, role-based access control, comprehensive audit logging
- **Integration**: Seamless compatibility with existing SIM tool (React/Express/Node/PostgreSQL)
- **Reliability**: High availability design with error monitoring and automated health checks
- **Maintainability**: Clean architecture with separation of concerns and comprehensive documentation
- **Data Integrity**: Referential integrity constraints with soft delete patterns for data preservation

### Alternative Design Considerations

#### Monolithic vs Microservices
- **Current Approach**: Modular monolithic design with service layer separation
- **Future Migration**: Architecture supports breaking into microservices as needed
- **Trade-offs**: Simplified deployment vs. independent scaling capabilities

#### Database Design Alternatives
- **NoSQL Option**: Considered MongoDB for flexibility but chose PostgreSQL for ACID compliance
- **Flat Structure**: Evaluated simplified table design but chose hierarchical for business logic alignment
- **Event Sourcing**: Future consideration for complex booking state management

This design ensures optimal performance, maintainability, and scalability while meeting all functional requirements for the apartment booking system.
