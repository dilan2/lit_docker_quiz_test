# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

LearnIT Quiz Generator - generates interactive HTML quizzes from markdown lesson files for educational courses. Quizzes are self-contained browser applications with no build step required.

## Development

**No build process.** Open any `.html` file directly in a browser to view/test.

## Key Files

- **SKILL.md** - Defines the `learnit-quiz-generator` skill: workflow, question generation rules, difficulty distribution (4-5 easy, 4-5 medium, 3-4 hard), and the complete HTML template
- **docker_quiz.html** - Example quiz implementation (Docker containerization topic)

## Quiz Data Structure

```javascript
const quizData = [
    {
        id: 1,                          // Unique sequential ID
        question: "Tekst pytania?",     // Question text (Polish)
        code: null,                     // null or multiline string with \n
        options: [
            { key: "A", text: "Option A" },
            { key: "B", text: "Option B" },
            { key: "C", text: "Option C" },
            { key: "D", text: "Option D" }
        ],
        correctAnswer: "B",             // A/B/C/D
        explanation: "Explanation...",  // 2-4 sentences (Polish)
        difficulty: 1                   // 1=Łatwe, 2=Średnie, 3=Trudne
    }
];
```

## Template Placeholders

When generating quizzes, replace these placeholders in the HTML template:
- `{{LESSON_ID}}` - e.g., "lesson-08"
- `{{LESSON_TITLE}}` - e.g., "Funkcje i idiomy"
- `{{LESSON_NUMBER}}` - e.g., "08"
- `{{QUIZ_DATA}}` - The generated quizData array

## Skill Usage

The `learnit-quiz-generator` skill is triggered by:
- "Stwórz quiz z lekcji [path]"
- "Create quiz from lesson [path]"

Output file naming: `lesson-{XX}-quiz.html` saved in same directory as source markdown.

## Language

All quiz content in Polish. Technical terms (SQL, ORM, lambda, Docker, etc.) remain in English.
