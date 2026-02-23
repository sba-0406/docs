# Flow Doc 02: Registration

This document details the **Registration Flow**, tracing how a new candidate profile is created, secured, and initialized with the correct access rights.

---

## 🚀 Step 1: User Action (Frontend UI)
*   **File**: [register.ejs](file:///c:/Users/shaik/Downloads/dojo-main/dojo-main/frontend/views/register.ejs)
*   **Trigger**: User clicks `<button type="submit">Create Account</button>`.

### 🔍 Behind the Scenes (JS Logic)
The `registerForm` script collects the user's name, email, and password:
```javascript
const res = await fetch('/api/auth/register', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ name, email, password })
});
```

---

## 🛡️ Step 2: Request Entry & Parsing
*   **Entry Point**: `app.js` -> `express.json()`
*   **Initial `req.body`**: `{ "name": "Jane Doe", "email": "jane@example.com", "password": "mypassword123" }`

---

## 📍 Step 3: Routing
*   **Path**: `app.js` -> `authRoutes.js` -> `router.post('/register', register)`
*   The request is routed to the `register` function in `authController.js`.

---

## 🧠 Step 4: Logic Execution (Controller)
*   **File**: [authController.js](file:///c:/Users/shaik/Downloads/dojo-main/dojo-main/backend/controllers/authController.js)
*   **Method**: `exports.register`

### 🕵️ Execution Trace:
1.  **Extraction**: Destructures `name`, `email`, and `password` from `req.body`.
2.  **Role Injection**: 
    ```javascript
    const user = await User.create({
        name,
        email,
        password,
        role: 'candidate' // FORCED ROLE
    });
    ```
    *   **CRITICAL**: Even if a hacker tried to send `role: 'admin'` in the request body, the controller **overwrites** it here. This is the first layer of RBAC security.

---

## 🔒 Step 5: Password Hashing (Model Hook)
*   **File**: [User.js](file:///c:/Users/shaik/Downloads/dojo-main/dojo-main/backend/models/User.js)
*   **Hook**: `UserSchema.pre('save', ...)`

Before the user is saved to MongoDB:
1.  **Salt Generation**: `bcrypt.genSalt(10)` creates a random string.
2.  **Hashing**: `bcrypt.hash(this.password, salt)` transforms the plain-text password into a complex hash (e.g., `$2a$10$X...`).
3.  **Storage**: Only the **hash** is stored in the database. The plain-text password is forgotten.

---

## 🏁 Step 6: Token Issue & Auto-Login
*   **Method**: `sendTokenResponse`

1.  A JWT is generated for the new user.
2.  The user is logged in automatically (cookie set).
3.  **Frontend Redirection**: The script in `register.ejs` redirects the new user to `/api/applications/my-dashboard` (The Candidate Dashboard).

### ✅ Final State:
*   **Database**: A new document exists in the `users` collection with `role: 'candidate'`.
*   **Client**: Browser has a valid session token.
