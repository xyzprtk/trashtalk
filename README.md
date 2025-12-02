TrashTalk is a web application designed to generate satirical, humorous, and "unhinged" Twitter posts (tweets) using Artificial Intelligence. Built with Next.js and TypeScript, the application allows users to select specific personas or tones, generating content that mimics internet culture, tech buzzwords, or absurdist humor.

The project features a modern, responsive user interface, real-time global statistics, and anonymous user tracking without requiring account registration.

## Features

### Content Generation
*   **AI-Powered Engine:** Utilizes Google's Gemini API to generate unique content based on complex system prompts.
*   **Tone Selection:** Users can choose from five distinct personalities:
    *   **Gen Z Burnout:** Existential dread mixed with internet slang.
    *   **Tech Bro Manifesto:** Satire of hustle culture and startup jargon.
    *   **Corporate Cringe:** Excessive use of business buzzwords.
    *   **Absurdist Nihilism:** Philosophical and chaotic randomness.
    *   **Anime Lord:** Over-the-top references to anime tropes.
*   **Custom Prompts:** Users can inject specific topics or instructions to guide the AI generation.
*   **Fallback System:** Includes a client-side fallback mechanism with pre-written posts to ensure functionality even if the AI API experiences downtime.

### Dashboard and Analytics
*   **Real-Time Global Counter:** Displays the total number of tweets generated across all users globally, synchronized in real-time using Supabase.
*   **Session Statistics:** Tracks the user's current session activity, calculating "Chaos Levels" and "Viral Potential" based on engagement.
*   **History Management:** Stores the last five generated posts locally, allowing users to review and copy previous content.
*   **Gamification:** Features visual metrics like "Sanity Remaining" and "Chaos Meter" that react to user activity.

### User Privacy and Identity
*   **Anonymous Fingerprinting:** Utilizes a custom fingerprinting algorithm (combining canvas data, screen resolution, and user agent) to identify unique visitors without requiring email, login, or cookies.
*   **Privacy-First Architecture:** Generated content is stateless and is not stored on the server. User preferences are stored in the browser's LocalStorage.

### Technical UI/UX
*   **Responsive Design:** Fully mobile-optimized interface.
*   **Theme Support:** Includes a robust Dark/Light mode toggle that persists user preference.
*   **Interactive Components:** Features animated cards, toast notifications, and dynamic charts using Tailwind CSS and Radix UI primitives.

## Technology Stack

*   **Framework:** Next.js 15 (App Router)
*   **Language:** TypeScript
*   **Styling:** Tailwind CSS, Tailwind Merge, Class Variance Authority
*   **UI Components:** Shadcn UI (based on Radix UI)
*   **Backend/Database:** Supabase (PostgreSQL)
*   **AI Model:** Google Gemini (via `generativelanguage.googleapis.com`)
*   **State Management:** React Hooks (Custom hooks for global counts and visitor tracking)
*   **Icons:** Lucide React

## Project Structure

The project follows the standard Next.js App Router structure:

*   **`app/`**: Contains the application routes, pages, and API endpoints.
    *   **`api/`**: Server-side route handlers for content generation, health checks, and database interactions.
    *   **`(pages)`**: Specific routes for Generate, How It Works, Privacy, Terms, and Contact.
*   **`components/`**: Reusable UI components.
    *   **`ui/`**: Base design system components (buttons, cards, inputs).
    *   **`generator-dashboard.tsx`**: The main interface for the application logic.
    *   **`tweet-card.tsx`**: Component for displaying generated content.
*   **`hooks/`**: Custom React hooks.
    *   **`use-global-tweet-count.ts`**: Manages the real-time synchronization of the global tweet counter.
    *   **`use-user-identification.ts`**: Handles the creation and retrieval of anonymous user IDs.
    *   **`use-visitor-tracking.ts`**: Interfaces with the API to track unique site visitors.
*   **`lib/`**: Utility functions and configuration.
    *   **`supabase.ts`**: Client-side Supabase configuration.
    *   **`supabaseServer.ts`**: Server-side Supabase configuration (for API routes).
    *   **`user-identification.ts`**: Logic for browser fingerprinting.

## Getting Started

### Prerequisites

*   Node.js (v18.17.0 or higher)
*   npm, yarn, or pnpm
*   A Supabase project
*   A Google Cloud project with the Gemini API enabled

