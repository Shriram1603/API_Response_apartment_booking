

# Accommodation Management API Contracts

## 1. Get Full Accommodation Hierarchy

**GET** `/api/accommodation/hierarchy`

**Response:**
```json
{
  "success": true,
  "hierarchy": {
    "cities": [
      { "id": 1, "name": "Coimbatore" }
      // ...
    ],
    "apartments": [
      { "id": 1, "name": "Pricol", "city_id": 1, "googleMapLink": "https://..." }
      // ...
    ],
    "flats": [
      { "id": 1, "name": "C4", "apartment_id": 1 }
      // ...
    ],
    "rooms": [
      { "id": 1, "name": "R1", "flat_id": 1, "beds": 3 }
      // ...
    ],
    "beds": [
      { "id": 1, "name": "B1", "room_id": 1, "status": null, "blocked_by": null }
      // ...
    ]
  }
}
```

---

## 2. Add/Update/Delete City

**Add:**  
**POST** `/api/cities`  
**Body:**
```json
{ "name": "Coimbatore" }
```
**Response:**  
```json
{ "success": true, "city": { "id": 1, "name": "Coimbatore" } }
```

**Update:**  
**PUT** `/api/cities/:id`  
**Body:**
```json
{ "name": "New City Name" }
```
**Response:**  
```json
{ "success": true, "city": { "id": 1, "name": "New City Name" } }
```

**Delete:**  
**DELETE** `/api/cities/:id`  
**Response:**  
```json
{ "success": true }
```

---

## 3. Add/Update/Delete Apartment

**Add:**  
**POST** `/api/apartments`  
**Body:**
```json
{ "name": "Pricol", "city_id": 1, "googleMapLink": "https://..." }
```
**Response:**  
```json
{ "success": true, "apartment": { "id": 1, "name": "Pricol", "city_id": 1, "googleMapLink": "https://..." } }
```

**Update:**  
**PUT** `/api/apartments/:id`  
**Body:**
```json
{ "name": "New Apartment Name", "googleMapLink": "https://..." }
```
**Response:**  
```json
{ "success": true, "apartment": { "id": 1, "name": "New Apartment Name", "city_id": 1, "googleMapLink": "https://..." } }
```

**Delete:**  
**DELETE** `/api/apartments/:id`  
**Response:**  
```json
{ "success": true }
```

---

## 4. Add/Update/Delete Flat

**Add:**  
**POST** `/api/flats`  
**Body:**
```json
{ "name": "C4", "apartment_id": 1 }
```
**Response:**  
```json
{ "success": true, "flat": { "id": 1, "name": "C4", "apartment_id": 1 } }
```

**Update:**  
**PUT** `/api/flats/:id`  
**Body:**
```json
{ "name": "New Flat Name" }
```
**Response:**  
```json
{ "success": true, "flat": { "id": 1, "name": "New Flat Name", "apartment_id": 1 } }
```

**Delete:**  
**DELETE** `/api/flats/:id`  
**Response:**  
```json
{ "success": true }
```

---

## 5. Add/Update/Delete Room

**Add:**  
**POST** `/api/rooms`  
**Body:**
```json
{ "name": "R1", "flat_id": 1, "beds": 3 }
```
**Response:**  
```json
{ "success": true, "room": { "id": 1, "name": "R1", "flat_id": 1, "beds": 3 } }
```

**Update:**  
**PUT** `/api/rooms/:id`  
**Body:**
```json
{ "name": "New Room Name", "beds": 4 }
```
**Response:**  
```json
{ "success": true, "room": { "id": 1, "name": "New Room Name", "flat_id": 1, "beds": 4 } }
```

**Delete:**  
**DELETE** `/api/rooms/:id`  
**Response:**  
```json
{ "success": true }
```

---

## 6. Add/Update/Delete Bed

**Add:**  
**POST** `/api/beds`  
**Body:**
```json
{ "name": "B1", "room_id": 1 }
```
**Response:**  
```json
{ "success": true, "bed": { "id": 1, "name": "B1", "room_id": 1 } }
```

**Update:**  
**PUT** `/api/beds/:id`  
**Body:**
```json
{ "name": "New Bed Name", "status": "booked", "blocked_by": "John Doe" }
```
**Response:**  
```json
{ "success": true, "bed": { "id": 1, "name": "New Bed Name", "room_id": 1, "status": "booked", "blocked_by": "John Doe" } }
```

**Delete:**  
**DELETE** `/api/beds/:id`  
**Response:**  
```json
{ "success": true }
```

---

## 7. Import/Export Hierarchy (Optional)

**Export:**  
**GET** `/api/accommodation/hierarchy/export`  
_Response: JSON file download_

**Import:**  
**POST** `/api/accommodation/hierarchy/import`  
_Body: JSON file upload (multipart/form-data)_

**Response:**  
```json
{ "success": true }
```

---

Let me know if you want sample SQL or controller code for any of these!
