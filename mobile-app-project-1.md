# **scenario: Booking a movie ticket via mobile app**

Here's the **project structure** for the movie ticket booking application using **React**, **Spring Boot**, and **SOLID principles**:

### **1. Frontend (React)**:

The frontend handles the user interface for booking the movie tickets and processing payments via Stripe.

```
movie-booking-app/
├── public/
│   ├── index.html               # HTML entry point for React app
│   └── ...                      # Static assets (images, etc.)
├── src/
│   ├── assets/                  # Static files like images, icons, etc.
│   ├── components/              # React Components
│   │   ├── Booking.js           # Booking page component
│   │   ├── PaymentService.js    # Service for handling payment logic
│   ├── App.js                   # Main React app component
│   ├── index.js                 # Entry point for React
│   ├── routes.js                # App routing configuration
│   └── styles/                  # CSS or styling files
│       ├── booking.css          # Styles for booking page
│       └── ...                  # Other CSS files
├── package.json                 # Project dependencies and scripts
└── README.md                    # Project documentation
```

### **2. Backend (Spring Boot)**:

The backend is built using **Spring Boot** and contains the business logic, payment processing, and interactions with the MySQL database.

```
movie-booking-backend/
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   ├── com/
│   │   │   │   ├── example/
│   │   │   │   │   ├── moviebooking/
│   │   │   │   │   │   ├── controller/              # Controllers to handle HTTP requests
│   │   │   │   │   │   │   ├── PaymentController.java # Controller for handling payment requests
│   │   │   │   │   │   │   └── BookingController.java # Controller for handling booking requests
│   │   │   │   │   │   ├── entity/                   # JPA entities (e.g., Booking)
│   │   │   │   │   │   │   └── Booking.java          # Entity class for booking
│   │   │   │   │   │   ├── repository/               # JPA repositories
│   │   │   │   │   │   │   └── BookingRepository.java # Repository for booking entity
│   │   │   │   │   │   ├── service/                  # Business logic (services)
│   │   │   │   │   │   │   ├── PaymentService.java   # Service to handle Stripe payment
│   │   │   │   │   │   │   ├── BookingService.java   # Service to handle booking logic
│   │   │   │   │   │   │   └── StripeConfig.java     # Stripe configuration for API key
│   │   │   │   │   │   ├── config/                   # Configuration files
│   │   │   │   │   │   │   └── StripeConfig.java     # Stripe configuration
│   │   │   │   │   │   ├── Application.java          # Main entry point for Spring Boot
│   │   │   │   │   │   ├── PaymentRequest.java       # DTO for payment request
│   │   │   │   │   │   └── ...                       # Other classes for business logic
│   │   ├── resources/
│   │   │   ├── application.properties               # Database and application configuration
│   │   │   └── ...                                  # Other resources (logs, etc.)
│   └── test/
│       ├── java/
│       │   └── com/example/moviebooking/            # Test classes for backend logic
│       └── resources/
│           └── ...                                  # Test resources
├── pom.xml                      # Maven dependencies and build configuration
└── README.md                    # Backend project documentation
```

---

### **Detailed Description of the Project Structure**:

#### **Frontend (React)**

* **`public/`**:

  * Contains the `index.html` file and static assets like images, fonts, and icons.
* **`src/`**:

  * **`components/`**:

    * **`Booking.js`**: This is the main component for displaying movie booking details and handling the payment through Stripe.
    * **`PaymentService.js`**: This file contains the logic for making API calls to the backend and processing payments using Stripe.

  * **`App.js`**: This is the root component of the app that includes the routing logic and overall structure.

  * **`index.js`**: The entry point for the React application.

  * **`styles/`**: Contains all the styles (CSS or styled-components) used throughout the app.

  * **`routes.js`**: This file would define the different routes in the app (e.g., booking page, home page, etc.).

#### **Backend (Spring Boot)**

* **`controller/`**:

  * **`PaymentController.java`**: This controller handles payment-related requests, including initiating payments through Stripe.
  * **`BookingController.java`**: Handles booking-related requests, such as booking tickets and managing user bookings.

* **`entity/`**:

  * **`Booking.java`**: Represents a booking in the system. Contains fields like `movie`, `user`, `amount`, and other necessary fields.

* **`repository/`**:

  * **`BookingRepository.java`**: The repository interface for interacting with the database. It provides CRUD operations for `Booking` entities.

* **`service/`**:

  * **`PaymentService.java`**: Handles the logic for interacting with Stripe, creating payment intents, and processing payments.
  * **`BookingService.java`**: Handles the logic for creating and managing bookings in the system.
  * **`StripeConfig.java`**: Contains Stripe-specific configuration such as the API key to interact with Stripe's service.

* **`config/`**:

  * **`StripeConfig.java`**: Configuration for Stripe API keys.

* **`Application.java`**: The main entry point to the Spring Boot application.

* **`PaymentRequest.java`**: This is a DTO (Data Transfer Object) for handling the payment details coming from the front-end.

* **`application.properties`**: Contains database configurations, Stripe keys, and other necessary configurations for the Spring Boot app.

---

### **Database Structure (MySQL)**

