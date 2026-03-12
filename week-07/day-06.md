# 📅 Day 6: Project Day — OOP Quiz App 🎮

> **Week 7 — Advanced ES6+ & Modern JavaScript** | [NextGen Arts](https://nextgenarts.pk)

---

## ✅ Prerequisites

- All Week 7 concepts: modules, classes, Map/Set, error handling
- DOM manipulation and events (Week 6)

---

## 🎯 Today's Goals

- 🏗️ Architect a Quiz App using OOP principles
- 📦 Use ES6 classes for data models
- 🗺️ Use Map and Set for tracking state
- ✨ Build a polished, interactive UI

---

## 📘 Project: OOP Quiz App

### Architecture Overview

```
quiz-app/
├── index.html
├── style.css
├── app.js               ← Entry point
├── models/
│   ├── Question.js      ← Question class
│   └── Quiz.js          ← Quiz engine class
└── ui/
    └── QuizUI.js        ← DOM rendering class
```

### Question Model

```javascript
// models/Question.js
export default class Question {
  #options;
  #correctIndex;

  constructor(text, options, correctIndex, category = "general") {
    if (!text || !options || options.length < 2) {
      throw new Error("Invalid question data");
    }
    if (correctIndex < 0 || correctIndex >= options.length) {
      throw new RangeError("Correct index out of bounds");
    }

    this.text = text;
    this.#options = [...options];
    this.#correctIndex = correctIndex;
    this.category = category;
  }

  get options() {
    return [...this.#options]; // return copy
  }

  isCorrect(answerIndex) {
    return answerIndex === this.#correctIndex;
  }

  get correctAnswer() {
    return this.#options[this.#correctIndex];
  }
}
```

### Quiz Engine

```javascript
// models/Quiz.js
export default class Quiz {
  #questions;
  #currentIndex;
  #answers;         // Map<questionIndex, answerIndex>
  #startTime;
  #endTime;

  constructor(questions) {
    this.#questions = [...questions];
    this.#currentIndex = 0;
    this.#answers = new Map();
    this.#startTime = null;
    this.#endTime = null;
  }

  start() {
    this.#startTime = Date.now();
    this.#currentIndex = 0;
    this.#answers.clear();
    this.#endTime = null;
  }

  get currentQuestion() {
    return this.#questions[this.#currentIndex] || null;
  }

  get currentIndex() {
    return this.#currentIndex;
  }

  get totalQuestions() {
    return this.#questions.length;
  }

  get isComplete() {
    return this.#currentIndex >= this.#questions.length;
  }

  get progress() {
    return Math.round((this.#currentIndex / this.#questions.length) * 100);
  }

  answer(answerIndex) {
    if (this.isComplete) throw new Error("Quiz already complete");

    const question = this.currentQuestion;
    const isCorrect = question.isCorrect(answerIndex);

    this.#answers.set(this.#currentIndex, {
      answerIndex,
      isCorrect,
      question: question.text
    });

    this.#currentIndex++;

    if (this.isComplete) {
      this.#endTime = Date.now();
    }

    return isCorrect;
  }

  get results() {
    if (!this.isComplete) return null;

    const correct = [...this.#answers.values()].filter(a => a.isCorrect).length;
    const total = this.#questions.length;
    const percentage = Math.round((correct / total) * 100);
    const duration = Math.round((this.#endTime - this.#startTime) / 1000);

    return {
      correct,
      total,
      percentage,
      duration,
      grade: this.#calculateGrade(percentage),
      answers: [...this.#answers.entries()].map(([idx, ans]) => ({
        questionIndex: idx,
        question: ans.question,
        yourAnswer: this.#questions[idx].options[ans.answerIndex],
        correctAnswer: this.#questions[idx].correctAnswer,
        isCorrect: ans.isCorrect
      }))
    };
  }

  #calculateGrade(percentage) {
    if (percentage >= 90) return { letter: "A+", emoji: "🏆" };
    if (percentage >= 80) return { letter: "A",  emoji: "⭐" };
    if (percentage >= 70) return { letter: "B",  emoji: "👍" };
    if (percentage >= 60) return { letter: "C",  emoji: "📝" };
    if (percentage >= 50) return { letter: "D",  emoji: "😅" };
    return { letter: "F", emoji: "📚" };
  }

  shuffle() {
    for (let i = this.#questions.length - 1; i > 0; i--) {
      const j = Math.floor(Math.random() * (i + 1));
      [this.#questions[i], this.#questions[j]] = [this.#questions[j], this.#questions[i]];
    }
    return this;
  }
}
```

### Quiz UI

```javascript
// ui/QuizUI.js
export default class QuizUI {
  #container;
  #quiz;
  #onComplete;

  constructor(containerId, quiz, onComplete) {
    this.#container = document.getElementById(containerId);
    this.#quiz = quiz;
    this.#onComplete = onComplete;

    this.#container.addEventListener("click", (e) => {
      const optionBtn = e.target.closest(".option-btn");
      if (optionBtn) {
        this.#handleAnswer(Number(optionBtn.dataset.index));
      }

      if (e.target.closest(".restart-btn")) {
        this.#quiz.shuffle().start();
        this.renderQuestion();
      }
    });
  }

  renderQuestion() {
    const question = this.#quiz.currentQuestion;

    if (this.#quiz.isComplete) {
      this.renderResults();
      return;
    }

    this.#container.innerHTML = `
      <div class="quiz-card">
        <div class="quiz-header">
          <span class="quiz-progress">
            Question ${this.#quiz.currentIndex + 1} of ${this.#quiz.totalQuestions}
          </span>
          <div class="progress-bar">
            <div class="progress-fill" style="width: ${this.#quiz.progress}%"></div>
          </div>
        </div>
        <h2 class="question-text">${this.#escapeHtml(question.text)}</h2>
        <div class="options">
          ${question.options.map((opt, i) => `
            <button class="option-btn" data-index="${i}">
              <span class="option-letter">${String.fromCharCode(65 + i)}</span>
              ${this.#escapeHtml(opt)}
            </button>
          `).join("")}
        </div>
      </div>
    `;
  }

  #handleAnswer(answerIndex) {
    const buttons = this.#container.querySelectorAll(".option-btn");
    const isCorrect = this.#quiz.answer(answerIndex);

    // Highlight correct/wrong
    buttons.forEach((btn, i) => {
      btn.disabled = true;
      const question = this.#quiz.currentIndex > 0
        ? this.#quiz.results === null
          ? null
          : null
        : null;
    });

    buttons[answerIndex].classList.add(isCorrect ? "correct" : "wrong");

    // Move to next question after delay
    setTimeout(() => this.renderQuestion(), 1000);
  }

  renderResults() {
    const results = this.#quiz.results;
    if (!results) return;

    this.#container.innerHTML = `
      <div class="results-card">
        <h2>${results.grade.emoji} Quiz Complete!</h2>
        <div class="score-circle">
          <span class="score-number">${results.percentage}%</span>
          <span class="score-label">Grade: ${results.grade.letter}</span>
        </div>
        <div class="stats">
          <div class="stat">✅ Correct: ${results.correct}/${results.total}</div>
          <div class="stat">⏱️ Time: ${results.duration}s</div>
        </div>
        <div class="review">
          <h3>Review Answers</h3>
          ${results.answers.map((a, i) => `
            <div class="review-item ${a.isCorrect ? "correct" : "wrong"}">
              <p><strong>Q${i + 1}:</strong> ${this.#escapeHtml(a.question)}</p>
              <p>Your answer: ${this.#escapeHtml(a.yourAnswer)}</p>
              ${!a.isCorrect ? `<p>Correct: ${this.#escapeHtml(a.correctAnswer)}</p>` : ""}
            </div>
          `).join("")}
        </div>
        <button class="btn restart-btn">🔄 Try Again</button>
      </div>
    `;

    this.#onComplete?.(results);
  }

  #escapeHtml(text) {
    const div = document.createElement("div");
    div.textContent = text;
    return div.innerHTML;
  }
}
```

### App Entry Point

```javascript
// app.js
import Question from "./models/Question.js";
import Quiz from "./models/Quiz.js";
import QuizUI from "./ui/QuizUI.js";

const questions = [
  new Question("What does HTML stand for?",
    ["Hyper Text Markup Language", "Home Tool Markup Language", "Hyperlinks Text Mark Language", "Hyperlinking Textual Marking Language"],
    0, "html"),
  new Question("Which CSS property controls text size?",
    ["text-size", "font-size", "text-style", "font-style"],
    1, "css"),
  new Question("Which keyword declares a block-scoped variable in JS?",
    ["var", "let", "both var and let", "none"],
    1, "javascript"),
  new Question("What does CSS stand for?",
    ["Creative Style Sheets", "Cascading Style Sheets", "Computer Style Sheets", "Colorful Style Sheets"],
    1, "css"),
  new Question("Which method adds an element to the end of an array?",
    ["push()", "append()", "addEnd()", "insert()"],
    0, "javascript")
];

const quiz = new Quiz(questions);
quiz.shuffle().start();

const ui = new QuizUI("app", quiz, (results) => {
  // Save high score
  const scores = JSON.parse(localStorage.getItem("quizScores") || "[]");
  scores.push({ percentage: results.percentage, date: new Date().toISOString() });
  localStorage.setItem("quizScores", JSON.stringify(scores));
});

ui.renderQuestion();
```

---

## 📝 Homework

1. Add **categories** — let users choose which category to quiz on
2. Add a **timer** — 30 seconds per question with auto-skip
3. Add **difficulty levels** — easy, medium, hard questions with point multipliers
4. Create a **leaderboard** using localStorage to track high scores
5. Add **20+ questions** across multiple categories

---

## 💡 Pro Tips

> 🏗️ **Separation of concerns:** Data models (Question, Quiz) know nothing about the DOM. The UI class handles rendering. This makes code testable and reusable.

> 🔒 **Encapsulation:** Private fields (`#`) ensure quiz state can't be tampered with from outside. Students can't cheat by accessing `quiz.#answers`!

> 📦 **Modules matter:** Each class in its own file makes the code navigable and maintainable. This pattern scales to real-world applications.

---

[← Day 5](day-05.md) | [Back to Week 7](README.md)
