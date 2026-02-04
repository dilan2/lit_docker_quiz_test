---
name: "learnit-quiz-generator"
description: "Generates interactive HTML quiz from markdown lesson file. Use when user wants quiz from lesson."
---

# LearnIT Quiz Generator

## Purpose

This skill generates interactive HTML quiz applications from markdown lesson files for LearnIT educational courses. It analyzes the lesson content and creates 10-15 questions covering key topics, with proper difficulty distribution.

## When to Use This Skill

Use this skill when the user:
- Provides a markdown lesson file and wants a quiz
- Says "Stwórz quiz z lekcji" or "Wygeneruj quiz"
- Says "Create quiz from lesson" or "Generate quiz"
- Mentions creating a quiz for a specific lesson file

## Input Format

The user provides a path to a markdown lesson file. Example:
- `Work/LearnIT/Products/Books/DataScience/Lekcja 08 - Funkcje i idiomy.md`
- `@"Work/LearnIT/Products/Books/Python/Lekcja 05 - Listy.md"`

## Workflow

### Step 1: Read the Markdown File
Use the Read tool to load the complete lesson content.

### Step 2: Extract Lesson Metadata
From the filename or content, extract:
- **Lesson number**: e.g., "Lekcja 08" → `08`
- **Lesson title**: e.g., "Funkcje i idiomy"

Naming convention for output file: `lesson-{XX}-quiz.html` (saved in same directory as source)

### Step 3: Analyze Content
Identify key topics from the lesson:
- Main concepts and definitions
- Code examples that can be used for questions
- Important terms and their explanations
- Practical scenarios mentioned

### Step 4: Generate Questions
Create 10-15 questions following the distribution rules below.

### Step 5: Build quizData Array
Structure questions in the JavaScript format specified below.

### Step 6: Generate HTML
Replace placeholders in the HTML template with actual values.

### Step 7: Save File
Write the complete HTML file to the same directory as the source markdown.

---

## Question Generation Rules

### Quantity and Difficulty Distribution

| Difficulty | Polish Label | Count | Description |
|------------|-------------|-------|-------------|
| 1 | Łatwe | 4-5 | Basic definitions, terms, simple facts |
| 2 | Średnie | 4-5 | Understanding concepts, connections between ideas |
| 3 | Trudne | 3-4 | Code analysis, finding errors, practical cases |

**Total: 10-15 questions**

### Question Types

1. **Definitions** — Term and concept definitions
2. **Syntax Selection** — Choosing correct syntax/function
3. **Code Analysis** — What will this code output?
4. **Error Finding** — Find the bug in this code
5. **Comparison** — Advantages/disadvantages of approaches
6. **Practical Scenarios** — Real-world application cases

### Question Requirements

- Each question has **4 options (A, B, C, D)**
- Only **one correct answer**
- Wrong answers must be **plausible** (not absurd)
- Questions must **cover all key topics** from the material
- For code questions, use the `code` field (string with `\n` for line breaks)
- Each question must have a **detailed explanation** (2-4 sentences)

### Language

- All text in **Polish**
- Technical terms can remain in English (SQL, ORM, JOIN, lambda, etc.)

---

## quizData Structure

```javascript
const quizData = [
    {
        id: 1,                          // Unique ID (number)
        question: "Tekst pytania?",     // Question in Polish
        code: null,                      // null or string with code (use \n for newlines)
        options: [
            { key: "A", text: "Wariant A" },
            { key: "B", text: "Wariant B" },
            { key: "C", text: "Wariant C" },
            { key: "D", text: "Wariant D" }
        ],
        correctAnswer: "B",             // Correct answer key (A/B/C/D)
        explanation: "Wyjaśnienie...",  // Detailed explanation in Polish
        difficulty: 1                   // 1 = Łatwe, 2 = Średnie, 3 = Trudne
    },
    // ... more questions
];
```

### Code Field Examples

**Without code:**
```javascript
code: null
```

**With code (single line):**
```javascript
code: "print('Hello World')"
```

**With code (multiline):**
```javascript
code: "def greet(name):\n    return f'Hello, {name}!'\n\nresult = greet('Anna')\nprint(result)"
```

---

## HTML Template

Replace the following placeholders in the template:
- `{{LESSON_ID}}` — Lesson identifier (e.g., "lesson-08")
- `{{LESSON_TITLE}}` — Lesson title in Polish (e.g., "Funkcje i idiomy")
- `{{LESSON_NUMBER}}` — Lesson number (e.g., "08")
- `{{QUIZ_DATA}}` — The generated quizData array