* **`Booking` Table**:

  * `id` (INT, PRIMARY KEY, AUTO_INCREMENT)
  * `movie` (VARCHAR)
  * `user` (VARCHAR)
  * `amount` (INT)
  * `created_at` (TIMESTAMP)
  * `updated_at` (TIMESTAMP)

### **Stripe Integration**

* **StripeConfig.java**: Configures Stripe's API key for both testing and production environments.
* **PaymentService.java**: Manages payment creation using Stripe’s API to generate a payment intent.

---

### **SOLID Principles in This Structure**:

* **Single Responsibility Principle (SRP)**:

  * Each class has one responsibility. For example, the `PaymentService` only handles payment processing, and the `BookingService` handles booking management.

* **Open/Closed Principle (OCP)**:

  * The system can be extended easily. For example, you can add new payment gateways like PayPal or integrate more booking systems without modifying the existing codebase.

* **Liskov Substitution Principle (LSP)**:

  * The app can substitute any class that implements the same interface. For instance, adding another payment provider that implements the same interface can be done without breaking the system.

* **Interface Segregation Principle (ISP)**:

  * The system has clearly defined interfaces and does not force clients to implement unnecessary methods. Each class (e.g., `PaymentService`, `BookingService`) is kept lean and focused.

* **Dependency Inversion Principle (DIP)**:

  * The app relies on abstractions (e.g., interfaces or services) rather than concrete classes. For example, `PaymentService` relies on an abstraction for payment processing, allowing the payment method to be swapped out.

---

### **How to Run This Project**:

#### **Frontend (React)**:

1. Clone the frontend repository.
2. Run `npm install` to install dependencies.
3. Run `npm start` to start the development server.

#### **Backend (Spring Boot)**:

1. Clone the backend repository.
2. Create a `database.properties` file or use `application.properties` to configure your database and Stripe keys.
3. Run the Spring Boot application using `mvn spring-boot:run` or through your IDE.

---

This structure ensures that the application is maintainable, scalable, and follows best practices for both front-end and back-end development, while adhering to the SOLID principles for clean and extensible code.



### Project: **Booking a Movie Ticket via Mobile App with Payment Service Failure Handling**

This project involves building a simple movie ticket booking application using **React** for the front-end and **Spring Boot** for the back-end. The goal is to implement payment failure handling in a user-friendly manner, and we’ll apply **SOLID principles** to ensure maintainable and scalable code.

---

### **Technologies Used**:

* **React** for the front-end
* **Spring Boot** for the back-end
* **Stripe API** (or another payment provider) for handling payments
* **Java** for implementing SOLID principles
* **MySQL** for the database (to store user and booking details)

---

### **Project Structure**:

* **Frontend (React)**: Handles the user interface, booking UI, and payment flow.
* **Backend (Spring Boot)**: Handles business logic, payment processing, and communicates with the front-end.

---

### **1. Frontend (React)**

#### 1.1 Install Dependencies

```bash
npx create-react-app movie-booking-app
cd movie-booking-app
npm install axios react-router-dom stripe react-stripe-checkout
```

#### 1.2 Create Payment Service (PaymentService.js)

Create a `PaymentService.js` that handles payment logic via Stripe or another payment gateway.

```js
import axios from 'axios';

class PaymentService {
  static async processPayment(paymentData) {
    try {
      const response = await axios.post('http://localhost:8080/api/payment', paymentData);
      return response.data;
    } catch (error) {
      throw new Error('Payment processing failed. Please try again later.');
    }
  }
}

export default PaymentService;
```

#### 1.3 Movie Booking Component (Booking.js)

```js
import React, { useState } from 'react';
import StripeCheckout from 'react-stripe-checkout';
import PaymentService from './PaymentService';

const Booking = () => {
  const [ticketDetails, setTicketDetails] = useState({ movie: 'Avengers', price: 15 });
  const [paymentStatus, setPaymentStatus] = useState('');

  const handleToken = async (token) => {
    const paymentData = {
      token,
      amount: ticketDetails.price * 100, // Price in cents
      movie: ticketDetails.movie,
    };

    try {
      const response = await PaymentService.processPayment(paymentData);
      setPaymentStatus(response.message); // Success message
    } catch (error) {
      setPaymentStatus(error.message); // Error message
    }
  };

  return (
    <div className="booking-container">
      <h2>Book Your Movie Ticket</h2>
      <p>Movie: {ticketDetails.movie}</p>
      <p>Price: ${ticketDetails.price}</p>

      <StripeCheckout
        stripeKey="your-publishable-key-here"
        token={handleToken}
        amount={ticketDetails.price * 100}
        name="Movie Booking"
        description={`Book your ticket for ${ticketDetails.movie}`}
        currency="USD"
      />

      {paymentStatus && <p>{paymentStatus}</p>}
    </div>
  );
};

export default Booking;
```

---

### **2. Backend (Spring Boot)**

#### 2.1 Create Spring Boot Project

Create a new Spring Boot application using Spring Initializr with dependencies for **Spring Web**, **Spring Boot DevTools**, **Stripe SDK**, and **MySQL**.

