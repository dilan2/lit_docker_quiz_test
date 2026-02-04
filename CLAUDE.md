# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

LearnIT Quiz Generator - generates interactive HTML quizzes for educational courses. Quizzes are self-contained browser applications with no build step required.

## Development

**No build process.** Open any `.html` file directly in a browser to view/test.

## Two Quiz Systems

This repo has two quiz generation skills with different data structures:

### 1. `learnit-quiz-generator` (SKILL.md)
Generates quizzes from markdown lesson files with difficulty levels.
- Triggered by: "Stwórz quiz z lekcji [path]"
- Output: `lesson-{XX}-quiz.html`
- Features: difficulty indicator (Łatwe/Średnie/Trudne), single explanation per question

### 2. `quiz-template` (QUIZ_SKILL.md)
Creates quizzes from JSON with per-option explanations.
- Triggered by: "Stwórz quiz" or "Create a quiz"
- Output: `quiz-{topic}.html`
- Features: email collection, localStorage progress saving, topic-based score breakdown, explanation for each option

## Quiz Data Structures

### learnit-quiz-generator (simple array)
```javascript
const quizData = [
    {
        id: 1,
        question: "Tekst pytania?",
        code: null,                     // or multiline string with \n
        options: [
            { key: "A", text: "Option A" },
            { key: "B", text: "Option B" },
            { key: "C", text: "Option C" },
            { key: "D", text: "Option D" }
        ],
        correctAnswer: "B",
        explanation: "Single explanation...",
        difficulty: 1                   // 1=Łatwe, 2=Średnie, 3=Trudne
    }
];
```

### quiz-template (object with metadata)
```javascript
const quizData = {
    meta: {
        title: "Quiz Title",
        description: "Description",
        course: "Course Name",
        lessonNumber: "01",
        author: "LearnIT"
    },
    questions: [
        {
            id: 1,
            question: "Tekst pytania?",
            code: null,
            type: "multiple",           // or "boolean"
            options: [
                { key: "A", text: "...", explanation: "Why wrong/correct" },
                { key: "B", text: "...", explanation: "Why wrong/correct" },
                { key: "C", text: "...", explanation: "Why wrong/correct" },
                { key: "D", text: "...", explanation: "Why wrong/correct" }
            ],
            correctAnswer: "B",
            topic: "Topic Name"         // for score breakdown
        }
    ],
    settings: {
        shuffleQuestions: false,
        passingScore: 70
    }
};
```

## Key Files

| File | Purpose |
|------|---------|
| `SKILL.md` | learnit-quiz-generator skill definition + HTML template |
| `QUIZ_SKILL.md` | quiz-template skill definition |
| `quiz-template.html` | Base template for quiz-template skill |
| `docker_quiz.html` | Example quiz (learnit-quiz-generator format) |
| `base copy.html` | LearnIT design system reference |
| `book/` | Markdown lesson files |

## Design System (LearnIT brand)

```css
--color-primary: #FFD700;      /* Yellow accent */
--color-primary-dark: #E6C200; /* Hover states */
--color-dark: #1a1a1a;         /* Text, dark backgrounds */
--color-success: #22c55e;      /* Correct answers */
--color-error: #ef4444;        /* Incorrect answers */
--font-family: 'Inter';
--border-radius: 12px;
```

## Language

All quiz content in Polish. Technical terms remain in English.

## LocalStorage (quiz-template only)

Progress saved as compact indices under key `quiz_progress_{sanitized_title}`. Auto-expires after 7 days.
