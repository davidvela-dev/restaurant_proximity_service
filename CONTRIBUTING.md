# Contributing Guidelines

To maintain code quality, consistency, and keep the `main` branch stable, we have the following settings:

<br>

## 1- Branching Strategy & Main Protection

Direct pushes to the `main` branch are disabled due to **Branch Protection Rules**. All changes must go through a Feature Branch and a Pull Request (PR).

### 1.1- Main Branch Protection

- Require a pull request before merging from another branch
- Require status checks to pass before merging to main
- Require branches to be up to date from main before merging
- Status checks: build_and_test
- Do not allow bypassing the above settings

### 1.2- Feature branches

- Create a branch from `main` using a descriptive name:
- Have up to date from main before uploading branch

### 1.3- Formatting Before Execution

- Automatic formatting `dart format .`
- Static analysis `flutter analyze`
- Unit Tests `flutter test`

<br>

## 2- Code Conventions

### 2.1- Naming and Style

- **Files:** `snake_case` for source code files
- **Classes and Types:** `UpperCamelCase` for class names, enums, and extensions
- **Variables and Methods:** `lowerCamelCase` for variables, parameters, and methods
- **Immutability:** Use of `const` and `final` keywords to optimize performance

### 2.2- Structure and Architecture (Layer-First)

- **Separation of Concerns:** `views/` don't contain direct business logic or queries, all logic is delegated to `viewmodels/` or `services/`
- **Components:** Complex or repetitive UI elements are modeled to be reused in `widgets/`
- **Data Models:** Classes in `models/` must have safe constructors and methods to interact with SQLite 