```html
<!DOCTYPE html>
<html lang="pl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Quiz: {{LESSON_TITLE}} | LearnIT</title>

    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700;800&display=swap" rel="stylesheet">

    <!-- Prism.js Syntax Highlighting -->
    <link href="https://cdnjs.cloudflare.com/ajax/libs/prism/1.29.0/themes/prism-tomorrow.min.css" rel="stylesheet">
    <script src="https://cdnjs.cloudflare.com/ajax/libs/prism/1.29.0/prism.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/prism/1.29.0/components/prism-python.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/prism/1.29.0/components/prism-sql.min.js"></script>

    <style>
        :root {
            --color-primary: #FFD700;
            --color-primary-dark: #E6C200;
            --color-dark: #1a1a1a;
            --color-dark-secondary: #2d2d2d;
            --color-gray: #6b7280;
            --color-gray-light: #f3f4f6;
            --color-white: #ffffff;
            --color-success: #22c55e;
            --color-error: #ef4444;
            --color-warning: #f59e0b;
            --color-info: #3b82f6;
            --font-family: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
            --border-radius: 12px;
        }

        * { margin: 0; padding: 0; box-sizing: border-box; }
        html { font-size: 16px; }
        body {
            font-family: var(--font-family);
            line-height: 1.7;
            color: var(--color-dark);
            background: var(--color-white);
            min-height: 100vh;
        }

        .container { max-width: 800px; margin: 0 auto; padding: 0 20px; }

        .page-header {
            background: var(--color-dark);
            color: var(--color-white);
            padding: 15px 30px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin: 40px 0 30px 0;
            border-radius: var(--border-radius);
        }

        .header-info { display: flex; align-items: center; gap: 15px; }

        .header-badge {
            background: var(--color-primary);
            color: var(--color-dark);
            padding: 4px 12px;
            border-radius: 20px;
            font-size: 0.75rem;
            font-weight: 600;
            text-transform: uppercase;
            letter-spacing: 0.5px;
        }

        .header-title { font-size: 0.9rem; color: #a1a1aa; }

        .progress-section { margin-bottom: 30px; }

        .progress-info {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 10px;
        }

        .progress-counter { font-size: 0.9rem; color: var(--color-gray); }
        .progress-counter strong { color: var(--color-dark); font-weight: 700; }

        .difficulty-indicator { display: flex; align-items: center; gap: 8px; }

        .difficulty-label {
            font-size: 0.8rem;
            color: var(--color-gray);
            text-transform: uppercase;
            letter-spacing: 0.5px;
        }

        .difficulty-dots { display: flex; gap: 4px; }

        .difficulty-dot {
            width: 10px;
            height: 10px;
            border-radius: 50%;
            background: #e5e5e5;
            transition: all 0.2s ease;
        }

        .difficulty-dot.active { background: var(--color-primary); }

        .progress-bar {
            height: 8px;
            background: var(--color-gray-light);
            border-radius: 4px;
            overflow: hidden;
        }

        .progress-fill {
            height: 100%;
            background: var(--color-primary);
            border-radius: 4px;
            transition: width 0.4s ease;
        }

        .quiz-card {
            background: var(--color-white);
            border: 2px solid var(--color-gray-light);
            border-radius: var(--border-radius);
            padding: 40px;
            margin-bottom: 30px;
        }

        .question-number {
            display: inline-block;
            background: var(--color-primary);
            color: var(--color-dark);
            padding: 6px 14px;
            border-radius: 20px;
            font-size: 0.8rem;
            font-weight: 700;
            margin-bottom: 20px;
        }

        .question-text {
            font-size: 1.25rem;
            font-weight: 600;
            color: var(--color-dark);
            line-height: 1.5;
            margin-bottom: 20px;
        }

        .question-code {
            background: var(--color-dark);
            color: #e5e5e5;
            padding: 20px;
            border-radius: var(--border-radius);
            overflow-x: auto;
            margin-bottom: 25px;
            font-family: 'Fira Code', 'Monaco', 'Consolas', monospace;
            font-size: 0.9rem;
            line-height: 1.5;
        }

        .options-list { display: flex; flex-direction: column; gap: 12px; margin-bottom: 30px; }

        .option-item {
            display: flex;
            align-items: flex-start;
            gap: 15px;
            padding: 18px 20px;
            background: var(--color-gray-light);
            border: 2px solid transparent;
            border-radius: var(--border-radius);
            cursor: pointer;
            transition: all 0.2s ease;
        }

        .option-item:hover { background: #e8e8e8; border-color: var(--color-gray); }
        .option-item.selected { background: #fffbeb; border-color: var(--color-primary); }

        .option-key {
            width: 32px;
            height: 32px;
            display: flex;
            align-items: center;
            justify-content: center;
            background: var(--color-white);
            border: 2px solid var(--color-gray);
            border-radius: 8px;
            font-weight: 700;
            font-size: 0.9rem;
            color: var(--color-dark);
            flex-shrink: 0;
            transition: all 0.2s ease;
        }

        .option-item.selected .option-key {
            background: var(--color-primary);
            border-color: var(--color-primary);
            color: var(--color-dark);
        }

        .option-text { font-size: 1rem; line-height: 1.5; padding-top: 4px; color: var(--color-dark); }

        .btn {
            display: inline-flex;
            align-items: center;
            justify-content: center;
            gap: 8px;
            padding: 16px 40px;
            font-size: 1rem;
            font-weight: 600;
            border: none;
            border-radius: var(--border-radius);
            cursor: pointer;
            transition: all 0.2s ease;
            font-family: inherit;
            text-decoration: none;
        }

        .btn-primary { background: var(--color-primary); color: var(--color-dark); width: 100%; }

        .btn-primary:hover:not(:disabled) {
            background: var(--color-primary-dark);
            transform: translateY(-2px);
            box-shadow: 0 4px 12px rgba(255, 215, 0, 0.3);
        }

        .btn-primary:disabled { opacity: 0.5; cursor: not-allowed; transform: none; box-shadow: none; }

        .btn-secondary { background: var(--color-dark); color: var(--color-white); width: 100%; }
        .btn-secondary:hover { background: var(--color-dark-secondary); transform: translateY(-2px); }

        .results-screen { display: none; }
        .results-screen.active { display: block; }
        .quiz-screen.hidden { display: none; }

        .results-header {
            background: linear-gradient(135deg, var(--color-dark) 0%, var(--color-dark-secondary) 100%);
            color: var(--color-white);
            padding: 50px 40px;
            border-radius: var(--border-radius);
            text-align: center;
            margin-bottom: 30px;
            position: relative;
            overflow: hidden;
        }

        .results-header::before {
            content: '';
            position: absolute;
            top: -50%;
            right: -10%;
            width: 200px;
            height: 200px;
            background: var(--color-primary);
            opacity: 0.1;
            border-radius: 50%;
        }

        .results-badge {
            display: inline-block;
            background: var(--color-primary);
            color: var(--color-dark);
            padding: 6px 16px;
            border-radius: 20px;
            font-size: 0.8rem;
            font-weight: 700;
            text-transform: uppercase;
            letter-spacing: 1px;
            margin-bottom: 20px;
        }

        .results-score { font-size: 4rem; font-weight: 800; margin-bottom: 10px; line-height: 1; }
        .results-score.score-high { color: var(--color-success); }
        .results-score.score-medium { color: var(--color-primary); }
        .results-score.score-low { color: var(--color-error); }

        .results-label { font-size: 1.1rem; color: #a1a1aa; margin-bottom: 30px; }

        .results-stats { display: flex; justify-content: center; gap: 50px; }
        .stat-item { text-align: center; }
        .stat-value { font-size: 2rem; font-weight: 800; color: var(--color-white); }
        .stat-label { font-size: 0.75rem; color: #71717a; text-transform: uppercase; letter-spacing: 1px; }

        .review-section { margin-bottom: 30px; }

        .review-title {
            font-size: 1.5rem;
            font-weight: 700;
            color: var(--color-dark);
            margin-bottom: 20px;
            padding-bottom: 10px;
            border-bottom: 3px solid var(--color-primary);
        }

        .review-list { display: flex; flex-direction: column; gap: 20px; }

        .review-item {
            background: var(--color-white);
            border: 2px solid var(--color-gray-light);
            border-radius: var(--border-radius);
            padding: 25px;
            transition: all 0.2s ease;
        }

        .review-item.correct { border-left: 4px solid var(--color-success); }
        .review-item.incorrect { border-left: 4px solid var(--color-error); }

        .review-header { display: flex; align-items: flex-start; gap: 15px; margin-bottom: 15px; }

        .review-status {
            width: 32px;
            height: 32px;
            display: flex;
            align-items: center;
            justify-content: center;
            border-radius: 50%;
            font-size: 1rem;
            font-weight: 700;
            flex-shrink: 0;
        }

        .review-status.correct { background: #f0fdf4; color: var(--color-success); }
        .review-status.incorrect { background: #fef2f2; color: var(--color-error); }

        .review-question { font-weight: 600; font-size: 1rem; line-height: 1.5; color: var(--color-dark); }

        .review-answers { margin-left: 47px; margin-bottom: 15px; }

        .review-answer {
            font-size: 0.9rem;
            padding: 8px 0;
            color: var(--color-gray);
            display: flex;
            align-items: center;
            gap: 8px;
        }

        .review-answer-icon { font-size: 0.8rem; }
        .review-answer.user-correct { color: var(--color-success); font-weight: 500; }
        .review-answer.user-incorrect { color: var(--color-error); font-weight: 500; }
        .review-answer.correct-answer { color: var(--color-success); font-weight: 500; }

        .review-explanation {
            margin-left: 47px;
            padding: 15px 20px;
            background: #fefce8;
            border-left: 4px solid var(--color-primary);
            border-radius: 0 var(--border-radius) var(--border-radius) 0;
        }

        .explanation-title {
            font-size: 0.75rem;
            font-weight: 700;
            color: #a16207;
            text-transform: uppercase;
            letter-spacing: 1px;
            margin-bottom: 8px;
            display: flex;
            align-items: center;
            gap: 6px;
        }

        .explanation-text { font-size: 0.9rem; color: var(--color-dark); line-height: 1.6; }

        .document-footer {
            background: var(--color-dark);
            color: var(--color-white);
            padding: 30px;
            border-radius: var(--border-radius);
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin: 50px 0 40px 0;
            flex-wrap: wrap;
            gap: 20px;
        }

        .footer-logo { display: flex; align-items: center; }
        .footer-contacts { display: flex; gap: 25px; flex-wrap: wrap; }

        .footer-contact { display: flex; align-items: center; gap: 8px; font-size: 0.9rem; }
        .footer-contact a { color: var(--color-white); text-decoration: none; transition: color 0.2s ease; }
        .footer-contact a:hover { color: var(--color-primary); }

        @media (max-width: 600px) {
            .container { padding: 0 15px; }
            .page-header { flex-direction: column; gap: 10px; text-align: center; padding: 20px; }
            .quiz-card { padding: 25px 20px; }
            .question-text { font-size: 1.1rem; }
            .option-item { padding: 15px; }
            .results-header { padding: 35px 25px; }
            .results-score { font-size: 3rem; }
            .results-stats { gap: 30px; }
            .review-answers, .review-explanation { margin-left: 0; }
            .document-footer { flex-direction: column; text-align: center; }
            .footer-contacts { justify-content: center; }
        }

        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(15px); }
            to { opacity: 1; transform: translateY(0); }
        }

        .quiz-card { animation: fadeIn 0.4s ease; }
    </style>
</head>
<body>
    <div class="container">
        <header class="page-header">
            <svg width="100" height="17" viewBox="0 0 159 26" fill="none" xmlns="http://www.w3.org/2000/svg"><g clip-path="url(#clip0_header)"><path d="M149.406 23.7717C149.096 23.7717 148.681 23.6701 148.474 23.3652C147.956 22.8571 147.956 22.0441 148.474 21.5359L155.93 14.2188L148.474 6.90179C147.956 6.39366 147.956 5.58063 148.474 5.0725C148.992 4.56437 149.82 4.56437 150.338 5.0725L158.726 13.3042C158.933 13.5075 159.14 13.914 159.14 14.2188C159.14 14.5237 159.037 14.9302 158.726 15.1335L150.338 23.3652C150.131 23.5684 149.717 23.7717 149.406 23.7717Z" fill="#FFDA17"/><path d="M12.1147 23.7717C11.804 23.7717 11.3898 23.6701 11.1827 23.3652L2.79506 15.1335C2.58796 14.9302 2.38086 14.5237 2.38086 14.2188C2.38086 13.914 2.4844 13.5075 2.79506 13.3042L11.1827 5.0725C11.7005 4.56437 12.5289 4.56437 13.0467 5.0725C13.5644 5.58063 13.5644 6.39366 13.0467 6.90179L5.59095 14.2188L13.0467 21.5359C13.5644 22.0441 13.5644 22.8571 13.0467 23.3652C12.8396 23.5684 12.4254 23.7717 12.1147 23.7717Z" fill="#FFDA17"/><path d="M131.284 23.7713C130.973 23.7713 130.766 23.6697 130.559 23.568C129.938 23.1615 129.834 22.3485 130.249 21.7388L142.053 5.27536C142.468 4.6656 143.296 4.56395 143.917 4.97046C144.539 5.37696 144.642 6.18999 144.228 6.79975L132.423 23.2631C132.112 23.568 131.698 23.7713 131.284 23.7713Z" fill="#FFDA17"/><path d="M128.388 2.5C128.762 2.5 129.064 2.6245 129.295 2.87351C129.544 3.10473 129.669 3.4071 129.669 3.78061C129.669 4.15412 129.544 4.46538 129.295 4.71438C129.064 4.9456 128.762 5.06121 128.388 5.06121H122.652V21.9492C122.652 22.3405 122.519 22.6696 122.252 22.9364C121.985 23.2031 121.656 23.3365 121.265 23.3365C120.873 23.3365 120.544 23.2031 120.278 22.9364C120.011 22.6696 119.877 22.3405 119.877 21.9492V5.06121H114.115C113.741 5.06121 113.43 4.9456 113.181 4.71438C112.95 4.46538 112.834 4.15412 112.834 3.78061C112.834 3.4071 112.95 3.10473 113.181 2.87351C113.43 2.6245 113.741 2.5 114.115 2.5H128.388Z" fill="#FFFFFF"/><path d="M107 3.88732C107 3.47824 107.133 3.1492 107.4 2.90019C107.667 2.6334 107.996 2.5 108.387 2.5C108.796 2.5 109.125 2.6334 109.374 2.90019C109.641 3.1492 109.775 3.47824 109.775 3.88732V21.9492C109.775 22.3405 109.641 22.6696 109.374 22.9364C109.125 23.2031 108.796 23.3365 108.387 23.3365C107.996 23.3365 107.667 23.2031 107.4 22.9364C107.133 22.6696 107 22.3405 107 21.9492V3.88732Z" fill="#FFFFFF"/><path d="M93.1347 8.6095C94.362 8.6095 95.4647 8.87629 96.4429 9.40988C97.4212 9.94346 98.186 10.7261 98.7374 11.7577C99.2887 12.7715 99.5644 13.9987 99.5644 15.4394V22.0025C99.5644 22.3938 99.431 22.7229 99.1642 22.9896C98.9152 23.2387 98.5951 23.3632 98.2038 23.3632C97.8125 23.3632 97.4834 23.2387 97.2166 22.9896C96.9676 22.7229 96.8431 22.3938 96.8431 22.0025V15.4394C96.8431 13.9987 96.4518 12.9138 95.6692 12.1845C94.9044 11.4375 93.8817 11.064 92.6011 11.064C91.8541 11.064 91.1693 11.2152 90.5468 11.5175C89.9421 11.8199 89.4618 12.229 89.1061 12.7448C88.7682 13.2606 88.5992 13.8386 88.5992 14.4789V22.0025C88.5992 22.3938 88.4747 22.7229 88.2257 22.9896C87.9767 23.2387 87.6477 23.3632 87.2386 23.3632C86.8473 23.3632 86.5182 23.2387 86.2514 22.9896C86.0024 22.7229 85.8779 22.3938 85.8779 22.0025V10.1035C85.8779 9.69446 86.0024 9.36541 86.2514 9.1164C86.5182 8.8674 86.8473 8.74289 87.2386 8.74289C87.6477 8.74289 87.9767 8.8674 88.2257 9.1164C88.4747 9.36541 88.5992 9.69446 88.5992 10.1035V10.397C89.1684 9.82785 89.8443 9.39209 90.6268 9.08972C91.4094 8.76957 92.2454 8.6095 93.1347 8.6095Z" fill="#FFFFFF"/><path d="M80.0534 8.58289C80.996 8.58289 81.7342 8.72518 82.2678 9.00976C82.8013 9.29433 83.0681 9.65006 83.0681 10.0769C83.0681 10.2014 83.0592 10.2904 83.0415 10.3437C82.8814 10.9129 82.5257 11.1975 81.9743 11.1975C81.8854 11.1975 81.752 11.1797 81.5741 11.1441C80.8627 11.0196 80.2846 10.9573 79.8399 10.9573C78.5593 10.9573 77.5277 11.2508 76.7451 11.8378C75.9625 12.4247 75.5713 13.2162 75.5713 14.2122V22.0026C75.5713 22.4295 75.4556 22.7585 75.2244 22.9897C75.011 23.2209 74.6819 23.3365 74.2373 23.3365C73.8104 23.3365 73.4814 23.2298 73.2502 23.0164C73.0189 22.7852 72.9033 22.4472 72.9033 22.0026V10.0769C72.9033 9.65006 73.0189 9.32101 73.2502 9.08979C73.4814 8.85857 73.8104 8.74296 74.2373 8.74296C75.1266 8.74296 75.5713 9.18762 75.5713 10.0769V10.5838C76.1048 9.96132 76.7629 9.4722 77.5455 9.11647C78.3281 8.76075 79.1641 8.58289 80.0534 8.58289Z" fill="#FFFFFF"/><path d="M61.5158 8.63623C62.8854 8.63623 64.1215 8.96527 65.2243 9.62336C66.3448 10.2637 67.2252 11.153 67.8655 12.2913C68.5236 13.4118 68.8526 14.6658 68.8526 16.0531V22.0026C68.8526 22.3939 68.7193 22.7229 68.4525 22.9897C68.2035 23.2387 67.8833 23.3632 67.492 23.3632C67.1007 23.3632 66.7717 23.2387 66.5049 22.9897C66.2559 22.7229 66.1314 22.3939 66.1314 22.0026V21.0154C65.5088 21.7802 64.7529 22.3761 63.8636 22.8029C62.9743 23.2298 62.0139 23.4432 60.9823 23.4432C59.7016 23.4432 58.5366 23.1231 57.4873 22.4828C56.4557 21.8425 55.6375 20.9621 55.0328 19.8415C54.4458 18.7032 54.1523 17.4404 54.1523 16.0531C54.1523 14.6658 54.4725 13.4118 55.1128 12.2913C55.7531 11.153 56.6335 10.2637 57.7541 9.62336C58.8924 8.96527 60.1463 8.63623 61.5158 8.63623ZM61.5158 21.0421C62.4051 21.0421 63.2055 20.8287 63.917 20.4018C64.6462 19.9572 65.2154 19.3613 65.6245 18.6143C66.0335 17.8495 66.2381 16.9957 66.2381 16.0531C66.2381 15.1104 66.0335 14.2567 65.6245 13.4919C65.2154 12.7271 64.6462 12.1312 63.917 11.7044C63.2055 11.2597 62.4051 11.0374 61.5158 11.0374C60.6265 11.0374 59.8173 11.2597 59.088 11.7044C58.3766 12.1312 57.8074 12.7271 57.3805 13.4919C56.9715 14.2567 56.7669 15.1104 56.7669 16.0531C56.7669 16.9957 56.9715 17.8495 57.3805 18.6143C57.8074 19.3613 58.3766 19.9572 59.088 20.4018C59.8173 20.8287 60.6265 21.0421 61.5158 21.0421Z" fill="#FFFFFF"/><path d="M51.9501 15.813C51.9501 16.1509 51.8345 16.4355 51.6033 16.6667C51.372 16.8801 51.0786 16.9869 50.7228 16.9869H40.5847C40.7803 18.2319 41.3406 19.2368 42.2655 20.0016C43.2082 20.7664 44.3554 21.1488 45.7071 21.1488C46.2407 21.1488 46.7921 21.051 47.3612 20.8554C47.9482 20.6597 48.4284 20.4196 48.8019 20.135C49.0509 19.9394 49.3444 19.8415 49.6823 19.8415C50.0203 19.8415 50.2871 19.9305 50.4827 20.1083C50.8029 20.3751 50.9629 20.6775 50.9629 21.0154C50.9629 21.3356 50.8207 21.6024 50.5361 21.8158C49.9313 22.296 49.1754 22.6873 48.2683 22.9897C47.379 23.2921 46.5253 23.4432 45.7071 23.4432C44.2487 23.4432 42.9414 23.132 41.7853 22.5095C40.6292 21.8692 39.7221 20.9888 39.064 19.8682C38.4237 18.7477 38.1035 17.476 38.1035 16.0531C38.1035 14.6302 38.4059 13.3585 39.0106 12.2379C39.6331 11.0996 40.4869 10.2192 41.5718 9.59668C42.6746 8.95638 43.9196 8.63623 45.3069 8.63623C46.6765 8.63623 47.8593 8.9386 48.8553 9.54333C49.8513 10.1481 50.6161 10.9929 51.1497 12.0779C51.6833 13.1628 51.9501 14.4079 51.9501 15.813ZM45.3069 10.9307C44.0085 10.9307 42.9503 11.2864 42.1321 11.9978C41.3317 12.7093 40.8248 13.6608 40.6114 14.8525H49.4956C49.3355 13.6608 48.8909 12.7093 48.1616 11.9978C47.4324 11.2864 46.4808 10.9307 45.3069 10.9307Z" fill="#FFFFFF"/><path d="M34.4059 20.5619C34.7972 20.5619 35.1262 20.6953 35.393 20.9621C35.6598 21.2289 35.7932 21.5579 35.7932 21.9492C35.7932 22.3405 35.6598 22.6696 35.393 22.9364C35.1262 23.2031 34.7972 23.3365 34.4059 23.3365H23.3606C22.9693 23.3365 22.6403 23.2031 22.3735 22.9364C22.1245 22.6696 22 22.3405 22 21.9492V3.88732C22 3.49603 22.1334 3.16698 22.4002 2.90019C22.667 2.6334 22.9871 2.5 23.3606 2.5C23.7519 2.5 24.081 2.6334 24.3478 2.90019C24.6146 3.16698 24.748 3.49603 24.748 3.88732V20.5619H34.4059Z" fill="#FFFFFF"/></g><defs><clipPath id="clip0_header"><rect width="159" height="25" fill="white" transform="translate(0 0.5)"/></clipPath></defs></svg>
            <div class="header-info">
                <span class="header-badge">Quiz</span>
                <span class="header-title">Lekcja {{LESSON_NUMBER}}: {{LESSON_TITLE}}</span>
            </div>
        </header>

        <div class="quiz-screen" id="quizScreen">
            <div class="progress-section">
                <div class="progress-info">
                    <span class="progress-counter">Pytanie <strong id="currentNum">1</strong> z <strong id="totalNum">12</strong></span>
                    <div class="difficulty-indicator">
                        <span class="difficulty-label" id="difficultyLabel">Łatwe</span>
                        <div class="difficulty-dots">
                            <div class="difficulty-dot active" id="dot1"></div>
                            <div class="difficulty-dot" id="dot2"></div>
                            <div class="difficulty-dot" id="dot3"></div>
                        </div>
                    </div>
                </div>
                <div class="progress-bar">
                    <div class="progress-fill" id="progressFill" style="width: 0%"></div>
                </div>
            </div>

            <div class="quiz-card">
                <span class="question-number" id="questionNumber">Pytanie 1</span>
                <p class="question-text" id="questionText"></p>
                <pre class="question-code" id="questionCode" style="display: none;"><code class="language-python"></code></pre>
                <div class="options-list" id="optionsContainer"></div>
                <button class="btn btn-primary" id="nextBtn" disabled>Dalej →</button>
            </div>
        </div>

        <div class="results-screen" id="resultsScreen">
            <div class="results-header">
                <span class="results-badge">Wynik testu</span>
                <div class="results-score" id="scoreValue">0%</div>
                <div class="results-label">Twój wynik</div>
                <div class="results-stats">
                    <div class="stat-item">
                        <div class="stat-value" id="correctCount">0</div>
                        <div class="stat-label">Poprawnych</div>
                    </div>
                    <div class="stat-item">
                        <div class="stat-value" id="incorrectCount">0</div>
                        <div class="stat-label">Błędnych</div>
                    </div>
                </div>
            </div>

            <div class="review-section">
                <h2 class="review-title">Przegląd odpowiedzi</h2>
                <div class="review-list" id="reviewContainer"></div>
            </div>

            <button class="btn btn-secondary" id="retryBtn">← Spróbuj ponownie</button>
        </div>

        <footer class="document-footer">
            <div class="footer-logo">
                <svg width="100" height="17" viewBox="0 0 159 26" fill="none" xmlns="http://www.w3.org/2000/svg"><g clip-path="url(#clip0_footer)"><path d="M149.406 23.7717C149.096 23.7717 148.681 23.6701 148.474 23.3652C147.956 22.8571 147.956 22.0441 148.474 21.5359L155.93 14.2188L148.474 6.90179C147.956 6.39366 147.956 5.58063 148.474 5.0725C148.992 4.56437 149.82 4.56437 150.338 5.0725L158.726 13.3042C158.933 13.5075 159.14 13.914 159.14 14.2188C159.14 14.5237 159.037 14.9302 158.726 15.1335L150.338 23.3652C150.131 23.5684 149.717 23.7717 149.406 23.7717Z" fill="#FFDA17"/><path d="M12.1147 23.7717C11.804 23.7717 11.3898 23.6701 11.1827 23.3652L2.79506 15.1335C2.58796 14.9302 2.38086 14.5237 2.38086 14.2188C2.38086 13.914 2.4844 13.5075 2.79506 13.3042L11.1827 5.0725C11.7005 4.56437 12.5289 4.56437 13.0467 5.0725C13.5644 5.58063 13.5644 6.39366 13.0467 6.90179L5.59095 14.2188L13.0467 21.5359C13.5644 22.0441 13.5644 22.8571 13.0467 23.3652C12.8396 23.5684 12.4254 23.7717 12.1147 23.7717Z" fill="#FFDA17"/><path d="M131.284 23.7713C130.973 23.7713 130.766 23.6697 130.559 23.568C129.938 23.1615 129.834 22.3485 130.249 21.7388L142.053 5.27536C142.468 4.6656 143.296 4.56395 143.917 4.97046C144.539 5.37696 144.642 6.18999 144.228 6.79975L132.423 23.2631C132.112 23.568 131.698 23.7713 131.284 23.7713Z" fill="#FFDA17"/><path d="M128.388 2.5C128.762 2.5 129.064 2.6245 129.295 2.87351C129.544 3.10473 129.669 3.4071 129.669 3.78061C129.669 4.15412 129.544 4.46538 129.295 4.71438C129.064 4.9456 128.762 5.06121 128.388 5.06121H122.652V21.9492C122.652 22.3405 122.519 22.6696 122.252 22.9364C121.985 23.2031 121.656 23.3365 121.265 23.3365C120.873 23.3365 120.544 23.2031 120.278 22.9364C120.011 22.6696 119.877 22.3405 119.877 21.9492V5.06121H114.115C113.741 5.06121 113.43 4.9456 113.181 4.71438C112.95 4.46538 112.834 4.15412 112.834 3.78061C112.834 3.4071 112.95 3.10473 113.181 2.87351C113.43 2.6245 113.741 2.5 114.115 2.5H128.388Z" fill="#FFFFFF"/><path d="M107 3.88732C107 3.47824 107.133 3.1492 107.4 2.90019C107.667 2.6334 107.996 2.5 108.387 2.5C108.796 2.5 109.125 2.6334 109.374 2.90019C109.641 3.1492 109.775 3.47824 109.775 3.88732V21.9492C109.775 22.3405 109.641 22.6696 109.374 22.9364C109.125 23.2031 108.796 23.3365 108.387 23.3365C107.996 23.3365 107.667 23.2031 107.4 22.9364C107.133 22.6696 107 22.3405 107 21.9492V3.88732Z" fill="#FFFFFF"/><path d="M93.1347 8.6095C94.362 8.6095 95.4647 8.87629 96.4429 9.40988C97.4212 9.94346 98.186 10.7261 98.7374 11.7577C99.2887 12.7715 99.5644 13.9987 99.5644 15.4394V22.0025C99.5644 22.3938 99.431 22.7229 99.1642 22.9896C98.9152 23.2387 98.5951 23.3632 98.2038 23.3632C97.8125 23.3632 97.4834 23.2387 97.2166 22.9896C96.9676 22.7229 96.8431 22.3938 96.8431 22.0025V15.4394C96.8431 13.9987 96.4518 12.9138 95.6692 12.1845C94.9044 11.4375 93.8817 11.064 92.6011 11.064C91.8541 11.064 91.1693 11.2152 90.5468 11.5175C89.9421 11.8199 89.4618 12.229 89.1061 12.7448C88.7682 13.2606 88.5992 13.8386 88.5992 14.4789V22.0025C88.5992 22.3938 88.4747 22.7229 88.2257 22.9896C87.9767 23.2387 87.6477 23.3632 87.2386 23.3632C86.8473 23.3632 86.5182 23.2387 86.2514 22.9896C86.0024 22.7229 85.8779 22.3938 85.8779 22.0025V10.1035C85.8779 9.69446 86.0024 9.36541 86.2514 9.1164C86.5182 8.8674 86.8473 8.74289 87.2386 8.74289C87.6477 8.74289 87.9767 8.8674 88.2257 9.1164C88.4747 9.36541 88.5992 9.69446 88.5992 10.1035V10.397C89.1684 9.82785 89.8443 9.39209 90.6268 9.08972C91.4094 8.76957 92.2454 8.6095 93.1347 8.6095Z" fill="#FFFFFF"/><path d="M80.0534 8.58289C80.996 8.58289 81.7342 8.72518 82.2678 9.00976C82.8013 9.29433 83.0681 9.65006 83.0681 10.0769C83.0681 10.2014 83.0592 10.2904 83.0415 10.3437C82.8814 10.9129 82.5257 11.1975 81.9743 11.1975C81.8854 11.1975 81.752 11.1797 81.5741 11.1441C80.8627 11.0196 80.2846 10.9573 79.8399 10.9573C78.5593 10.9573 77.5277 11.2508 76.7451 11.8378C75.9625 12.4247 75.5713 13.2162 75.5713 14.2122V22.0026C75.5713 22.4295 75.4556 22.7585 75.2244 22.9897C75.011 23.2209 74.6819 23.3365 74.2373 23.3365C73.8104 23.3365 73.4814 23.2298 73.2502 23.0164C73.0189 22.7852 72.9033 22.4472 72.9033 22.0026V10.0769C72.9033 9.65006 73.0189 9.32101 73.2502 9.08979C73.4814 8.85857 73.8104 8.74296 74.2373 8.74296C75.1266 8.74296 75.5713 9.18762 75.5713 10.0769V10.5838C76.1048 9.96132 76.7629 9.4722 77.5455 9.11647C78.3281 8.76075 79.1641 8.58289 80.0534 8.58289Z" fill="#FFFFFF"/><path d="M61.5158 8.63623C62.8854 8.63623 64.1215 8.96527 65.2243 9.62336C66.3448 10.2637 67.2252 11.153 67.8655 12.2913C68.5236 13.4118 68.8526 14.6658 68.8526 16.0531V22.0026C68.8526 22.3939 68.7193 22.7229 68.4525 22.9897C68.2035 23.2387 67.8833 23.3632 67.492 23.3632C67.1007 23.3632 66.7717 23.2387 66.5049 22.9897C66.2559 22.7229 66.1314 22.3939 66.1314 22.0026V21.0154C65.5088 21.7802 64.7529 22.3761 63.8636 22.8029C62.9743 23.2298 62.0139 23.4432 60.9823 23.4432C59.7016 23.4432 58.5366 23.1231 57.4873 22.4828C56.4557 21.8425 55.6375 20.9621 55.0328 19.8415C54.4458 18.7032 54.1523 17.4404 54.1523 16.0531C54.1523 14.6658 54.4725 13.4118 55.1128 12.2913C55.7531 11.153 56.6335 10.2637 57.7541 9.62336C58.8924 8.96527 60.1463 8.63623 61.5158 8.63623ZM61.5158 21.0421C62.4051 21.0421 63.2055 20.8287 63.917 20.4018C64.6462 19.9572 65.2154 19.3613 65.6245 18.6143C66.0335 17.8495 66.2381 16.9957 66.2381 16.0531C66.2381 15.1104 66.0335 14.2567 65.6245 13.4919C65.2154 12.7271 64.6462 12.1312 63.917 11.7044C63.2055 11.2597 62.4051 11.0374 61.5158 11.0374C60.6265 11.0374 59.8173 11.2597 59.088 11.7044C58.3766 12.1312 57.8074 12.7271 57.3805 13.4919C56.9715 14.2567 56.7669 15.1104 56.7669 16.0531C56.7669 16.9957 56.9715 17.8495 57.3805 18.6143C57.8074 19.3613 58.3766 19.9572 59.088 20.4018C59.8173 20.8287 60.6265 21.0421 61.5158 21.0421Z" fill="#FFFFFF"/><path d="M51.9501 15.813C51.9501 16.1509 51.8345 16.4355 51.6033 16.6667C51.372 16.8801 51.0786 16.9869 50.7228 16.9869H40.5847C40.7803 18.2319 41.3406 19.2368 42.2655 20.0016C43.2082 20.7664 44.3554 21.1488 45.7071 21.1488C46.2407 21.1488 46.7921 21.051 47.3612 20.8554C47.9482 20.6597 48.4284 20.4196 48.8019 20.135C49.0509 19.9394 49.3444 19.8415 49.6823 19.8415C50.0203 19.8415 50.2871 19.9305 50.4827 20.1083C50.8029 20.3751 50.9629 20.6775 50.9629 21.0154C50.9629 21.3356 50.8207 21.6024 50.5361 21.8158C49.9313 22.296 49.1754 22.6873 48.2683 22.9897C47.379 23.2921 46.5253 23.4432 45.7071 23.4432C44.2487 23.4432 42.9414 23.132 41.7853 22.5095C40.6292 21.8692 39.7221 20.9888 39.064 19.8682C38.4237 18.7477 38.1035 17.476 38.1035 16.0531C38.1035 14.6302 38.4059 13.3585 39.0106 12.2379C39.6331 11.0996 40.4869 10.2192 41.5718 9.59668C42.6746 8.95638 43.9196 8.63623 45.3069 8.63623C46.6765 8.63623 47.8593 8.9386 48.8553 9.54333C49.8513 10.1481 50.6161 10.9929 51.1497 12.0779C51.6833 13.1628 51.9501 14.4079 51.9501 15.813ZM45.3069 10.9307C44.0085 10.9307 42.9503 11.2864 42.1321 11.9978C41.3317 12.7093 40.8248 13.6608 40.6114 14.8525H49.4956C49.3355 13.6608 48.8909 12.7093 48.1616 11.9978C47.4324 11.2864 46.4808 10.9307 45.3069 10.9307Z" fill="#FFFFFF"/><path d="M34.4059 20.5619C34.7972 20.5619 35.1262 20.6953 35.393 20.9621C35.6598 21.2289 35.7932 21.5579 35.7932 21.9492C35.7932 22.3405 35.6598 22.6696 35.393 22.9364C35.1262 23.2031 34.7972 23.3365 34.4059 23.3365H23.3606C22.9693 23.3365 22.6403 23.2031 22.3735 22.9364C22.1245 22.6696 22 22.3405 22 21.9492V3.88732C22 3.49603 22.1334 3.16698 22.4002 2.90019C22.667 2.6334 22.9871 2.5 23.3606 2.5C23.7519 2.5 24.081 2.6334 24.3478 2.90019C24.6146 3.16698 24.748 3.49603 24.748 3.88732V20.5619H34.4059Z" fill="#FFFFFF"/></g><defs><clipPath id="clip0_footer"><rect width="159" height="25" fill="white" transform="translate(0 0.5)"/></clipPath></defs></svg>
                <span style="margin-left: 5px; font-size: 0.8rem; color: var(--color-gray);">sp. z o.o.</span>
            </div>
            <div class="footer-contacts">
                <div class="footer-contact"><span>📞</span><a href="tel:+48573580990">+48 573-580-990</a></div>
                <div class="footer-contact"><span>🌐</span><a href="https://learnit.com.pl">learnit.com.pl</a></div>
            </div>
        </footer>
    </div>

    <script>
        // ==========================================
        // QUIZ DATA - Generated from lesson content
        // ==========================================
        {{QUIZ_DATA}}

        // ==========================================
        // QUIZ LOGIC (DO NOT MODIFY)
        // ==========================================
        const sortedQuestions = [...quizData].sort((a, b) => a.difficulty - b.difficulty);

        let currentQuestionIndex = 0;
        let selectedAnswer = null;
        let quizResults = {
            lessonId: "{{LESSON_ID}}",
            startedAt: new Date().toISOString(),
            completedAt: null,
            totalQuestions: sortedQuestions.length,
            correctAnswers: 0,
            scorePercent: 0,
            answers: []
        };

        const quizScreen = document.getElementById('quizScreen');
        const resultsScreen = document.getElementById('resultsScreen');
        const questionText = document.getElementById('questionText');
        const questionCode = document.getElementById('questionCode');
        const optionsContainer = document.getElementById('optionsContainer');
        const nextBtn = document.getElementById('nextBtn');
        const currentNumEl = document.getElementById('currentNum');
        const totalNumEl = document.getElementById('totalNum');
        const progressFill = document.getElementById('progressFill');
        const difficultyLabel = document.getElementById('difficultyLabel');
        const questionNumber = document.getElementById('questionNumber');

        function init() {
            totalNumEl.textContent = sortedQuestions.length;
            renderQuestion();
            nextBtn.addEventListener('click', handleNext);
            document.getElementById('retryBtn').addEventListener('click', restartQuiz);
        }

        function renderQuestion() {
            const q = sortedQuestions[currentQuestionIndex];
            selectedAnswer = null;
            nextBtn.disabled = true;

            currentNumEl.textContent = currentQuestionIndex + 1;
            progressFill.style.width = (currentQuestionIndex / sortedQuestions.length) * 100 + '%';
            questionNumber.textContent = `Pytanie ${currentQuestionIndex + 1}`;
            updateDifficultyIndicator(q.difficulty);
            nextBtn.textContent = currentQuestionIndex === sortedQuestions.length - 1 ? 'Zakończ test →' : 'Dalej →';

            questionText.textContent = q.question;

            if (q.code) {
                questionCode.style.display = 'block';
                questionCode.querySelector('code').textContent = q.code;
                Prism.highlightElement(questionCode.querySelector('code'));
            } else {
                questionCode.style.display = 'none';
            }

            optionsContainer.innerHTML = '';
            q.options.forEach(option => {
                const optionEl = document.createElement('div');
                optionEl.className = 'option-item';
                optionEl.dataset.key = option.key;
                optionEl.innerHTML = `<div class="option-key">${option.key}</div><div class="option-text">${option.text}</div>`;
                optionEl.addEventListener('click', () => selectOption(option.key));
                optionsContainer.appendChild(optionEl);
            });
        }

        function updateDifficultyIndicator(difficulty) {
            const labels = ['Łatwe', 'Średnie', 'Trudne'];
            difficultyLabel.textContent = labels[difficulty - 1];
            document.getElementById('dot1').classList.toggle('active', difficulty >= 1);
            document.getElementById('dot2').classList.toggle('active', difficulty >= 2);
            document.getElementById('dot3').classList.toggle('active', difficulty >= 3);
        }

        function selectOption(key) {
            selectedAnswer = key;
            nextBtn.disabled = false;
            document.querySelectorAll('.option-item').forEach(opt => {
                opt.classList.toggle('selected', opt.dataset.key === key);
            });
        }

        function handleNext() {
            if (!selectedAnswer) return;

            const q = sortedQuestions[currentQuestionIndex];
            const isCorrect = selectedAnswer === q.correctAnswer;

            quizResults.answers.push({
                questionId: q.id,
                question: q.question,
                selectedAnswer: selectedAnswer,
                correctAnswer: q.correctAnswer,
                isCorrect: isCorrect
            });

            if (isCorrect) quizResults.correctAnswers++;
            currentQuestionIndex++;

            if (currentQuestionIndex < sortedQuestions.length) {
                renderQuestion();
            } else {
                showResults();
            }
        }

        function showResults() {
            quizResults.completedAt = new Date().toISOString();
            quizResults.scorePercent = Math.round((quizResults.correctAnswers / quizResults.totalQuestions) * 100);

            quizScreen.classList.add('hidden');
            resultsScreen.classList.add('active');

            const scoreEl = document.getElementById('scoreValue');
            scoreEl.textContent = quizResults.scorePercent + '%';
            scoreEl.className = 'results-score';
            if (quizResults.scorePercent >= 80) scoreEl.classList.add('score-high');
            else if (quizResults.scorePercent >= 50) scoreEl.classList.add('score-medium');
            else scoreEl.classList.add('score-low');

            document.getElementById('correctCount').textContent = quizResults.correctAnswers;
            document.getElementById('incorrectCount').textContent = quizResults.totalQuestions - quizResults.correctAnswers;

            const reviewContainer = document.getElementById('reviewContainer');
            reviewContainer.innerHTML = '';

            quizResults.answers.forEach((answer, index) => {
                const q = sortedQuestions[index];
                const userOption = q.options.find(o => o.key === answer.selectedAnswer);
                const correctOption = q.options.find(o => o.key === answer.correctAnswer);

                const reviewItem = document.createElement('div');
                reviewItem.className = `review-item ${answer.isCorrect ? 'correct' : 'incorrect'}`;
                reviewItem.innerHTML = `
                    <div class="review-header">
                        <div class="review-status ${answer.isCorrect ? 'correct' : 'incorrect'}">${answer.isCorrect ? '✓' : '✗'}</div>
                        <div class="review-question">${index + 1}. ${answer.question}</div>
                    </div>
                    <div class="review-answers">
                        <div class="review-answer ${answer.isCorrect ? 'user-correct' : 'user-incorrect'}">
                            <span class="review-answer-icon">${answer.isCorrect ? '✓' : '✗'}</span>
                            <span>Twoja odpowiedź: <strong>${answer.selectedAnswer}.</strong> ${userOption.text}</span>
                        </div>
                        ${!answer.isCorrect ? `<div class="review-answer correct-answer"><span class="review-answer-icon">✓</span><span>Poprawna odpowiedź: <strong>${answer.correctAnswer}.</strong> ${correctOption.text}</span></div>` : ''}
                    </div>
                    <div class="review-explanation">
                        <div class="explanation-title">💡 Wyjaśnienie</div>
                        <div class="explanation-text">${q.explanation}</div>
                    </div>
                `;
                reviewContainer.appendChild(reviewItem);
            });

            console.log('Quiz Results:', quizResults);
        }

        function restartQuiz() {
            currentQuestionIndex = 0;
            selectedAnswer = null;
            quizResults = {
                lessonId: "{{LESSON_ID}}",
                startedAt: new Date().toISOString(),
                completedAt: null,
                totalQuestions: sortedQuestions.length,
                correctAnswers: 0,
                scorePercent: 0,
                answers: []
            };

            resultsScreen.classList.remove('active');
            quizScreen.classList.remove('hidden');
            renderQuestion();
        }

        init();
    </script>
</body>
</html>
```

