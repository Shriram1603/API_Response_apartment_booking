## 1. MANAGEMENT APIs

### Cities CRUD

**GET /api/cities**

```json
// Response
{
  "success": true,
  "data": [
    {
      "id": "CITY_001",
      "name": "Coimbatore",
      "createdAt": "2025-01-20T10:00:00Z",
      "updatedAt": "2025-01-20T10:00:00Z"
    }
  ]
}
```

**POST /api/cities**

```json
// Request
{
  "name": "Chennai"
}

// Response
{
  "success": true,
  "data": {
    "id": "CITY_002",
    "name": "Chennai",
    "createdAt": "2025-01-20T10:00:00Z",
    "updatedAt": "2025-01-20T10:00:00Z"
  }
}
```

**PUT /api/cities/:cityId**

```json
// Request
{
  "name": "Updated City Name"
}

// Response
{
  "success": true,
  "data": {
    "id": "CITY_001",
    "name": "Updated City Name",
    "createdAt": "2025-01-20T10:00:00Z",
    "updatedAt": "2025-01-20T12:00:00Z"
  }
}
```

**DELETE /api/cities/:cityId**

```json
// Response
{
  "success": true,
  "message": "City and all related data deleted successfully"
}
```

### Apartments CRUD

**GET /api/apartments?cityId=CITY_001**

```json
// Response
{
  "success": true,
  "data": [
    {
      "id": "APT_001",
      "name": "Pricol",
      "cityId": "CITY_001",
      "cityName": "Coimbatore",
      "googleMapLink": "https://maps.google.com/pricol",
      "createdAt": "2025-01-20T10:00:00Z",
      "updatedAt": "2025-01-20T10:00:00Z"
    }
  ]
}
```

**POST /api/apartments**

```json
// Request
{
  "name": "Sreevatsa Srilakshmi",
  "cityId": "CITY_001",
  "googleMapLink": "https://maps.google.com/sreevatsa"
}

// Response
{
  "success": true,
  "data": {
    "id": "APT_002",
    "name": "Sreevatsa Srilakshmi",
    "cityId": "CITY_001",
    "googleMapLink": "https://maps.google.com/sreevatsa",
    "createdAt": "2025-01-20T10:00:00Z",
    "updatedAt": "2025-01-20T10:00:00Z"
  }
}
```

**PUT /api/apartments/:apartmentId**

```json
// Request
{
  "name": "Updated Apartment Name",
  "googleMapLink": "https://maps.google.com/updated"
}

// Response
{
  "success": true,
  "data": {
    "id": "APT_001",
    "name": "Updated Apartment Name",
    "cityId": "CITY_001",
    "googleMapLink": "https://maps.google.com/updated",
    "createdAt": "2025-01-20T10:00:00Z",
    "updatedAt": "2025-01-20T12:00:00Z"
  }
}
```

**DELETE /api/apartments/:apartmentId**

```json
// Response
{
  "success": true,
  "message": "Apartment and all related data deleted successfully"
}
```

### Flats CRUD

**GET /api/flats?apartmentId=APT_001**

```json
// Response
{
  "success": true,
  "data": [
    {
      "id": "FLAT_001",
      "name": "C4",
      "apartmentId": "APT_001",
      "apartmentName": "Pricol",
      "rooms": [
        {
          "id": "ROOM_001",
          "name": "R1",
          "beds": 3
        },
        {
          "id": "ROOM_002",
          "name": "R2",
          "beds": 2
        }
      ],
      "totalRooms": 2,
      "totalBeds": 5,
      "createdAt": "2025-01-20T10:00:00Z",
      "updatedAt": "2025-01-20T10:00:00Z"
    }
  ]
}
```

**POST /api/flats**

```json
// Request
{
  "name": "C6",
  "apartmentId": "APT_001",
  "rooms": [
    {
      "name": "R1",
      "beds": 2
    },
    {
      "name": "R2",
      "beds": 3
    }
  ]
}

// Response
{
  "success": true,
  "data": {
    "id": "FLAT_002",
    "name": "C6",
    "apartmentId": "APT_001",
    "rooms": [
      {
        "id": "ROOM_003",
        "name": "R1",
        "beds": 2
      },
      {
        "id": "ROOM_004",
        "name": "R2",
        "beds": 3
      }
    ],
    "createdAt": "2025-01-20T10:00:00Z",
    "updatedAt": "2025-01-20T10:00:00Z"
  }
}
```

**PUT /api/flats/:flatId**

```json
// Request
{
  "name": "Updated Flat Name"
}

// Response
{
  "success": true,
  "data": {
    "id": "FLAT_001",
    "name": "Updated Flat Name",
    "apartmentId": "APT_001",
    "updatedAt": "2025-01-20T12:00:00Z"
  }
}
```

**DELETE /api/flats/:flatId**

```json
// Response
{
  "success": true,
  "message": "Flat and all related data deleted successfully"
}
```

### Get Full Hierarchy

**GET /api/accommodation/hierarchy**

