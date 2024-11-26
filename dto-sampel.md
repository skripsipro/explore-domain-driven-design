
# Data Transfer Objects (DTOs) in Domain-Driven Design (DDD)

Data Transfer Object (DTO) is used to transfer data between layers of an application without adding business logic. DTOs are typically used for:

1. Validating user input.
2. Restricting the data returned to the client.
3. Providing consistent data structures between application layers.

## **Examples of DTOs**

### **1. User DTO (`dtos/UserDTO.js`)**

```javascript
class UserDTO {
  constructor({ id, name, email, isActive }) {
    this.id = id;
    this.name = name;
    this.email = email;
    this.isActive = isActive;
  }

  // Optional: Utility function to format data
  toJSON() {
    return {
      id: this.id,
      name: this.name,
      email: this.email,
      isActive: this.isActive,
    };
  }
}

module.exports = UserDTO;
```

### **2. User Registration DTO (`dtos/UserRegistrationDTO.js`)**

```javascript
class UserRegistrationDTO {
  constructor({ name, email, password }) {
    this.name = name;
    this.email = email;
    this.password = password;
  }

  // Validate input data (optional)
  static validate(data) {
    const errors = [];
    if (!data.name) errors.push('Name is required');
    if (!data.email) errors.push('Email is required');
    if (!data.password || data.password.length < 6) {
      errors.push('Password must be at least 6 characters');
    }

    if (errors.length > 0) {
      throw new Error(errors.join(', '));
    }
  }
}

module.exports = UserRegistrationDTO;
```

### **3. User Response DTO (`dtos/UserResponseDTO.js`)**

```javascript
class UserResponseDTO {
  constructor({ id, name, email }) {
    this.id = id;
    this.name = name;
    this.email = email;
  }

  toJSON() {
    return {
      id: this.id,
      name: this.name,
      email: this.email,
    };
  }
}

module.exports = UserResponseDTO;
```

## **4. Usage of DTOs**

### **Use Case Utilizing DTO (`application/use-cases/RegisterUser.js`)**

```javascript
const User = require('../../domain/entities/User');
const UserDTO = require('../dtos/UserDTO');

class RegisterUser {
  constructor({ userRepository, emailService }) {
    this.userRepository = userRepository;
    this.emailService = emailService;
  }

  async execute(userData) {
    const user = new User(userData);
    await this.userRepository.save(user);
    await this.emailService.sendWelcomeEmail(user.email);

    // Use DTO for output
    return new UserDTO(user);
  }
}

module.exports = RegisterUser;
```

### **Controller Utilizing DTO (`http/controllers/UserController.js`)**

```javascript
const RegisterUser = require('../../application/use-cases/RegisterUser');
const UserRegistrationDTO = require('../../application/dtos/UserRegistrationDTO');
const UserResponseDTO = require('../../application/dtos/UserResponseDTO');
const UserRepositoryImpl = require('../database/repositories/UserRepositoryImpl');
const EmailService = require('../services/EmailService');

const userRepository = new UserRepositoryImpl();
const emailService = new EmailService();

class UserController {
  async register(req, res, next) {
    try {
      // Validate input with DTO
      UserRegistrationDTO.validate(req.body);

      const registerUser = new RegisterUser({ userRepository, emailService });
      const user = await registerUser.execute(req.body);

      // Use DTO for response
      const response = new UserResponseDTO(user);
      res.status(201).json(response.toJSON());
    } catch (error) {
      next(error);
    }
  }
}

module.exports = new UserController();
```

## **Advantages of Using DTOs**

1. **Consistency:**  
   DTOs provide a uniform data format across application layers.

2. **Centralized Validation:**  
   Input validation can be performed in one place using DTOs.

3. **Ease of Maintenance:**  
   If there is a change in data format, only the DTOs need to be updated.

4. **Security:**  
   DTOs help restrict the data exposed to the client, ensuring only relevant data is sent.

By using DTOs, your application architecture becomes more structured, consistent, and easier to maintain. 🚀
