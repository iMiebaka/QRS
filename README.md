# Dynamic Question Assignment System

<img src="https://github.com/iMiebaka/QRS/blob/main/design-diagram.png?raw=true" />
### Overview
This project is a scalable system designed to assign dynamic questions to users based on their region and the current cycle. Each region receives a different set of questions, and questions rotate on a configurable cycle (e.g., every week, every two weeks). The system is built using Node.js, Express, MongoDB, and Redis for caching, with scheduled updates managed by cron jobs.

The system is capable of handling millions of users by optimizing performance through database design, caching, and efficient API architecture.

### Features
<strong> - Region-Specific Questions</strong>: Each region gets its own set of questions.
<strong> - Configurable Cycle Duration</strong>: You can configure the duration of each cycle (e.g., 7 days, 14 days) using the config collection.
<strong> - Automated Question Rotation</strong>: Questions are rotated automatically at the end of each cycle through a cron job that runs periodically.
<strong> - Scalability</strong>: The system is optimized for high traffic, with Redis used to cache the current question for each region, reducing load on MongoDB.
<strong> - Flexibility</strong>: MongoDB provides flexibility in schema design, allowing for easy modifications to questions, cycles, and configurations.

## Architecture
### Components
<strong> - Client</strong>: Users from different regions send requests to fetch the current question.
<strong> - Express.js API</strong>: Handles client requests, manages business logic, and interacts with MongoDB for question and cycle data.
MongoDB:
<strong> - Questions Collection </strong>: Stores questions for each region.
<strong> - Cycles Collection </strong>: Tracks the current question and cycle start time for each region.
<strong> - Config Collection </strong>: Stores the cycle duration for each region.
<strong> - Redis (Optional) </strong>: Caches the current question for each region to speed up request responses.
<strong> - Cron Job</strong>: Automatically rotates the question at the end of each cycle based on the configuration.

### Flow of Events

#### Client Request:
The client sends a request to the API endpoint /current-question?region=US to fetch the current question for their region.
The API first checks if the current question is cached in Redis.
<strong> - Cache Hit </strong>: Returns the cached question.
<strong> - Cache Miss </strong>: Fetches the question from MongoDB, stores it in Redis for future requests, and returns the question to the client.

#### Question Rotation:
A cron job runs at the configured interval (e.g., every Monday at 7 PM SGT) to check if a new cycle has started.
The job fetches the current cycle for each region from MongoDB, checks if the current cycle duration has expired, and if so, updates the cycle with the next question.

<strong>Cycle Management</strong>:
The config collection stores the duration of each cycle (in days).
The cycles collection stores the current question and the start time of the current cycle.
At the end of each cycle, the system rotates to the next question in the questions collection for that region.
