# Dojo Assessment Interface (`dojo-assessment.ejs`)

This is the most complex frontend file in the candidate flow. It manages the two-phase simulation: Technical MCQs and Behavioral Scenarios.

## Purpose
- Provide a focused, distraction-free environment for evaluation.
- Handle real-time state transitions between technical and behavioral tests.

## Events & Actions
### Phase 1: Technical MCQ
- **Action**: `submitMCQ(idx)`
- **Trigger**: Clicking an option button.
- **Logic**: Sends choice to `/api/assessment/submit-mcq`. If it's the last question, triggers a "Phase Complete" overlay.

### Phase 2: Behavioral Scenario
- **Action**: `submitScenarioResponse(opt)`
- **Trigger**: Clicking an AI-generated scenario option.
- **Logic**: Sends the chosen "Approach" to `/api/assessment/respond`. The UI then updates with the AI's reaction and a new set of options.
- **Turn Limit**: After 3-5 turns, the scenario resolves, and the user progresses or finishes.

### Dynamic Rendering
- **`renderMCQ(data)`**: Injects technical questions into the card.
- **`renderScenario(data)`**: Builds the chat feed and updates the AI stakeholder profile.
- **`updateMetrics(worldState)`**: Updates a miniature dashboard of metrics (Morale, Productivity, etc.) in the header during the behavioral phase.

## Modal Interactions
- **Phase Transition Overlay**: Shown between MCQ and Scenario phases to give the candidate a mental break and reset the context.
- **Finalization Overlay**: Shown after the final scenario, triggering the `finalizeAssessment` call which redirects to the dashboard.
