# Smart Task Scheduler with Dynamic Strategy Execution

**Author:** Megha Das
**Course:** Object-Oriented Programming — Term II
**Approval Status:** APPROVED\_MINOR (Score: 90/100)
**Submission Deadline:** 29 March 2026

---

## Problem Statement

Students and professionals often struggle to manage multiple deadlines with varying urgency and complexity. Most existing to-do applications rely on manual prioritization and do not dynamically adjust task order when conditions change. This project addresses that limitation through a structured OOP hierarchy and a strategy-based scheduling engine.

---

## Features

| Feature | Description |
|---|---|
| Task Management | Full CRUD — Create, Read, Update, Delete all task types |
| Dynamic Ordering | Runtime-switchable scheduling via the Strategy Design Pattern |
| Task Specialization | `OneTimeTask`, `RecurringTask`, `UrgentTask` through inheritance |
| Conflict Alerts | Automatic overdue/overload detection via the Observer Pattern |
| Data Persistence | JSON file-based save and load (`data/tasks.json`) |
| Input Validation | Custom exceptions for invalid dates, conflicts, and missing tasks |

---

## Project Structure

```
SmartTaskScheduler/
├── src/com/taskscheduler/
│   ├── Main.java                          # Entry point
│   ├── model/
│   │   ├── Task.java                      # Abstract base class
│   │   ├── OneTimeTask.java               # Single-occurrence task
│   │   ├── RecurringTask.java             # Repeating task with interval
│   │   └── UrgentTask.java               # High-stakes task with multiplier
│   ├── strategy/
│   │   ├── SchedulingStrategy.java        # Strategy interface
│   │   ├── DeadlineStrategy.java          # Earliest Deadline First
│   │   ├── PriorityStrategy.java          # Highest Priority First
│   │   └── UrgencyStrategy.java           # Combined Urgency Score
│   ├── observer/
│   │   ├── TaskObserver.java              # Observer interface
│   │   ├── DeadlineConflictNotifier.java  # Overdue + overload alerts
│   │   └── WorkloadMonitor.java           # Aggregate workload tracking
│   ├── manager/
│   │   └── SchedulerManager.java          # Central coordinator
│   ├── persistence/
│   │   └── TaskFileHandler.java           # JSON save/load
│   ├── exception/
│   │   ├── InvalidDateException.java
│   │   ├── DeadlineConflictException.java
│   │   └── EmptyTaskException.java
│   └── ui/
│       └── CLI.java                       # Menu-driven interface
├── docs/
│   └── ClassDiagram.png                   # UML class diagram
├── report/
│   └── ProjectReport.pdf
├── slides/
│   └── Presentation.pptx
├── data/
│   └── tasks.json                         # Auto-generated on first run
└── README.md
```

---

## OOP Concepts

| Concept | Implementation |
|---|---|
| **Abstraction** | `Task` is abstract — defines shared structure, defers `getUrgencyScore()` and `getTaskType()` to subclasses |
| **Inheritance** | `OneTimeTask`, `RecurringTask`, `UrgentTask` all extend `Task` |
| **Polymorphism** | `getUrgencyScore()` is overridden in each subclass; `UrgencyStrategy` sorts all types uniformly via this method |
| **Exception Handling** | `InvalidDateException`, `DeadlineConflictException`, `EmptyTaskException` caught throughout CLI |
| **Collections** | `ArrayList` with strategy-based sorting used in `SchedulerManager` |

---

## Design Patterns

### Strategy Pattern
`SchedulingStrategy` interface is implemented by three concrete classes. `SchedulerManager` holds a reference to the active strategy and can switch it at runtime without modifying any existing code.

```
SchedulingStrategy (interface)
    ├── DeadlineStrategy    → sort by closest deadline
    ├── PriorityStrategy    → sort by highest priority value
    └── UrgencyStrategy     → sort by getUrgencyScore() (polymorphic)
```

### Observer Pattern
`TaskObserver` interface is implemented by two concrete observers registered with `SchedulerManager`. Every CRUD operation triggers `notifyObservers()`, which calls `onTaskListUpdated()` on each observer.

```
TaskObserver (interface)
    ├── DeadlineConflictNotifier  → flags overdue tasks, upcoming deadlines, overloaded days
    └── WorkloadMonitor           → reports total pending hours and workload health status
```

---

## How to Run

### Requirements
- Java 17 or higher (uses switch expressions and records)
- No external libraries — pure standard Java

### Compile

```bash
cd SmartTaskScheduler
find src -name "*.java" > sources.txt
mkdir -p out
javac -d out @sources.txt
```

### Run

```bash
java -cp out com.taskscheduler.Main
```

### Expected startup

```
✔ Loaded 3 task(s) from data/tasks.json

╔══════════════════════════════════════╗
║   Smart Task Scheduler  v1.0         ║
╚══════════════════════════════════════╝

──────────────────────────────────────
  Active Strategy: Highest Priority First
  Tasks loaded:    3
──────────────────────────────────────
  1. Add Task
  2. View All Tasks
  3. Update Task
  4. Delete Task
  5. Mark Task Complete
  6. Switch Scheduling Strategy
  7. Exit
──────────────────────────────────────
```

---

## Edge Cases Handled

- Past date input → `InvalidDateException` with user-friendly message
- Task ID not found → `EmptyTaskException`
- Day overloaded with > 12 hours → `DeadlineConflictException` blocks the add
- Empty title or invalid priority range → `IllegalArgumentException` from model layer
- Empty or missing `tasks.json` → graceful startup with empty list

---

## Git Commit Strategy

Commits are spread across multiple sessions following this progression:

| # | Message | Layer |
|---|---|---|
| 1 | `init: project structure and README` | root |
| 2 | `feat: abstract Task class and three subclasses` | model |
| 3 | `feat: SchedulingStrategy interface and three implementations` | strategy |
| 4 | `feat: SchedulerManager with PriorityQueue and strategy switching` | manager |
| 5 | `feat: TaskObserver interface, DeadlineConflictNotifier, WorkloadMonitor` | observer |
| 6 | `feat: custom exceptions — InvalidDate, DeadlineConflict, EmptyTask` | exception |
| 7 | `feat: JSON persistence with TaskFileHandler` | persistence |
| 8 | `feat: CLI menu loop with full CRUD and input validation` | ui |
| 9 | `fix: edge case handling for recurring task scheduling` | model |
| 10 | `docs: UML class diagram and project report` | docs/report |