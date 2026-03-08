# Microscopic System Flows

This document provides a low-level, file-to-file view of how the most critical services in ScenarioSim operate.

---

## 1. Notification & Email Flow
This flow is triggered whenever the system needs to alert a user (e.g., Job Applied, Assessment Completed, Interview Scheduled).

### 🎬 Sequence Diagram
```mermaid
sequenceDiagram
    participant FE as "Frontend"
    participant BC as "Backend Controller"
    participant NS as "notificationService.js"
    participant ES as "emailService.js"
    participant DB as "MongoDB"
    participant SMTP as "Gmail SMTP"

    FE->>BC: "Trigger Action"
    BC->>NS: "sendNotification()"
    
    Note over NS: "populateTemplate() Logic"
    NS->>NS: "Fetch Template from DB"
    NS->>NS: "Regex Replace placeholders"

    Note over NS: "Dispatch In-App Notification"
    NS->>DB: "Notification.create()"
    Note right of DB: "Status is Unread"

    Note over NS: "Dispatch Email Notification"
    NS->>ES: "sendEmail()"
    Note over ES: "Filter non-gmail recipients"
    ES->>SMTP: "transporter.sendMail()"
    SMTP-->>ES: "Receipt ID"

    NS-->>BC: "Return Success Status"
    BC-->>FE: "HTTP 200 Success"
```

### 🔬 Low-Level Detail
*   **Entry Point**: `notificationService.js` -> `sendNotification()`
*   **Template Engine**: A custom `populateTemplate` function uses `new RegExp('{{' + key + '}}', 'g')` to perform global string replacement.
*   **Auto-Policy**: If a template belongs to the `CANDIDATE` category, `settings.sendEmail` is forced to `true` to ensure candidates always get proof of their actions.

---

## 2. PDF Parsing & Resume Matching Flow
This is the "Brain" of the application process. It turns a raw PDF into structured scores.

### 🎬 Sequence Diagram
```mermaid
sequenceDiagram
    participant FE as "Apply UI"
    participant BC as "applicationController.js"
    participant RA as "resumeAssistant.js"
    participant AI as "aiService.js"
    participant SS as "skillService.js"
    participant DB as "MongoDB"

    FE->>BC: "POST /apply with PDF"
    Note over BC: "Buffer populated by Multer"
    
    BC->>RA: "extractTextFromBuffer()"
    Note right of RA: "Uses pdf-parse engine"
    RA-->>BC: "Return Raw String"

    Note over BC: "Extract Profile & Skills"
    BC->>RA: "getAutofillData()"
    RA->>AI: "Extract Name, Email, Years"
    AI-->>RA: "JSON Structure"

    BC->>RA: "extractSkills()"
    RA->>AI: "List technical skills"
    AI-->>RA: "JSON Array"

    BC->>SS: "matchSkills()"
    Note over SS: "Jaccard similarity math"
    SS-->>BC: "Return Score 0-100"

    BC->>BC: "calculateMatchScore()"
    Note over BC: "Weighted average logic"

    BC->>DB: "Application.save()"
    BC-->>FE: "Redirect Success"
```

### 🔬 Low-Level Detail
*   **The pdf() method**: We use the `pdf-parse` library. It reads the binary buffer and traverses the PDF’s internal "Page" objects to find text streams.
*   **AI Extraction Strategy**: Instead of naive regex, we use "Prompt Engineering" to ask for specifically formatted JSON structures.
*   **Fallback**: If the AI (Groq/OpenAI) fails, it switches to `_keywordFallback()` which uses regex to scan for a hardcoded list of common tech keywords.

---

## 3. AI Question Suggestion Flow
How the system analyzes a JD to build a custom exam.

### 🎬 Sequence Diagram
```mermaid
sequenceDiagram
    participant HR as "Employer Dashboard"
    participant JC as "jobController.js"
    participant AGS as "assessmentGeneratorService.js"
    participant JD as "jdParserService.js"
    participant QB as "questionBankService.js"
    participant DB as "MongoDB"

    HR->>JC: "POST Job Description"
    JC->>AGS: "generateAssessment()"
    
    AGS->>JD: "analyzeJobDescription()"
    Note over JD: "Extract Skills, Seniority, Weights"
    JD-->>AGS: "Skills & Difficulty Level"

    AGS->>QB: "selectQuestions()"
    Note over QB: "Match items in DB storage"
    QB-->>AGS: "Technical Questions"

    AGS->>JD: "generateScenarioTemplates()"
    Note over JD: "Build tailored challenges"
    JD-->>AGS: "Scenarios & Metrics"

    AGS->>DB: "Assessment.save()"
    AGS-->>JC: "Complete Assessment Data"
    JC-->>HR: "Ready for HR Review"
```

---

## 4. Assessment Simulation (Dojo) Flow
The real-time candidate experience.

### 🎬 Sequence Diagram
```mermaid
sequenceDiagram
    participant C as "Candidate UI"
    participant AC as "assessmentController.js"
    participant CS as "chatService.js"
    participant DB as "MongoDB"

    C->>AC: "Start Assessment"
    AC->>DB: "ChatSessionInit"
    AC-->>C: "Render Examination UI"

    Note over C, AC: "Phase 1: Skill Check"
    C->>AC: "Submit MCQ Answer"
    AC->>AC: "Grade & Score"
    AC->>DB: "Update Session State"

    Note over AC: "Phase 2: Situational Dojo"
    C->>AC: "Enter Chat Message"
    AC->>CS: "analyzeTurn()"
    CS-->>AC: "Sentiment & Soft Skills"
    AC->>CS: "generateResponse()"
    CS-->>AC: "Stakeholder Reply"
    AC->>DB: "Save Chat History"

    C->>AC: "Finalize Exam"
    AC->>AC: "Weighted Match Logic"
    AC->>DB: "Save Completed Record"
```

---

## 5. Key Inbuilt Methods Used

| Method | Source | Purpose |
| :--- | :--- | :--- |
| `Buffer.from()` | Node.js Core | Converts binary file data into a format Node can manipulate. |
| `pdf(buffer)` | `pdf-parse` | The main engine that reads the PDF structure and returns serialized text. |
| `Object.entries()`| ES6/JS | Converts the data object into an array for looping. |
| `populate()` | Mongoose | Automatically joins two collections. |
| `bcrypt.compare()`| `bcryptjs` | Essential for security password check. |