#### 2.2 Controller for Payment Processing (PaymentController.java)

```java
package com.example.moviebooking.controller;

import com.example.moviebooking.service.PaymentService;
import com.stripe.exception.StripeException;
import com.stripe.model.PaymentIntent;
import org.springframework.web.bind.annotation.*;

import java.util.HashMap;
import java.util.Map;

@RestController
@RequestMapping("/api/payment")
public class PaymentController {

    private final PaymentService paymentService;

    public PaymentController(PaymentService paymentService) {
        this.paymentService = paymentService;
    }

    @PostMapping
    public Map<String, String> processPayment(@RequestBody PaymentRequest paymentRequest) throws StripeException {
        PaymentIntent paymentIntent = paymentService.createPaymentIntent(paymentRequest);
        Map<String, String> response = new HashMap<>();
        response.put("message", "Payment successful!");
        return response;
    }
}

class PaymentRequest {
    public String token;
    public int amount;
    public String movie;
}
```

#### 2.3 Payment Service (PaymentService.java)

The service will use **SOLID principles**, focusing on **Single Responsibility Principle** (SRP), **Dependency Inversion Principle** (DIP), and **Interface Segregation Principle** (ISP).

```java
package com.example.moviebooking.service;

import com.stripe.exception.StripeException;
import com.stripe.model.PaymentIntent;
import com.stripe.net.RequestOptions;
import org.springframework.stereotype.Service;

import java.util.HashMap;
import java.util.Map;

@Service
public class PaymentService {

    private static final String STRIPE_SECRET_KEY = "your-secret-key-here";

    public PaymentIntent createPaymentIntent(PaymentRequest paymentRequest) throws StripeException {
        Map<String, Object> params = new HashMap<>();
        params.put("amount", paymentRequest.amount);
        params.put("currency", "usd");
        params.put("payment_method", paymentRequest.token);

        // Create PaymentIntent
        RequestOptions requestOptions = RequestOptions.builder().setApiKey(STRIPE_SECRET_KEY).build();
        return PaymentIntent.create(params, requestOptions);
    }
}
```

#### 2.4 Application Configuration for Stripe (StripeConfig.java)

```java
package com.example.moviebooking.config;

import com.stripe.Stripe;
import org.springframework.context.annotation.Configuration;

@Configuration
public class StripeConfig {

    public StripeConfig() {
        Stripe.apiKey = "your-secret-key-here";
    }
}
```

---

### **3. SOLID Principles Applied**

1. **Single Responsibility Principle (SRP)**:

   * The `PaymentService` handles only payment logic.
   * `PaymentController` only handles HTTP requests.

2. **Open/Closed Principle (OCP)**:

   * The payment method can be extended to support other payment providers without modifying existing code.

3. **Liskov Substitution Principle (LSP)**:

   * If we were to extend payment services to include other providers (e.g., PayPal), we could extend `PaymentService` and implement a new subclass without affecting the existing code.

4. **Interface Segregation Principle (ISP)**:

   * We create separate classes and interfaces for different payment services if needed, ensuring no unnecessary dependencies are injected into other parts of the application.

5. **Dependency Inversion Principle (DIP)**:

   * The `PaymentService` class depends on abstractions (e.g., `PaymentGateway`) rather than concrete classes.

---

### **4. MySQL Integration**

#### 4.1 MySQL Setup

* Set up a MySQL database to store user bookings and payment information.

#### 4.2 Booking Entity (Booking.java)

```java
package com.example.moviebooking.entity;

import javax.persistence.Entity;
import javax.persistence.Id;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;

@Entity
public class Booking {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String movie;
    private String user;
    private int amount;

    // Getters and setters
}
```

#### 4.3 Booking Repository (BookingRepository.java)

```java
package com.example.moviebooking.repository;

import com.example.moviebooking.entity.Booking;
import org.springframework.data.jpa.repository.JpaRepository;

public interface BookingRepository extends JpaRepository<Booking, Long> {
}
```

#### 4.4 Booking Service (BookingService.java)

```java
package com.example.moviebooking.service;

import com.example.moviebooking.entity.Booking;
import com.example.moviebooking.repository.BookingRepository;
import org.springframework.stereotype.Service;

@Service
public class BookingService {

    private final BookingRepository bookingRepository;

    public BookingService(BookingRepository bookingRepository) {
        this.bookingRepository = bookingRepository;
    }

    public Booking createBooking(String movie, String user, int amount) {
        Booking booking = new Booking();
        booking.setMovie(movie);
        booking.setUser(user);
        booking.setAmount(amount);
        return bookingRepository.save(booking);
    }
}
```

---

### **5. Handling Payment Failures**

* If the payment fails (due to issues like service downtime or insufficient funds), the backend can catch the error, log it, and return an appropriate message to the front-end (e.g., “Payment failed. Please try again later.”).
* The front-end can show the failure message and allow the user to retry or select an alternative payment method.

---

### **Conclusion**

By applying SOLID principles, we have built a simple yet scalable system that can handle movie ticket bookings, process payments, and manage payment failures efficiently. This architecture can be expanded in the future to support multiple payment providers or additional features like user authentication, booking history, etc.
