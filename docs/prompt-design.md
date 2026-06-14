Prompt / Agent Design Logic
Project: AI Loan Sales Coach
This document explains the prompt design and agent workflow behind AI Loan Sales Coach, a prototype that helps loan telesales reps practice customer objection handling and generate structured coaching reports.
The current version is a v0.2 interactive prototype. It uses a rule-based evaluation flow to simulate the coaching logic. Future versions can replace or enhance the rule-based layer with LLM tool calling and structured outputs.
---
1. Design Goal
The goal of this project is not to make AI replace experienced loan sales reps.
Instead, the goal is to help sales reps, especially newer reps, practice the thinking process behind effective sales conversations:
What is the customer really objecting to?
What key follow-up questions should the rep ask?
Did the rep clarify the customer’s real concern?
Did the rep manage expectations honestly?
Did the rep provide a reasonable next step?
Did the rep avoid sounding too pushy?
The AI coach is designed to evaluate the quality of the sales thinking process, not just whether the wording sounds polished.
---
2. Core Product Flow
The coaching workflow follows this structure:
```text
Select practice scenario
↓
Load customer persona and objection context
↓
User enters four rounds of sales responses
↓
System evaluates whether key questions were asked
↓
System scores the conversation
↓
System generates coaching feedback
↓
System renders a structured training report
```
In the current v0.2 prototype, the user enters all four responses in a guided form.
In future versions, this can become a real-time AI role-play conversation.
---
3. Scenario Design
Current Scenario
The first MVP scenario is:
```text
Rate Objection Handling
```
The customer says:
> Your interest rate sounds too high. My friend said another bank offers a lower rate.
This scenario was selected because rate objection is common in loan telesales and is a good test case for coaching logic.
---
4. Customer Persona
The AI customer is defined by a scenario card.
Example: Rate Objection Customer Persona
```text
Customer Profile:
- 35-year-old salaried employee
- Wants to borrow NT$800,000
- Purpose: debt consolidation and personal cash flow
- Has asked another bank but has not fully confirmed the terms
- Defensive toward telesales
- Dislikes being pushed
- Says they care about interest rate
- Actually also cares about monthly payment, approved amount, and disbursement speed
```
This persona is important because it prevents the AI customer from being too easy to convince.
The customer should not immediately accept the sales rep’s explanation. The customer should keep pushing back until the rep asks useful follow-up questions.
---
5. Key Question Framework
The core coaching logic is based on whether the sales rep asks the right follow-up questions.
For the rate objection scenario, the five key questions are:
No.	Key Question	Purpose
Q1	Which bank are you comparing with?	Identify the comparison target
Q2	What interest rate did the other bank offer?	Confirm whether the customer has an actual offer
Q3	What are the loan term and monthly payment?	Avoid comparing only headline interest rate
Q4	Are you more concerned about rate, amount, monthly payment, or disbursement speed?	Identify the real decision factor
Q5	If the conditions are close, would you be willing to submit an application for comparison?	Test next-step willingness
The purpose of this framework is to shift the sales conversation from vague objection handling to structured diagnosis.
---
6. Evaluation Logic
The system evaluates each conversation using five categories:
Category	Max Score	Meaning
Emotional response	20	Did the rep acknowledge the customer’s concern?
Key questions	30	Did the rep ask the important follow-up questions?
Need clarification	20	Did the rep clarify the true concern behind the objection?
Next step	20	Did the rep propose a reasonable next action?
Natural tone	10	Did the rep avoid sounding pushy or robotic?
Total score:
```text
100 points
```
---
7. Current v0.2 Rule-Based Detection
The current prototype uses keyword-based detection to simulate coaching evaluation.
For example:
Q1: Comparison Bank
The system checks whether the user’s response includes phrases related to:
```text
哪一家
哪間
哪個銀行
比較哪家
他行
別家
```
Q2: Interest Rate
The system checks for phrases such as:
```text
利率
幾%
幾趴
年利率
對方給多少
```
Q3: Loan Term / Monthly Payment
The system checks for phrases such as:
```text
期數
幾年
幾期
月付金
月繳
每月
還款
```
Q4: Customer Priority
The system checks for phrases such as:
```text
最在意
比較在意
利率
額度
月付金
撥款速度
速度
```
Q5: Willingness to Apply
The system checks for phrases such as:
```text
送件
申請
比較看看
條件接近
願不願意
可以接受
```
This is not perfect, but it is enough for v0.2 because the goal is to demonstrate the product flow.
---
8. Prompt Design Principles
The prompt design follows six principles.
1. Role Separation
The system should separate different AI roles:
```text
Customer Simulator
Sales Coach
Question Checker
Feedback Generator
Report Renderer
```
This makes the system easier to debug and extend.
---
2. Scenario Grounding
The AI should not generate generic sales advice.
It should always be grounded in:
Customer persona
Objection type
Loan context
Key question framework
Scoring criteria
This keeps the feedback relevant to loan telesales.
---
3. Question Quality Over Pretty Wording
The coach should not only reward smooth wording.
It should evaluate whether the rep:
Asked the right diagnostic questions
Avoided jumping to conclusion
Managed customer expectations
Used the customer’s actual numbers correctly
Proposed a realistic next step
This is important because a sales script can sound good but still fail in real performance.
---
4. Expectation Management
For loan sales, overpromising is dangerous.
The AI coach should discourage vague or unrealistic promises such as:
```text
We can definitely give you a lower rate.
```
Instead, it should encourage honest expectation-setting:
```text
The final rate depends on review results. Since this is a debt consolidation case, the bank will look closely at your current debt and repayment ability. I can help you get an actual result first, so you can compare based on real numbers.
```
---
5. Structured Feedback
The final report should not be a long paragraph.
It should be structured into:
```text
Score
Key question checklist
Transcript
What went well
Missed questions
Improvement areas
Suggested rewrite
Next practice focus
```
This makes the output usable for training, review, and portfolio presentation.
---
6. Compliance-Aware Framing
The system should avoid:
Guaranteeing approval
Guaranteeing a specific interest rate
Using real customer data
Recommending misleading sales tactics
Encouraging pressure selling
The prototype is designed for training and simulation only.
---
9. Proposed Agent Architecture
The future version can be designed as a multi-step agent workflow.
```text
User Input
↓
Scenario Loader
↓
Customer Simulator
↓
Transcript Collector
↓
Key Question Checker
↓
Score Calculator
↓
Feedback Generator
↓
Report Renderer
```
---
10. Tool Use Design
In a future LLM tool-calling version, the system can use the following tools.
Tool 1: `load_scenario`
Loads the selected practice scenario.
Input
```json
{
  "scenario_id": "rate_objection"
}
```
Output
```json
{
  "scenario_name": "Rate Objection Handling",
  "customer_persona": "...",
  "opening_line": "你們利率太高了吧，我朋友說別家銀行比較低。",
  "key_questions": [...]
}
```
---
Tool 2: `check_key_questions`
Checks whether the rep asked the required questions.
Input
```json
{
  "transcript": [
    {
      "speaker": "sales",
      "message": "..."
    }
  ],
  "scenario_id": "rate_objection"
}
```
Output
```json
{
  "q1": "done",
  "q2": "missed",
  "q3": "partial",
  "q4": "done",
  "q5": "done"
}
```
---
Tool 3: `calculate_score`
Calculates the coaching score.
Input
```json
{
  "emotional_response": 14,
  "key_questions": 18,
  "need_clarification": 14,
  "next_step": 12,
  "natural_tone": 7
}
```
Output
```json
{
  "total_score": 65,
  "grade": "Needs improvement"
}
```
---
Tool 4: `generate_feedback`
Generates coaching feedback.
Input
```json
{
  "transcript": "...",
  "question_check_result": "...",
  "score": 65
}
```
Output
```json
{
  "strengths": [],
  "missed_questions": [],
  "improvement_areas": [],
  "suggested_rewrite": "",
  "next_practice_focus": ""
}
```
---
Tool 5: `render_report`
Renders the final training report.
Input
```json
{
  "scenario": "...",
  "transcript": "...",
  "score": "...",
  "feedback": "..."
}
```
Output
```json
{
  "html_report": "<html>...</html>"
}
```
---
11. Prompt Templates
11.1 Customer Simulator Prompt
```text
You are role-playing as a loan telesales customer.

Scenario:
{scenario_description}

Customer persona:
{customer_persona}

Conversation rules:
- Do not be convinced too quickly.
- React naturally like a cautious customer.
- Keep the objection realistic.
- Reveal information gradually.
- If the sales rep asks too many questions at once, show mild resistance.
- Do not give coaching feedback during the role-play.

Start with the opening line:
{opening_line}
```
---
11.2 Sales Coach Evaluation Prompt
```text
You are an AI loan sales coach.

Evaluate the sales rep's responses based on the transcript.

Scenario:
{scenario_name}

Key questions:
{key_questions}

Scoring criteria:
- Emotional response: 20 points
- Key questions: 30 points
- Need clarification: 20 points
- Next step: 20 points
- Natural tone: 10 points

Please output:
1. Total score
2. Key question checklist
3. What went well
4. Missed questions
5. Improvement areas
6. Suggested rewrite
7. Next practice focus

Important:
- Do not reward unrealistic promises.
- Penalize vague answers that avoid the customer's direct concern.
- Reward honest expectation management.
- Reward using the customer's actual loan amount and context.
```
---
11.3 Report Generation Prompt
```text
You are generating a structured coaching report for a loan telesales practice session.

Use the following data:
- Scenario
- Customer persona
- Transcript
- Key question checklist
- Score
- Feedback
- Suggested rewrite
- Next practice focus

Generate the report in a clear, structured format suitable for sales training.
```
---
12. Structured Output Schema
A future LLM version should return structured JSON before rendering HTML.
```json
{
  "scenario": {
    "id": "rate_objection",
    "name": "利率反對處理",
    "customer_profile": []
  },
  "score": {
    "total": 65,
    "breakdown": {
      "emotional_response": 14,
      "key_questions": 18,
      "need_clarification": 14,
      "next_step": 12,
      "natural_tone": 7
    }
  },
  "key_question_checklist": [
    {
      "id": "Q1",
      "question": "客戶比較的是哪一家銀行？",
      "status": "partial",
      "note": "業務有問，但客戶未透露行名。"
    }
  ],
  "feedback": {
    "strengths": [],
    "missed_questions": [],
    "improvement_areas": [],
    "suggested_rewrite": "",
    "next_practice_focus": ""
  }
}
```
---
13. Limitations of Current Version
The current v0.2 version has several limitations:
It uses rule-based keyword detection, not true semantic understanding.
It only supports one scenario: rate objection handling.
The customer responses are fixed rather than dynamically generated.
The scoring logic is simplified.
The report is generated from predefined logic rather than a full LLM reasoning pipeline.
These limitations are intentional for MVP development.
The purpose of v0.2 is to validate:
```text
Can the user input responses and receive a useful coaching report?
```
---
14. Future Improvements
v0.3: LLM Feedback Layer
Replace static feedback with LLM-generated coaching suggestions.
v0.4: Multi-Scenario Training
Add more scenarios:
Customer says they need to think about it
Customer refuses to provide documents
Customer compares with another bank
Customer is cold and impatient
Customer asks whether sending the case will affect credit
v0.5: Real-Time Role Play
Allow the AI customer to dynamically respond to each sales reply.
v1.0: Agent + Tool Calling Version
Use tool calling to separate:
Scenario loading
Objection classification
Key question checking
Score calculation
Feedback generation
Report rendering
---
15. Portfolio Positioning
This project demonstrates the ability to:
Translate real-world sales experience into a structured AI workflow
Design scenario-based AI coaching logic
Build a key-question evaluation framework
Create a rule-based MVP before implementing advanced AI
Plan a future Agent / Tool Use architecture
Package the result as a usable training product
This project is not just a sales script generator.
It is a prototype of an AI-assisted sales training workflow.
