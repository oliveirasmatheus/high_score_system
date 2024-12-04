
# Global High Score System Documentation

## Overview
The Global High Score System is designed to handle the submission, retrieval, and management of video game high scores. It provides a reliable, scalable, and fault-tolerant API for players to compete globally by recording their scores securely.

### Key Features
- **Metadata Support:** Records player scores along with location, timestamp, and game metadata.
- **Scalable Architecture:** Handles high traffic with low-latency responses.
- **Security:** Secure authentication and input validation.
- **Fault Tolerance:** Designed for high availability and reliability.

---

## System Design

![diagram-export-12-4-2024-12_02_28-AM](https://github.com/user-attachments/assets/6859a9c4-4d92-4fcd-8815-1c56b6076747)


The architecture includes the following components:
- **Web Interface:** Score Display Page and Score Submission Page.
- **API Layer:** Includes Score Retrieval and Submission APIs.
- **Backend Logic:** Powered by Node.js and integrated with a Load Balancer.
- **Data Storage:** Utilizes a relational database for persistent storage and CDN for performance.
- **Authentication Service:** Ensures secure access and authentic submissions.

---

## Functional Requirements

### Score Submission
The request would be an HTTP POST request to the API server's `/submit` endpoint. It should include the following JSON payload:
**Endpoint:** `POST /scores/submit`

#### Request Example
```json
POST /scores/submit HTTP/1.1
Host: api.highscore.com
Authorization: Bearer YOUR_API_KEY
Content-Type: application/json

{
  "score": 123456,
  "player_name": "GameMaster",
  "game_name": "Space Invaders",
  "timestamp": "2024-03-15T22:30:45Z",
  "location": {
    "country": "US",
    "city": "San Francisco",
    "coordinates": {
      "lat": 37.7749,
      "lon": -122.4194
    }
  }
}
```

### What should the response from your system look like? When should it respond?
The response will include:
- **Status Code:**
  - `200 OK` for a successful submission.
  - `400 Bad Request` for invalid data.
  - `401 Unauthorized` for authentication failures.

Example JSON response for success:
#### Response Example
```json
{
  "status": "success",
  "message": "Score submitted successfully",
  "data": {
    "score": 123456,
    "player_name": "GameMaster",
    "game_name": "Space Invaders",
    "timestamp": "2024-03-15T22:30:45Z",
    "location": {
      "country": "US",
      "city": "San Francisco",
      "coordinates": {
        "lat": 37.7749,
        "lon": -122.4194
      }
    }
  }
}
```

The response is sent immediately after the request is processed.

---

## Data Storage

### How does your system store the data?
The data is stored in a relational database (e.g., PostgreSQL or MySQL). It includes tables for:
- Scores (storing player scores and metadata)
- Players (player profiles and authentication data)
- Games (metadata about supported games)

---

## System Considerations

### Reliability, Availability, and Scalability
1. **Reliability:**
   - Database replication and sharding.
   - Redundant backend services.
   - Monitoring with alerting systems.
     
![diagram-export-12-4-2024-12_23_07-AM](https://github.com/user-attachments/assets/f0f1ad3e-43c0-4542-8556-ba1828756f80)

2. **Availability:**
   - Load balancing and failover mechanisms.
   - Auto-scaling of resources during high demand.

![diagram-export-12-4-2024-12_26_00-AM](https://github.com/user-attachments/assets/ebbce041-fe40-4019-9bb1-5e7b10a5f9ec)

3. **Scalability:**
   - Distributed architecture.
   - Leveraging caching and horizontal scaling for backend services.

![diagram-export-12-4-2024-12_36_46-AM](https://github.com/user-attachments/assets/0d013d9a-83d0-47f0-8e32-82ccd5b49658)


---

## Fault Tolerance

### How is your system fault-tolerant?
- Redundant backend and database components.
- Load balancer for traffic distribution.
- Circuit breakers, retries, and fallback strategies in API and processing layers.

---

## Security

### How can we ensure that the system is only receiving authentic scores?
- Authentication Service validates users through API keys, OAuth, or JWT.
- Secure HTTPS communication.
- Input data validation to prevent tampering.

---

## Load Handling

### How are you handling peak loads?
- Load balancer distributes traffic evenly across instances.
- Caching frequent queries and asynchronous message queues reduce backend load.
- Auto-scaling ensures adequate resources during high-traffic periods.

---

## Input Validation

### What other input validation might we want to do?
- **Duplicate Check:** Ensure no duplicate submissions (e.g., same player, game, and timestamp).
- **Game Validation:** Validate game names against a predefined list.
- **Location Validation:** Check GPS coordinates and address formats.
- **Content Moderation:** Sanitize player names to avoid inappropriate submissions.

---

## Tutorials

### Getting Started
1. **Set Up Your Environment**
   - Use HTTPS for secure communication.

2. **Integrate API Endpoints**
   - Use the `/scores/submit` endpoint to record player scores.
   - Use the `/scores/leaderboard` endpoint to retrieve top scores.

3. **Test Your Integration**
   - Test API calls using tools like Postman or cURL.

### Base URL
All requests should be made to:
```
https://api.highscore.com/v1
```

### Authorization
Include your API key in the `Authorization` header:
```
Authorization: Bearer YOUR_API_KEY
```

---

## More API Examples


### Retrieve Leaderboard
**Endpoint:** `GET /scores/leaderboard`

#### Request Example
```json
GET /scores/leaderboard?game_name=Space%20Invaders&limit=5 HTTP/1.1
Host: api.highscore.com
Authorization: Bearer YOUR_API_KEY
```

#### Response Example
```json
{
  "status": "success",
  "data": [
    {
      "rank": 1,
      "score": 123456,
      "player_name": "GameMaster",
      "timestamp": "2024-03-15T22:30:45Z",
      "location": {
        "country": "US",
        "city": "San Francisco",
        "coordinates": {
          "lat": 37.7749,
          "lon": -122.4194
        }
      }
    },
    {
      "rank": 2,
      "score": 100000,
      "player_name": "PixelWarrior",
      "timestamp": "2024-03-15T20:10:15Z",
      "location": {
        "country": "CA",
        "city": "Toronto",
        "coordinates": {
          "lat": 43.65107,
          "lon": -79.347015
        }
      }
    }
  ]
}
```

---

### Error Example
**Invalid API Key:**
```json
{
  "status": "error",
  "message": "Invalid API Key",
  "code": 401
}
```

---

## Glossary

- **API (Application Programming Interface):** A set of endpoints and methods that allow communication between systems.
- **Cache:** A temporary storage layer that speeds up data retrieval.
- **Load Balancer:** A tool that distributes network traffic across multiple servers.
- **JWT (JSON Web Token):** A secure token format for authentication.
- **Player Name:** The name of the player submitting a score.
- **Game Name:** The name of the game for which the score is submitted.
- **Timestamp:** The date and time when the score was achieved, in ISO 8601 format.
- **Location:** Metadata including the player's country, city, and geographic coordinates.
