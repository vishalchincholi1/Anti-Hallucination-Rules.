# Anti-Hallucination LLM Test Generation

This repository contains guidelines and templates for using Large Language Models (LLMs) to generate software test cases with high precision and zero "hallucinations" (invented or false information).

## 📂 Contents

1.  **[Anti_Hallucination_Rules.md](./Anti_Hallucination_Rules.md)**
    *   **What it is:** The theoretical core principles.
    *   **Use it for:** Understanding *why* LLMs hallucinate and the specific constraints (like grounding, deterministic output, and critic loops) required to prevent it.

2.  **[System_Prompt_Template.md](./System_Prompt_Template.md)**
    *   **What it is:** A copy-paste ready prompt for your AI model.
    *   **Use it for:** Configuring your LLM (ChatGPT, Claude, Gemini, etc.) to act as a strict, evidence-based QA Engineer.

---

## 🚀 How to Use

Follow this workflow to generate reliable test cases:

### Step 1: Prepare Your Context
Gather the source documents that represent the "Source of Truth". This could be:
*   Product Requirement Document (PRD) text.
*   Ticket/User Story descriptions.
*   API Definitions (Swagger/OpenAPI JSON).
*   Pertinent code snippets (e.g., data models or validation logic).

### Step 2: Configure the LLM
Open your LLM interface or API playground and set up the prompt structure:

**A. Set the System Instruction:**
Copy the content from `System_Prompt_Template.md` and paste it into the "System Prompt" or "Custom Instructions" field.
*   *If using an API:* Set this as the `role: "system"` message.

**B. Set the Temperature:**
Lower the temperature setting to **0.1** or **0.2**. This forces the model to be deterministic and less "creative."

### Step 3: Construct the User Prompt
In the main chat box (User Message), structure your request like this:

> **User:**
> "Reference Context:
> [PASTE YOUR PRD / CODE / API SPEC HERE]
> 
> Task:
> Using the context above, generate positive and negative test cases for the User Login flow."

### Step 4: Verify the Output
The LLM will generate a JSON response.
1.  **Check the `source_reference` field:** Ensure every test case points to a specific part of your text.
2.  **Check the `missing_information_log`:** If the key is not empty, the LLM has correctly identified gaps in your documentation instead of guessing.
3.  **Review Placeholders:** Look for `{{UNDEFINED_REFER_TO_PM}}`. These are areas where you need to ask your Product Manager for clarification.