```json
// Response
{
  "success": true,
  "data": {
    "cities": [
      {
        "id": "CITY_001",
        "name": "Coimbatore"
      }
    ],
    "apartments": [
      {
        "id": "APT_001",
        "name": "Pricol",
        "cityId": "CITY_001",
        "googleMapLink": "https://maps.google.com/pricol"
      }
    ],
    "flats": [
      {
        "id": "FLAT_001",
        "name": "C4",
        "apartmentId": "APT_001",
        "rooms": [
          {
            "id": "ROOM_001",
            "name": "R1",
            "beds": 3
          }
        ]
      }
    ],
    "rooms": [
      {
        "id": "ROOM_001",
        "name": "R1",
        "flatId": "FLAT_001",
        "beds": 3
      }
    ],
    "beds": [
      {
        "id": "BED_001",
        "name": "B1",
        "roomId": "ROOM_001",
        "status": "available",
        "occupantId": null,
        "occupantName": null
      }
    ]
  }
}
```

## 2. BOOKING APIs

### Get Available Cities

**GET /api/booking/cities**

```json
// Response
{
  "success": true,
  "data": [
    {
      "id": "CITY_001",
      "name": "Coimbatore"
    },
    {
      "id": "CITY_002",
      "name": "Chennai"
    }
  ]
}
```

### Submit Accommodation Request

**POST /api/booking/requests**

```json
// Request
{
  "userId": "USER_001",
  "user": {
    "id": "USER_001",
    "name": "John Doe",
    "email": "john.doe@company.com",
    "role": "Manager",
    "gender": "Male"
  },
  "city": "Coimbatore",
  "dates": {
    "from": "2025-01-25",
    "to": "2025-01-30"
  },
  "checkInTime": "08:00",
  "checkOutTime": "10:00",
  "bookingType": "individual", // or "team"
  "bookingFor": "john.doe@company.com", // for individual
  "teamMembers": [], // for team: ["jane.smith@company.com", "mike.johnson@company.com"]
  "remarks": "Special requirements if any"
}

// Response
{
  "success": true,
  "data": {
    "id": "REQ_001",
    "userId": "USER_001",
    "city": "Coimbatore",
    "dates": {
      "from": "2025-01-25",
      "to": "2025-01-30"
    },
    "checkInTime": "08:00",
    "checkOutTime": "10:00",
    "bookingType": "individual",
    "status": "pending",
    "createdAt": "2025-01-20T10:00:00Z"
  },
  "message": "Accommodation request submitted successfully"
}
```

## 3. ADMIN REQUEST MANAGEMENT APIs

### Get Pending Requests

**GET /api/admin/requests/pending?type=individual&city=Coimbatore**

```json
// Response
{
  "success": true,
  "data": [
    {
      "id": "REQ_001",
      "user": {
        "id": "USER_001",
        "name": "John Doe",
        "email": "john.doe@company.com",
        "role": "Manager",
        "gender": "Male"
      },
      "city": "Coimbatore",
      "dates": {
        "from": "2025-01-25",
        "to": "2025-01-30"
      },
      "duration": 6,
      "checkInTime": "08:00",
      "checkOutTime": "10:00",
      "bookingType": "individual",
      "bookingFor": "john.doe@company.com",
      "teamMembers": [],
      "status": "pending",
      "createdAt": "2025-01-20T10:00:00Z",
      "updatedAt": "2025-01-20T10:00:00Z"
    }
  ],
  "pagination": {
    "total": 1,
    "page": 1,
    "limit": 10,
    "totalPages": 1
  }
}
```

### Get Available Accommodations for Assignment

**GET /api/admin/requests/:requestId/available-accommodations**

```json
// Response
{
  "success": true,
  "data": {
    "city": "Coimbatore",
    "accommodations": {
      "flats": [
        {
          "apartment": "Pricol",
          "items": ["C4", "C6"]
        }
      ],
      "rooms": [
        {
          "apartment": "Pricol",
          "items": ["C4-R1", "C4-R2", "C6-R1"]
        }
      ],
      "beds": [
        {
          "apartment": "Pricol",
          "items": ["C4-R1-B1", "C4-R1-B2", "C4-R2-B1"]
        }
      ]
    }
  }
}
```

### Approve Request

**POST /api/admin/requests/:requestId/approve**

```json
// Request
{
  "assignedAccommodations": {
    "john.doe@company.com": "C4-R1-B1",
    "jane.smith@company.com": "C4-R1-B2"
  },
  "remarks": "Approved with standard accommodation",
  "processedBy": "ADMIN_001"
}

// Response
{
  "success": true,
  "data": {
    "id": "REQ_001",
    "status": "approved",
    "assignedAccommodations": {
      "john.doe@company.com": "C4-R1-B1",
      "jane.smith@company.com": "C4-R1-B2"
    },
    "remarks": "Approved with standard accommodation",
    "processedBy": "ADMIN_001",
    "processedAt": "2025-01-20T12:00:00Z"
  },
  "message": "Request approved successfully"
}
```