### Installation

1.  **Clone the repository:**
    ```bash
    git clone https://github.com/xyzprtk/trashtalk.git
    cd trashtalk
    ```

2.  **Install dependencies:**
    ```bash
    npm install
    ```

3.  **Environment Configuration:**
    Create a `.env.local` file in the root directory and add the following variables:

    ```env
    GEMINI_API_KEY=your_key_here
    NEXT_PUBLIC_SUPABASE_URL=your_url_here
    NEXT_PUBLIC_SUPABASE_ANON_KEY=your_key_here
    SUPABASE_SERVICE_ROLE_KEY=your_key_here
    ```

### Database Setup (Supabase)

This project requires a specific table structure in Supabase to function correctly.

1.  **Create the `global_stats` table:**
    Run the following SQL in your Supabase SQL Editor:

    ```sql
    create table public.global_stats (
      id bigint generated by default as identity not null,
      stat_name text not null,
      stat_value bigint not null default 0,
      created_at timestamp with time zone not null default now(),
      updated_at timestamp with time zone not null default now(),
      constraint global_stats_pkey primary key (id),
      constraint global_stats_stat_name_key unique (stat_name)
    );

    -- Insert initial values
    insert into global_stats (stat_name, stat_value) values ('total_tweets', 0);
    insert into global_stats (stat_name, stat_value) values ('total_visitors', 0);
    ```

2.  **Create RPC Functions:**
    The API uses Remote Procedure Calls (RPC) for atomic updates.

    *Function: `increment_tweet_count`*
    ```sql
    create or replace function increment_tweet_count()
    returns integer
    language plpgsql
    as $$
    declare
      new_count integer;
    begin
      update global_stats
      set stat_value = stat_value + 1,
          updated_at = now()
      where stat_name = 'total_tweets'
      returning stat_value into new_count;
      
      return new_count;
    end;
    $$;
    ```

    *Function: `track_visitor`*
    This function handles unique visitor logic based on the anonymous ID provided. Note that this requires a separate `visitors` table if you wish to track individual IDs, or you can simplify it to just increment the global counter. The codebase expects an RPC named `track_visitor`.

    ```sql
    -- Simplified version for global counting only
    create or replace function track_visitor(p_user_id text, p_user_agent text, p_timezone text, p_language text)
    returns table (is_new_visitor boolean, total_visits bigint, total_unique_visitors bigint)
    language plpgsql
    as $$
    declare
      current_visitors bigint;
    begin
      -- Logic to check if visitor is new would go here using a separate visitors table
      -- For this setup, we will just return the global count
      
      select stat_value into current_visitors from global_stats where stat_name = 'total_visitors';
      
      return query select false, cast(1 as bigint), current_visitors;
    end;
    $$;
    ```

3.  **Enable Realtime:**
    In your Supabase dashboard, go to **Database** -> **Replication** and enable replication for the `global_stats` table to allow the client to subscribe to changes.

### Running the Application

Run the development server:

```bash
npm run dev
```

Open [http://localhost:3000](http://localhost:3000) with your browser to see the result.

## API Endpoints

*   **`GET /api/health`**: Returns the system status and checks if the Gemini API key is configured.
*   **`GET /api/tweet-count`**: Fetches the current global tweet count from Supabase.
*   **`POST /api/tweet-count`**: Increments or resets (admin only) the global tweet count.
*   **`POST /api/generate`**: Accepts a tone and prompt, communicates with Gemini, and returns the generated text.
*   **`POST /api/visitors`**: Records visitor analytics using the anonymous user ID.

## Privacy Policy

This application is designed with privacy as a core principle.

1.  **No Personal Data:** We do not collect names, emails, or phone numbers.
2.  **Stateless Generation:** The inputs sent to the AI and the outputs generated are not stored in our database.
3.  **Anonymous IDs:** User tracking is done via a hash of browser characteristics. This ID is stored in the user's LocalStorage and is used solely to count unique visitors and maintain session continuity.
4.  **Third Parties:** Data is processed by Google (for AI generation) and Supabase (for anonymous counter storage).

## Contributions
This project is mainly built as a fun side-project, but any contributions that improve stability, user experience or security are welcome. Please refer to the contribution guide at [CONTRIBUTING.md](https://github.com/xyzprtk/trashtalk/blob/main/CONTRIBUTING.md)
