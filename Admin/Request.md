

# Accommodation Admin API Contracts

## 1. Get All Pending Requests

**GET** `/api/requests?status=pending`

**Response:**
```json
{
  "success": true,
  "requests": [
    {
      "id": 1,
      "user": {
        "id": 5,
        "name": "John Doe",
        "email": "john@example.com",
        "role": "manager"
      },
      "start_time": "2025-07-18T00:00:00.000Z",
      "end_time": "2025-07-22T00:00:00.000Z",
      "status": "pending",
      "remarks": null,
      "created_at": "2025-07-17T12:34:56.000Z",
      "apartment_id": null,
      "flat_id": null,
      "room_id": null,
      "cottage_id": null
    }
    // ...more requests
  ]
}
```

---

## 2. Approve a Request

**POST** `/api/requests/:id/approve`

**Request Body:**
```json
{
  "apartment_id": 2,   // optional, depending on assignment
  "flat_id": 5,        // optional
  "room_id": 10,       // optional
  "cottage_id": 20,    // optional
  "remarks": "Approved for flat C4",
  "assigned_by": 1     // admin user id (optional, for audit)
}
```

**Response:**
```json
{
  "success": true,
  "message": "Request approved.",
  "request": {
    "id": 1,
    "status": "approved",
    "apartment_id": 2,
    "flat_id": 5,
    "room_id": 10,
    "cottage_id": 20,
    "remarks": "Approved for flat C4"
  }
}
```

---

## 3. Reject a Request

**POST** `/api/requests/:id/reject`

**Request Body:**
```json
{
  "remarks": "No availability for requested dates.",
  "assigned_by": 1 // admin user id (optional)
}
```

**Response:**
```json
{
  "success": true,
  "message": "Request rejected.",
  "request": {
    "id": 1,
    "status": "rejected",
    "remarks": "No availability for requested dates."
  }
}
```

---

## 4. Get Accommodation Hierarchy for a City

**GET** `/api/cities/:cityId/accommodations`

**Response:**
```json
{
  "success": true,
  "apartments": [
    {
      "id": 1,
      "name": "Pricol",
      "flats": [
        {
          "id": 5,
          "name": "C4",
          "is_booked": false,
          "rooms": [
            {
              "id": 10,
              "name": "C4-R1",
              "is_booked": false,
              "cottages": [
                {
                  "id": 20,
                  "name": "C4-R1-B1",
                  "is_booked": false
                }
              ]
            }
          ]
        }
      ]
    }
  ]
}
```

---

## 5. (Optional) Get All Users

**GET** `/api/users`

**Response:**
```json
{
  "success": true,
  "users": [
    { "id": 5, "name": "John Doe", "email": "john@example.com", "role": "manager" }
    // ...
  ]
}
```

---