### Reject Request

**POST /api/admin/requests/:requestId/reject**

```json
// Request
{
  "remarks": "No accommodation available for the requested dates",
  "processedBy": "ADMIN_001"
}

// Response
{
  "success": true,
  "data": {
    "id": "REQ_001",
    "status": "rejected",
    "remarks": "No accommodation available for the requested dates",
    "processedBy": "ADMIN_001",
    "processedAt": "2025-01-20T12:00:00Z"
  },
  "message": "Request rejected successfully"
}
```

## 4. OCCUPANCY APIs

### Get Current Occupancy

**GET /api/admin/occupancy?city=Coimbatore&apartment=Pricol&status=occupied**

```json
// Response
{
  "success": true,
  "data": [
    {
      "id": "OCC_001",
      "apartment": "Pricol",
      "flat": "C4",
      "room": "R1",
      "bed": "B1",
      "occupant": {
        "id": "USER_001",
        "name": "John Doe",
        "email": "john.doe@company.com",
        "role": "Manager"
      },
      "checkIn": "2025-01-20",
      "checkOut": "2025-01-25",
      "status": "occupied",
      "requestId": "REQ_001",
      "createdAt": "2025-01-20T08:00:00Z"
    }
  ],
  "summary": {
    "totalUnits": 50,
    "occupied": 25,
    "available": 20,
    "maintenance": 5,
    "occupancyRate": 50
  }
}
```

### Export Occupancy Data

**POST /api/admin/occupancy/export**

```json
// Request
{
  "filters": {
    "city": "Coimbatore",
    "apartment": "Pricol",
    "status": "occupied"
  },
  "format": "csv" // or "excel"
}

// Response
{
  "success": true,
  "data": {
    "downloadUrl": "https://api.example.com/downloads/occupancy-2025-01-20.csv",
    "filename": "occupancy-2025-01-20.csv",
    "expiresAt": "2025-01-20T18:00:00Z"
  }
}
```

## 5. BOOKING HISTORY APIs

### Get Booking History

**GET /api/admin/history?status=approved&city=Coimbatore&role=Manager&search=john&dateFrom=2025-01-01&dateTo=2025-01-31&page=1&limit=10**

```json
// Response
{
  "success": true,
  "data": [
    {
      "id": "REQ_001",
      "user": {
        "id": "USER_001",
        "name": "John Doe",
        "email": "john.doe@company.com",
        "role": "Manager"
      },
      "city": "Coimbatore",
      "dates": {
        "from": "2025-01-20",
        "to": "2025-01-25"
      },
      "duration": 6,
      "checkInTime": "08:00",
      "checkOutTime": "10:00",
      "bookingType": "individual",
      "teamMembers": [],
      "status": "approved",
      "assignedAccommodations": {
        "john.doe@company.com": "C4-R1-B1"
      },
      "remarks": "Approved with standard accommodation",
      "processedBy": {
        "id": "ADMIN_001",
        "name": "Admin User"
      },
      "createdAt": "2025-01-20T10:00:00Z",
      "processedAt": "2025-01-20T12:00:00Z"
    }
  ],
  "pagination": {
    "total": 1,
    "page": 1,
    "limit": 10,
    "totalPages": 1
  },
  "summary": {
    "totalRequests": 100,
    "approved": 85,
    "rejected": 15,
    "teamBookings": 25
  }
}
```

### Export History Data

**POST /api/admin/history/export**

```json
// Request
{
  "filters": {
    "status": "approved",
    "city": "Coimbatore",
    "role": "Manager",
    "dateFrom": "2025-01-01",
    "dateTo": "2025-01-31"
  },
  "format": "csv"
}

// Response
{
  "success": true,
  "data": {
    "downloadUrl": "https://api.example.com/downloads/history-2025-01-20.csv",
    "filename": "booking-history-2025-01-20.csv",
    "expiresAt": "2025-01-20T18:00:00Z"
  }
}
```

## Database Schema Suggestions

### Tables Structure:

1. **cities** (id, name, created_at, updated_at)
2. **apartments** (id, name, city_id, google_map_link, created_at, updated_at)
3. **flats** (id, name, apartment_id, created_at, updated_at)
4. **rooms** (id, name, flat_id, bed_count, created_at, updated_at)
5. **beds** (id, name, room_id, status, created_at, updated_at)
6. **users** (id, name, email, role, gender, created_at, updated_at)
7. **accommodation_requests** (id, user_id, city, dates_from, dates_to, check_in_time, check_out_time, booking_type, status, remarks, processed_by, processed_at, created_at, updated_at)
8. **request_team_members** (id, request_id, user_email, user_name, user_role)
9. **accommodation_assignments** (id, request_id, user_email, accommodation_code, created_at)
10. **occupancy** (id, bed_id, user_id, request_id, check_in_date, check_out_date, status, created_at, updated_at)


This structure will support all the functionality in your application. Let me know if you need any clarification on the endpoints or database structure!
