# Occupancy Overview API Contracts

## 1. Get Occupancy Data

**GET** `/api/occupancy?city=Coimbatore&apartment=Pricol&status=occupied`

**Query Parameters (all optional):**
- `city` (string)
- `apartment` (string)
- `status` (string: `occupied`, `available`, `maintenance`)

**Response:**
```json
{
  "success": true,
  "data": [
    {
      "apartment": "Pricol",
      "flat": "C4",
      "room": "R1",
      "bed": "B1",
      "occupant": "John Doe",
      "checkIn": "2025-07-15",
      "checkOut": "2025-07-20",
      "status": "occupied",
      "role": "Manager"
    },
    {
      "apartment": "Pricol",
      "flat": "C4",
      "room": "R2",
      "bed": "B1",
      "occupant": null,
      "checkIn": null,
      "checkOut": null,
      "status": "available",
      "role": null
    }
    // ...more rows
  ]
}
```

---

## 2. Export Occupancy Data

**POST** `/api/occupancy/export`

**Request Body:**
```json
{
  "filters": {
    "city": "Coimbatore",
    "apartment": "Pricol",
    "status": "occupied"
  }
}
```

**Response:**  
- Returns a downloadable CSV or Excel file (set appropriate headers for file download).

---

## 3. (Optional) Get List of Cities and Apartments

**GET** `/api/occupancy/filters`

**Response:**
```json
{
  "success": true,
  "cities": ["Coimbatore", "Chennai", "Bangalore"],
  "apartments": ["Pricol", "Sreevatsa Srilakshmi", "Chennai Heights"]
}
```

---

Let me know if you need sample SQL or controller code for any of these!
