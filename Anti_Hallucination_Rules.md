# Anti-Hallucination Rules for LLM Test Case Generation

This document outlines the key considerations and rules to enforce when using Large Language Models (LLMs) to generate test cases. The goal is to eliminate "hallucinations"—plausible but incorrect or non-existent scenarios, data, or system behaviors.

## 1. Strict Grounding (Source of Truth)
**Rule:** The LLM must strictly adhere to provided input context.
- **Input Requirement**: Never ask the LLM to generate tests based on a high-level topic alone (e.g., "Write tests for a login page"). Always provide the specific **PRD (Product Requirement Document)**, **User Stories**, **API Specifications (Swagger/OpenAPI)**, or **UI Wireframes/Screenshots**.
- **Negative Constraint**: Incorporate a system instruction: *"Do not invent features, buttons, code paths, or error messages not explicitly mentioned in the provided context files."*

## 2. Evidence-Based Generation
**Rule:** Every test case must map back to a specific requirement or code snippet.
- **Traceability**: Require the output to include a field (e.g., `requirement_id` or `source_reference`) linking the test case to the exact line or section in the documentation.
- **Citation**: If the LLM asserts a system behavior (e.g., "System returns 400 Error"), it must cite *where* that behavior is defined in the source.

## 3. Deterministic Output Strategy
**Rule:** Reduce "creativity" in logical flows.
- **Temperature Control**: Set the LLM temperature close to 0 (e.g., 0.1 or 0.2) to favor deterministic outputs over creative ones.
- **Schema Enforcement**: Use structured output formats (JSON/YAML) with strict schemas. This prevents the model from adding "fluff" text or imagining extra steps that look like valid prose but invalid logic.

## 4. Handling Missing Information
**Rule:** It is better to fail than to guess.
- **Explicit Fallback**: Instruct the model: *"If a testing scenario depends on information not present in the context (e.g., exact error message text, timeout duration), use a placeholder like `{{VERIFY_ERROR_MESSAGE}}` or state 'Clarification Needed' instead of inventing a value."*
- **No Unassuming Defaults**: Do not assume default behaviors (e.g., standard HTTP status codes) unless they are standard protocol or explicitly stated in general engineering guidelines provided to the bot.

## 5. Domain-Specific Constraints
**Rule:** Define boundaries for data and logic.
- **Data Types**: Explicitly define valid data formats. For example, if a phone number field is 10 digits in the code, the LLM should not "hallucinate" that it accepts international formats unless specified.
- **Mocking Reality**: When generating mock data, strictly follow the format of the provided data models/DB schema. Do not invent fields (e.g., adding a `middle_name` field to a JSON payload if the API spec only has `first_name` and `last_name`).

## 6. Review and Verification (The "Critic" Loop)
**Rule:** Trust but verify.
- **Self-Correction**: Implement a two-step process:
    1. **Generator**: Creates the test cases.
    2. **Validator**: A separate prompt/agent reviews the generated cases against the source text specifically looking for hallucinations.
- **Human-in-the-Loop**: Mark high-risk areas (e.g., compliance, security) for mandatory human review.

## 7. Scenario Scope Limits
**Rule:** Avoid scope creep.
- **Scope Definition**: Explicitly tell the LLM what is *out of scope*. (e.g., "Do not generate performance or load tests, only functional logic tests").
- **External Dependencies**: Do not hallucinate the behavior of third-party services (e.g., "PayPal returns success") unless a mock response definition is provided.

---
**Implementation Checklist:**
- [ ] System Prompt includes "No Hallucination" directive.
- [ ] Context files (PRD/Code) are loaded and indexed.
- [ ] Output schema is defined.
- [ ] Temperature is set to 0.
- [ ] "Unknown/Clarify" token is defined for missing info.
