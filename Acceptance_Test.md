Here’s a clear acceptance test suite for the Circuit Breaker pattern, written in a BDD style (Given–When–Then).

## 🧪 Feature: Circuit Breaker Behavior

Feature: Circuit Breaker for External Service Calls

Background:
Given the circuit breaker failure threshold is 3
And the reset timeout is 30 seconds
And the external service is initially healthy

Scenario: Requests succeed when service is healthy (Closed state)
Given the circuit breaker is in CLOSED state
When the client makes a request
Then the request should be forwarded to the external service
And the response should be returned successfully
And the failure count should remain 0

Scenario: Circuit opens after consecutive failures
Given the circuit breaker is in CLOSED state
And the external service fails for 3 consecutive requests
When the client makes another request
Then the circuit breaker should transition to OPEN state
And the request should not be sent to the external service
And a fallback response should be returned

Scenario: Requests are blocked when circuit is OPEN
Given the circuit breaker is in OPEN state
When the client makes a request
Then the request should not be forwarded
And a fallback response should be returned immediately

Scenario: Circuit transitions to HALF-OPEN after timeout
Given the circuit breaker is in OPEN state
And 30 seconds have passed
When the next request is made
Then the circuit breaker should transition to HALF-OPEN state
And allow a limited request to the external service

Scenario: Circuit closes after successful trial in HALF-OPEN
Given the circuit breaker is in HALF-OPEN state
And the external service responds successfully
When the trial request is made
Then the circuit breaker should transition to CLOSED state
And normal traffic should resume

Scenario: Circuit reopens if failure occurs in HALF-OPEN
Given the circuit breaker is in HALF-OPEN state
And the external service fails
When the trial request is made
Then the circuit breaker should transition back to OPEN state
And fallback response should be returned

Scenario: Failure count resets after success
Given the circuit breaker is in CLOSED state
And there was 1 previous failure
When a successful request is made
Then the failure count should reset to 0

Scenario: Fallback response is returned when service is unavailable
Given the circuit breaker is OPEN
When the client makes a request
Then a predefined fallback response should be returned
And no external service call should be attempted

---

## 🔍 What This Covers

This acceptance test ensures:

* Correct **state transitions** (Closed → Open → Half-Open → Closed)
* Proper **failure threshold handling**
* **Timeout-based recovery**
* **Fallback behavior**
* **Protection from repeated failures**
