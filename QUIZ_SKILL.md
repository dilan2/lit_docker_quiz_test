---
name: "quiz-template"
description: "Creates universal interactive HTML quiz from JSON data. Use when user provides quiz questions or wants to create a quiz."
---

# Quiz Template Generator

## Purpose

This skill creates self-contained, interactive HTML quiz applications using the LearnIT design system. The quiz reads questions from an embedded JSON structure and provides rich feedback with per-option explanations.

## When to Use This Skill

Use this skill when the user:
- Provides quiz questions and wants an interactive quiz
- Says "Stwórz quiz" or "Create a quiz"
- Wants to generate an assessment/test for educational content
- Provides a JSON file or structure with questions

## Input Format

The user provides quiz content as JSON or describes the questions. The skill generates a complete HTML file with embedded quiz data.

## JSON Data Structure

```javascript
const quizData = {
    // Quiz metadata
    meta: {
        title: "Quiz Title",              // Quiz display title
        description: "Short description", // Brief quiz description
        course: "Course Name",            // Course/module name
        lessonNumber: "01",               // Lesson identifier
        author: "LearnIT"                 // Author/organization
    },

    // Array of questions
    questions: [
        {
            id: 1,                         // Unique question ID (sequential)
            question: "Question text?",    // Question in Polish
            code: null,                    // null or code string (use \n for newlines)
            type: "multiple",              // "multiple" (4 options) or "boolean" (true/false)
            options: [
                {
                    key: "A",
                    text: "Option A text",
                    explanation: "Why this is wrong/correct"
                },
                {
                    key: "B",
                    text: "Option B text",
                    explanation: "Why this is wrong/correct"
                },
                {
                    key: "C",
                    text: "Option C text",
                    explanation: "Why this is wrong/correct"
                },
                {
                    key: "D",
                    text: "Option D text",
                    explanation: "Why this is wrong/correct"
                }
            ],
            correctAnswer: "B",            // Key of correct answer (A/B/C/D)
            topic: "Topic Name"            // Category for scoring breakdown
        }
        // ... more questions
    ],

    // Quiz settings
    settings: {
        shuffleQuestions: false,           // Randomize question order
        showExplanationsAfterEach: false,  // Show explanation after each answer
        passingScore: 70                   // Percentage needed to pass
    }
};
```

### Code Field Examples

**Without code:**
```javascript
code: null
```

**Single line code:**
```javascript
code: "docker run -d nginx"
```

**Multiline code:**
```javascript
code: "FROM python:3.9\nWORKDIR /app\nCOPY . .\nRUN pip install -r requirements.txt\nCMD [\"python\", \"app.py\"]"
```

### Boolean Questions

For true/false questions, use only 2 options:
```javascript
{
    id: 5,
    question: "Docker containers share the host OS kernel. True or false?",
    type: "boolean",
    options: [
        { key: "A", text: "Prawda", explanation: "Correct! Containers share the host kernel, unlike VMs." },
        { key: "B", text: "Fałsz", explanation: "Incorrect. Containers DO share the host kernel." }
    ],
    correctAnswer: "A",
    topic: "Docker Basics"
}
```

## Design System

The quiz uses LearnIT brand colors and styling:

| Variable | Value | Usage |
|----------|-------|-------|
| `--color-primary` | #FFD700 | Yellow accent, buttons, highlights |
| `--color-primary-dark` | #E6C200 | Hover states |
| `--color-dark` | #1a1a1a | Primary text, dark backgrounds |
| `--color-success` | #22c55e | Correct answers |
| `--color-error` | #ef4444 | Incorrect answers |
| `--color-warning` | #f59e0b | Medium scores |
| `--font-family` | Inter | Primary font |
| `--border-radius` | 12px | Card corners |

## Quiz Features

1. **Progress Tracking** - Visual progress bar showing current position
2. **Question Navigation** - Previous/Next buttons for moving between questions
3. **Answer Selection** - Click to select, visual feedback on selection
4. **Results Screen** - Score display with percentage and correct/incorrect counts
5. **Topic Breakdown** - Scores grouped by topic with progress bars
6. **Review Mode** - Revisit all questions with correct answers highlighted
7. **Per-Option Explanations** - Each wrong answer shows why it's incorrect
8. **Restart Function** - Begin quiz again from the start

## Output File

Save the generated HTML file with a descriptive name:
- `quiz-docker-basics.html`
- `quiz-python-functions.html`
- `lesson-01-quiz.html`

## Output Behavior

**REQUIRED**:
1. Read/receive the quiz content from user
2. Generate complete HTML file with embedded quizData
3. Use the Write tool to save the file
4. Brief confirmation: "Quiz saved: filename.html (X questions)"

**FORBIDDEN**:
- NO introductory phrases before generating
- NO meta-commentary about the process
- NO closing suggestions or offers

## Pre-Output Checklist

Before saving the HTML file, verify:

- [ ] All question IDs are unique and sequential (1, 2, 3, ...)
- [ ] Each question has the correct number of options (4 for multiple, 2 for boolean)
- [ ] All options have explanations (especially wrong answers)
- [ ] correctAnswer matches one of the option keys
- [ ] Code strings use \n for line breaks (not actual newlines in JSON)
- [ ] All text is in Polish (UI and content)
- [ ] Topics are consistent across related questions
- [ ] meta section has all required fields

## Example Usage

**User input:**
```
Create a quiz about Docker volumes with 5 questions
```

**Expected actions:**
1. Generate 5 questions about Docker volumes
2. Create complete HTML with quizData
3. Save to: `quiz-docker-volumes.html`

**Final output:**
```
Quiz saved: quiz-docker-volumes.html (5 questions)
```

---

## HTML Template

The quiz-template.html file in this repository serves as the base template. When generating a quiz:

1. Copy the entire template
2. Replace the `quizData` object with generated content
3. Update `<title>` tag with quiz title
4. Save as new HTML file
