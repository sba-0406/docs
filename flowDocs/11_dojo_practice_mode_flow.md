# Dojo Practice Mode Flow

This flow describes the "standalone" or training mode of ScenarioSim, where users can practice leadership scenarios outside of a formal job application.

## 1. Role Selection
- **Component**: `dojo-roles.ejs`
- **Action**: User navigates to `/dojo/roles`.
- **Logic**:
    - The `dojoController.renderRolesPage` retrieves any completed sessions to show recent/best grades.
    - User selects a role (Manager, Developer, HR, Executive).
    - Clicking "Enter Simulation" calls `POST /dojo/start` with the selected role.

## 2. Session Initialization
- **Component**: `dojoController.startDojoSession`
- **Logic**:
    - Retrieves the role configuration from `simulationConfig.js`.
    - Creates a `ChatSession` with `MULTI_SCENARIO_DOJO` archetype.
    - Initializes 3 scenarios with "in-progress" or "pending" status.
    - Sets initial `worldState` (e.g., Team Morale, Trust) based on the role.
    - Redirects user to `/dojo/simulation/:sessionId`.

## 3. Interactive Simulation
- **Component**: `dojo-simulation.ejs` & `dojoController.respondToScenario`
- **Action**: User interacts with a specific stakeholder (e.g., "Angry Client").
- **Logic**:
    - **MCQ Turn**: `chatService.generateMCQOptions` provides 3 approaches (Relationship, Results, Boundary).
    - **Custom Input**: User can type free-text, which is processed by AI.
    - **World State Effects**: Chosen approaches modify metrics (e.g., +10 Trust, -5 Productivity).
    - **Progress**: Each scenario typically lasts 3-4 turns before advancing.

## 4. Scenario Advancement
- **Component**: `dojoController.nextScenario`
- **Action**: When a scenario is resolved, the system advances to the next one.
- **Logic**:
    - Marks the current scenario as "resolved".
    - Resets chat history for the new stakeholder context.
    - Updates `scenarioProgress.currentScenario`.
    - When all 3 scenarios are done, the session status moves to `completed`.

## 5. Reporting & Grading
- **Component**: `dojoController.finalizeDojoSession`
- **Logic**:
    - Calculates an `overallGrade` (S to F) using a weighted average of:
        - Technical/Skill scores.
        - Final `worldState` health (Performance Multiplier).
    - Generates an AI summary of strengths and improvements.
    - Saves the `finalReport` to the session.
    - Displays the final results to the user on the simulation screen.
