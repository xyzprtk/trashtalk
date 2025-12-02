
# Contributing to TrashTalk

Thank you for your interest in contributing to TrashTalk. This document outlines the standards and procedures for contributing to the codebase. We value your help in improving this satirical content generator.

## Getting Started

### Prerequisites
Before you begin, ensure you have the following installed:
*   Node.js (v18.17.0 or higher)
*   npm (or yarn/pnpm)
*   Git

You will also need accounts with:
*   Google Cloud Platform (for Gemini API)
*   Supabase (for database functionality)

### Local Development Setup

1.  **Fork the Repository**
    Click the "Fork" button on the top right corner of the repository page to create your own copy of the project.

2.  **Clone Your Fork**
    ```bash
    git clone https://github.com/xyzprtk/trashtalk.git
    cd trashtalk
    ```

3.  **Install Dependencies**
    ```bash
    npm install
    ```

4.  **Environment Variables**
    Create a `.env.local` file in the root directory. You must populate the following keys for the application to function:
    ```env
    GEMINI_API_KEY=your_key_here
    NEXT_PUBLIC_SUPABASE_URL=your_url_here
    NEXT_PUBLIC_SUPABASE_ANON_KEY=your_key_here
    SUPABASE_SERVICE_ROLE_KEY=your_key_here
    ```

5.  **Database Setup**
    Refer to the README.md file for the specific SQL commands required to set up the `global_stats` table and necessary RPC functions in Supabase. The app will not function without these database elements.

6.  **Run the Development Server**
    ```bash
    npm run dev
    ```
    The application will be available at http://localhost:3000.

## Development Workflow

1.  **Create a Branch**
    Create a new branch for your specific feature or bug fix. Use a descriptive name.
    ```bash
    git checkout -b feature/your-feature-name
    # or
    git checkout -b fix/issue-description
    ```

2.  **Make Changes**
    Implement your code. Ensure you are following the coding standards outlined below.

3.  **Lint and Format**
    Run the linter to catch any issues before committing.
    ```bash
    npm run lint
    ```

4.  **Commit Changes**
    Write clear, concise commit messages using the imperative mood (e.g., "Add tone selection" not "Added tone selection").
    ```bash
    git commit -m "Description of changes"
    ```

5.  **Push to Your Fork**
    ```bash
    git push origin your-branch-name
    ```

6.  **Submit a Pull Request**
    Open a Pull Request (PR) from your branch to the main repository's `main` branch. Provide a clear description of what you changed and link to any relevant issues.

## Coding Standards

### TypeScript
*   Strict type checking is enabled. Do not use `any` unless absolutely necessary.
*   Define interfaces for all data structures, especially API responses and component props.
*   Place types and interfaces in the relevant component file or in a dedicated file in `lib/` if reused.

### React and Next.js
*   Use Functional Components with Hooks.
*   Use the Next.js App Router structure (`app/`) correctly.
*   Server Components should be the default. Use `"use client"` directive only when user interaction or state management is required.
*   Keep components small and reusable.

### Styling (Tailwind CSS)
*   Use Tailwind CSS utility classes for styling.
*   Use the `cn()` utility function (from `lib/utils.ts`) when conditionally merging class names.
*   Follow the existing color variables defined in `globals.css` to maintain theme compatibility (dark/light mode).

### API Routes
*   All API routes must handle errors gracefully and return appropriate HTTP status codes.
*   Ensure sensitive logic (like direct database writes requiring service roles) stays in server-side code.

## Project Structure Guide

*   **app/**: Contains page logic and API routes.
*   **components/ui/**: Contains primitive UI elements (buttons, inputs). Do not modify these unless fixing a bug in the base system.
*   **components/**: Contains complex, application-specific components (e.g., `generator-dashboard.tsx`).
*   **lib/**: Contains utility functions and external service configurations.
*   **hooks/**: Contains custom React hooks for state and logic reuse.

## Reporting Issues

If you find a bug or have a suggestion, please open an Issue in the repository.

### Bug Reports
Include the following information:
*   A clear title describing the bug.
*   Steps to reproduce the issue.
*   Expected behavior.
*   Actual behavior.
*   Screenshots (if applicable).
*   Browser and Operating System details.

### Feature Requests
*   Clearly describe the feature you want to add.
*   Explain why this feature is valuable to the project.
*   If possible, outline a proposed implementation strategy.

## Code of Conduct

Please be respectful and professional in all communications. We are building a fun, chaotic app, but our collaboration process should be constructive and inclusive.
