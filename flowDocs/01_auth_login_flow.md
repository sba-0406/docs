# Flow Doc 01: Authentication (Login)

This document provides a microscopic trace of the **Login Flow** in the ScenarioSim project. It follows a single request from the moment a user clicks "Sign In" until they are redirected to their specialized dashboard.

---

## 🚀 Step 1: User Action (Frontend UI)
*   **File**: [login.ejs](file:///c:/Users/shaik/Downloads/dojo-main/dojo-main/frontend/views/login.ejs)
*   **Trigger**: User enters credentials and clicks the `<button type="submit">Sign In</button>`.

### 🔍 Behind the Scenes (JS Logic)
The `loginForm` listener intercepts the submit event:
```javascript
document.getElementById('loginForm').addEventListener('submit', async (e) => {
    e.preventDefault(); // Prevents page reload
    const email = document.getElementById('email').value;
    const password = document.getElementById('password').value;
    
    // FETCH REQUEST INITIATED
    const res = await fetch('/api/auth/login', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ email, password })
    });
});
```

### 📦 Initial `req` Object State
When this hits the network, the request is essentially:
*   **URL**: `POST /api/auth/login`
*   **Headers**: `Content-Type: application/json`
*   **Body**: `{ "email": "user@example.com", "password": "securepassword123" }`

---

## 🛡️ Step 2: Global Entry & Middleware (Backend)
*   **File**: [app.js](file:///c:/Users/shaik/Downloads/dojo-main/dojo-main/backend/app.js)

1.  **Body Parsing**: `app.use(express.json())` (Line 30) detects the JSON header and populates `req.body` with the email/password object.
2.  **Auth Context**: `app.use(loadUser)` (Line 55) runs. Since no token exists yet, it simply calls `next()` without attaching a user to `req.user`.

---

## 📍 Step 3: Routing
*   **File**: [app.js](file:///c:/Users/shaik/Downloads/dojo-main/dojo-main/backend/app.js) -> [authRoutes.js](file:///c:/Users/shaik/Downloads/dojo-main/dojo-main/backend/routes/authRoutes.js)

1.  In `app.js`, the request matches `app.use('/api/auth', authRoutes)`.
2.  In `authRoutes.js`, the sub-path `/login` matches `router.post('/login', login)`.
3.  The request is handed off to the `login` method in **authController.js**.

---

## 🧠 Step 4: Logic Execution (Controller)
*   **File**: [authController.js](file:///c:/Users/shaik/Downloads/dojo-main/dojo-main/backend/controllers/authController.js)
*   **Method**: `exports.login`

### 🕵️ Execution Trace:
1.  **Validation**: Checks if email/password are provided in `req.body`.
2.  **Database Lookup**: 
    ```javascript
    const user = await User.findOne({ email }).select('+password');
    ```
    *   This queries the MongoDB **Users** collection.
    *   `.select('+password')` is critical because the password field is hidden by default for security.
3.  **Password Verification**: Calls `user.matchPassword(password)`.
    *   This goes to [User.js](file:///c:/Users/shaik/Downloads/dojo-main/dojo-main/backend/models/User.js) where **bcrypt** compares the plain-text password with the hash in the DB.
4.  **Token Creation**: If matched, calls `sendTokenResponse(user, 200, res)`.

---

## 🔑 Step 5: Security & JWT
*   **File**: `authController.js` (Method: `sendTokenResponse`)

### 🔍 Token Generation Logic:
1.  **JWT Signing**: `user.getSignedJwtToken()` is called. 
    *   Payload: `{ id: user._id, role: user.role }`
    *   Key: `process.env.JWT_SECRET`
2.  **Cookie Attachment**: The token is added to a secure HTTP-Only cookie named `token`.
3.  **JSON Response**: The user data (excluding password) and the token are returned.

---

## 🏁 Step 6: Completion & Redirection (Frontend)
*   **File**: `login.ejs` (Script Block)

### 🕵️ Data Return Journey:
1.  Frontend receives `data.success: true`.
2.  **Browser Storage**: `localStorage.setItem('user', JSON.stringify(data.data))` saves user info (id, name, role) for UI usage.
3.  **RBAC Redirection**: 
    ```javascript
    if (data.data.role === 'hr' || data.data.role === 'admin') {
        window.location.href = '/api/jobs/dashboard'; // Admin/HR View
    } else {
        window.location.href = '/api/applications/my-dashboard'; // Candidate View
    }
    ```

### ✅ Final State:
*   **Server**: DB connection remains open, logs recorded.
*   **Client**: Browser now has a `token` cookie (for API Auth) and a `user` object in `localStorage` (for UI personalization).
