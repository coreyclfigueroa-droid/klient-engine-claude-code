# CLAUDE.md

## About Me
I'm Corey. I run a Skool community for small business owners who want AI automation. I help the little guys do big things with AI.

## My Audience
Small business owners who want to save money or do more. They don't have big teams — they're scrappy, resourceful, and just need the right tools to punch above their weight.

## My Voice & Style
Casual and funny. I talk like a friend, not a corporate robot. If I can make someone laugh while teaching them something useful, that's a win. Keep it real, keep it light.

## Tools I Use
- TikTok, Instagram, YouTube (short-form content is the engine)
- Airtable (for organizing and tracking everything)
- fal.ai (for AI-powered video/image generation)

## Current Focus
Automating AI UGC videos — building a pipeline where scripts get generated, sent to fal.ai for video creation, and content pumps out on autopilot across all platforms.

## Workspace Overview

This is a general-purpose Claude Code workspace used for project development and experimentation. It runs on Windows 10 with a bash shell.

## Structure

- skills/ — Custom Claude Code skill definitions (`.md` files defining slash commands)
- .claude/ — Claude Code local settings and configuration

## Environment Notes

- Platform: Windows 10 (bash shell via Git Bash or similar)
- Use Unix-style paths with forward slashes in shell commands
- Use /dev/null (not NUL) and other Unix conventions in bash

## Workflow Orchestration

### 1. Plan Node Default
- Enter plan mode for ANY non-trivial task (3+ steps or architectural decisions)
- If something goes sideways, STOP and re-plan immediately – don't keep pushing
- Use plan mode for verification steps, not just building
- Write detailed specs upfront to reduce ambiguity

### 2. Subagent Strategy
- Use subagents liberally to keep main context window clean
- Offload research, exploration, and parallel analysis to subagents
- For complex problems, throw more compute at it via subagents
- One tack per subagent for focused execution

### 3. Self-Improvement Loop
- After ANY correction from the user: update tasks/lessons.md with the pattern
- Write rules for yourself that prevent the same mistake
- Ruthlessly iterate on these lessons until mistake rate drops
- Review lessons at session start for relevant project

### 4. Verification Before Done
- Never mark a task complete without proving it works
- Diff behavior between main and your changes when relevant
- Ask yourself: "Would a staff engineer approve this?"
- Run tests, check logs, demonstrate correctness

### 5. Demand Elegance (Balanced)
- For non-trivial changes: pause and ask "is there a more elegant way?"
- If a fix feels hacky: "Knowing everything I know now, implement the elegant solution"
- Skip this for simple, obvious fixes – don't over-engineer
- Challenge your own work before presenting it

### 6. Autonomous Bug Fixing
- When given a bug report: just fix it. Don't ask for hand-holding
- Point at logs, errors, failing tests – then resolve them
- Zero context switching required from the user
- Go fix failing CI tests without being told how

## Task Management

1. Plan First: Write plan to tasks/todo.md with checkable items
2. Verify Plan: Check in before starting implementation
3. Track Progress: Mark items complete as you go
4. Explain Changes: High-level summary at each step
5. Document Results: Add review section to tasks/todo.md
6. Capture Lessons: Update tasks/lessons.md after corrections

## Core Principles

- Simplicity First: Make every change as simple as possible. Impact minimal code.
- No Laziness: Find root causes. No temporary fixes. Senior developer standards.
- Minimal Impact: Changes should only touch what's necessary. Avoid introducing bugs.
