
## 1. Get Booking History

**GET** `/api/requests/history?city=Coimbatore&status=approved&role=Manager&search=John&dateFrom=2025-07-01&dateTo=2025-07-31`

**Query Parameters (all optional):**
- `city` (string)
- `status` (string: `approved`, `rejected`)
- `role` (string: user role)
- `search` (string: matches name or email)
- `dateFrom` (string: ISO date)
- `dateTo` (string: ISO date)

**Response:**
```json
{
  "success": true,
  "requests": [
    {
      "id": 1,
      "timestamp": "2025-07-10T10:00:00.000Z",
      "user": {
        "id": 5,
        "name": "John Doe",
        "email": "john.doe@example.com",
        "role": "Manager"
      },
      "city": "Coimbatore",
      "dates": { "from": "2025-07-15", "to": "2025-07-20" },
      "status": "approved",
      "assignedAccommodations": {
        "john.doe@example.com": "Pricol > C4 > R1 > B1",
        "jane.smith@example.com": "Pricol > C4 > R1 > B2"
      },
      "bookingType": "team",
      "teamMembers": ["jane.smith@example.com"],
      "remarks": "Approved for team",
      "processedAt": "2025-07-11T09:00:00.000Z"
    }
    // ...more requests
  ]
}
```

---

## 2. Export Booking History

**POST** `/api/requests/history/export`

**Request Body:**
```json
{
  "filters": {
    "city": "Coimbatore",
    "status": "approved",
    "role": "Manager",
    "search": "John",
    "dateFrom": "2025-07-01",
    "dateTo": "2025-07-31"
  }
}
```

**Response:**  
- Returns a downloadable CSV or Excel file (set appropriate headers for file download).

---

## 3. (Optional) Get Filter Options

**GET** `/api/requests/history/filters`

**Response:**
```json
{
  "success": true,
  "cities": ["Coimbatore", "Chennai", "Mumbai"],
  "roles": ["Manager", "Senior", "Project Engineer"]
}
```

---

Let me know if you need sample SQL or controller code for any of these!
