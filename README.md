
# Global High Score System Documentation

## Overview
The Global High Score System is designed to handle the submission, retrieval, and management of video game high scores. It provides a reliable, scalable, and fault-tolerant API for players by recording their scores securely.


## Requirements

### Functional Requirements
- The score will be displayed in a web page or pages.
- Scores can also be retrieved via an API.
- Scores can be submitted via web page or an API.
- Scores can only be edited by the submitter.

### Non-Functional Requirements
- Reliability
- Availability
- Scalability

## System Architecture

![diagram-export-12-4-2024-11_22_13-PM](https://github.com/user-attachments/assets/8957b173-8f74-456a-bb32-9e71afdc68d1)

## API
The API designed for this architecture follows the RESTful paradigm. This approach was chosen due to its simplicity, scalability, and widespread adoption, which ensures compatibility with a wide range of clients and technologies. 
- Use the `/scores/` endpoint to retrieve all players and games top scores.
- Use the `/scores/submit` endpoint to record player scores.
- Use the `/scores/game` endpoint to get scores by game.
- Use the `/scores/player/:player-id` endpoint to get all scores from a single player   

### Score Submission
The request would be an HTTP POST request to the API server's `/scores/submit` endpoint. It should include the following JSON payload:

#### Request Example
```json
POST /scores/submit HTTP/1.1
Authorization: Bearer <JWT>
Content-Type: application/json

{
  "user_id": 321,
  "score": 123456,
  "game_name": "Pokemon",
  "date_time": "2024-03-15T22:30:45Z",
  "location": {
    "latitude": 37.7749,
    "longitude": -122.4194
  }
}
```

### Response
The response is sent immediately after the request is processed.

#### Response Example Status code 200 OK
```json
{
  "status": "success",
  "message": "Score submitted successfully",
  "data": {
    "score_id": 123,
    "user_id": 321,
    "score": 123456,
    "game_name": "Pokemon",
    "date_time": "2024-03-15T22:30:45Z",
    "location": {
      "latitude": 37.7749,
      "longitude": -122.4194
    }
  }
}
```

#### Response Example Status code 404 not found
```json
{
  "status": "error",
  "message": "User not found",
  "errors": [
    {
      "code": "USER_NOT_REGISTERED",
      "detail": "The provided user ID does not exist in our system.",
      "suggestion": "Please register an account before submitting scores.",
      "action": {
        "type": "register",
        "url": "/auth/register"
      }
    }
  ]
}
```

### Get All Scores
Retrieves all players and games top scores.

#### Request Example
```json
GET /scores/ HTTP/1.1
Authorization: Bearer <JWT>
```

#### Response Example Status code 200 OK
```json
{
  "status": "success",
  "data": {
    "scores": [
      {
        "score_id": 123,
        "user_id": 321,
        "score": 123456,
        "game_name": "Pokemon",
        "date_time": "2024-03-15T22:30:45Z"
      },
      {
        "score_id": 124,
        "user_id": 322,
        "score": 98765,
        "game_name": "Tetris",
        "date_time": "2024-03-15T22:31:00Z"
      }
    ],
    "pagination": {
      "total": 150,
      "page": 1,
      "per_page": 20
    }
  }
}
```

### Get Scores by Game
Retrieves all scores for a specific game.

#### Request Example
```json
GET /scores/game?name=Pokemon HTTP/1.1
Authorization: Bearer <JWT>
```

#### Response Example Status code 200 OK
```json
{
  "status": "success",
  "data": {
    "game_name": "Pokemon",
    "scores": [
      {
        "score_id": 123,
        "user_id": 321,
        "score": 123456,
        "date_time": "2024-03-15T22:30:45Z"
      },
      {
        "score_id": 125,
        "user_id": 323,
        "score": 89012,
        "date_time": "2024-03-15T22:32:00Z"
      }
    ],
    "pagination": {
      "total": 50,
      "page": 1,
      "per_page": 20
    }
  }
}
```

### Get Player Scores
Retrieves all scores for a specific player.

#### Request Example
```json
GET /scores/player/321 HTTP/1.1
Authorization: Bearer <JWT>
```

#### Response Example Status code 200 OK
```json
{
  "status": "success",
  "data": {
    "user_id": 321,
    "scores": [
      {
        "score_id": 123,
        "game_name": "Pokemon",
        "score": 123456,
        "date_time": "2024-03-15T22:30:45Z"
      },
      {
        "score_id": 126,
        "game_name": "Tetris",
        "score": 45678,
        "date_time": "2024-03-15T22:33:00Z"
      }
    ],
    "pagination": {
      "total": 25,
      "page": 1,
      "per_page": 20
    }
  }
}
```

#### Response Example Status code 404 Not Found
```json
{
  "status": "error",
  "message": "Player not found",
  "errors": [
    {
      "code": "PLAYER_NOT_FOUND",
      "detail": "The specified player ID does not exist in our system.",
      "suggestion": "Please verify the player ID and try again."
    }
  ]
}
```

## Data storage

![diagram-export-12-4-2024-10_28_53-PM](https://github.com/user-attachments/assets/feb70d8c-b81c-4324-a086-2bc292b433f7)


### How does the system store data

1. **User Submits Data**:
  - The **Web Pages** or **API Client** sends a request to the **API Gateway** with the data to be stored (e.g., a new video game score).
   
2. **API Gateway Routes the Request**:  
  - The **API Gateway** checks the request for authentication and authorization. If the request is valid, it forwards the request to the **Authentication Service** to verify the user’s identity.

3. **Authentication Service**:  
  - The **Authentication Service** validates the user's credentials (e.g., checking if the user is logged in or if the request contains a valid token).
  - If the authentication is successful, the **API Gateway** passes the request along to the **Web Server** for further processing.

4. **Web Server Processes the Request**:  
  - The **Web Server** receives the validated request and processes the data (e.g., saving a new high score).  
  - The server interacts with the **Relational Database** to store the data.
   
5. **Storing the Data**:  
  - The data (score, player name, game name, date, and location) is saved to the **Relational Database** for persistence.
  - Optionally, the system may use a **Cache** for quick access to frequently requested data.

6. **Async Processing** (if needed):  
  - For performance reasons, some operations (e.g., processing scores for leaderboards) might be offloaded to **asynchronous processing** via a **Message Queue**.

7. **Confirmation Response**:  
  - After successfully storing the data, the **Web Server** sends a response back to the **API Gateway**, which then forwards the response to the **Web Pages** or **API Client** confirming that the score has been added.

## System Considerations

### Reliability, Availability, and Scalability
1. **Reliability:**
   - Database replication and sharding.
   - Redundant backend services.
   - Monitoring with alerting systems.
     

2. **Availability:**
   - Load balancing and failover mechanisms.
   - Auto-scaling of resources during high demand.


3. **Scalability:**
   - Distributed architecture.
   - Leveraging caching and horizontal scaling for backend services.

## Fault Tolerance

- Load Balancer: Redirects traffic to healthy web servers.
- Database Replication: Ensures data is available even if the primary database fails.
- Message Queue: Ensures that asynchronous tasks are retried on failure.

## Security

- Authentication Service validates users through API keys and JWT.
- Secure HTTPS communication.
- Input data validation to prevent tampering.

## Load Handling

- Load balancer distributes traffic evenly across instances.
- Caching frequent queries and asynchronous message queues reduce backend load.
- Auto-scaling ensures adequate resources during high-traffic periods.

## Input Validation

- **Duplicate Check:** Ensure no duplicate submissions (e.g., same player, game, and timestamp).
- **Game Validation:** Validate game names against a predefined list.
- **Location Validation:** Verify that the location is a valid, recognizable format.
- **Content Moderation:** Sanitize player names to avoid inappropriate submissions.