---

## Output Behavior

**ABSOLUTELY CRITICAL - NO EXCEPTIONS**:

When generating a quiz, your output must be ONLY the actions to create the HTML file. No commentary.

**FORBIDDEN**:
- ❌ NO introductory phrases like "Now I'll generate..." or "Based on the lesson..."
- ❌ NO explanatory text before generating questions
- ❌ NO meta-commentary about what you're doing
- ❌ NO closing suggestions or offers
- ❌ NO discussion about the questions after generation

**REQUIRED**:
- ✅ Read the lesson file FIRST
- ✅ Generate complete quizData array with 10-15 questions
- ✅ Use the Write tool to save complete HTML file
- ✅ Save to same directory as source markdown
- ✅ Brief confirmation message at the end (e.g., "Quiz saved to: lesson-08-quiz.html")

**Example of WRONG workflow**:
```
Now I'll analyze the lesson about functions and create a quiz...
Based on the content, I see several key topics...
Let me generate questions covering these areas...
[generates quiz]
The quiz has been created! You can open it in your browser...
```

**Example of CORRECT workflow**:
```
[Read tool: reads the markdown file]
[Write tool: saves lesson-08-quiz.html]
Quiz saved: Work/LearnIT/Products/Books/DataScience/lesson-08-quiz.html (12 questions)
```

---

## Pre-Output Checklist

Before saving the HTML file, verify:

- [ ] Questions cover all key topics from the material
- [ ] Difficulty distribution: 4-5 easy, 4-5 medium, 3-4 hard
- [ ] All text is in Polish
- [ ] Each question has exactly 4 options (A/B/C/D)
- [ ] Wrong answers are plausible (not absurd)
- [ ] Explanations are informative (2-4 sentences)
- [ ] Code in questions is syntactically correct
- [ ] Question IDs are unique and sequential (1, 2, 3, ...)
- [ ] All placeholders replaced with actual values

---

## Example Usage

**User input:**
```
Stwórz quiz z lekcji: Work/LearnIT/Products/Books/DataScience/Lekcja 08 - Funkcje i idiomy.md
```

**Expected actions:**
1. Read the markdown file
2. Extract: lesson number = "08", title = "Funkcje i idiomy"
3. Generate 12 questions covering: functions, lambda, list comprehensions, dict/set comprehensions
4. Create HTML with quizData and replaced placeholders
5. Save to: `Work/LearnIT/Products/Books/DataScience/lesson-08-quiz.html`

**Final output:**
```
Quiz saved: Work/LearnIT/Products/Books/DataScience/lesson-08-quiz.html (12 questions)
```
