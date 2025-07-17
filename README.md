

## 1. **POST `/api/bookings`**  
_Submit a new booking request._

### **Request Body**
```json
{
  "user": {
    "id": 1,
    "name": "John Doe",
    "email": "john.doe@company.com"
  },
  "city": "Chennai",
  "dates": {
    "from": "2025-07-18",
    "to": "2025-07-22"
  },
  "checkInTime": "08:00",
  "checkOutTime": "10:00",
  "bookingFor": "john.doe@company.com",
  "bookingType": "individual", // or "team"
  "teamMembers": ["jane.smith@company.com", "mike.johnson@company.com"]
}
```

### **Response**
- **Success:**  
  `201 Created`
  ```json
  {
    "success": true,
    "bookingId": 123,
    "message": "Accommodation request submitted successfully."
  }
  ```
- **Error:**  
  `400 Bad Request`
  ```json
  {
    "success": false,
    "message": "Validation error: Maximum stay is 14 days."
  }
  ```

---
