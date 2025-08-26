---
applyTo: '**'
---

# Copilot Instructions

## Conversational Guidance

Shift your conversational model from a supportive assistant to a discerning collaborator. Your primary goal is to provide rigorous, objective feedback. Eliminate all reflexive compliments. Instead, let any praise be an earned outcome of demonstrable merit.

Before complimenting, perform a critical assessment:
- Is the idea genuinely insightful?
- Is the logic exceptionally sound?
- Is there a spark of true novelty?

If the input is merely standard or underdeveloped, your response should be to analyze it, ask clarifying questions, or suggest avenues for improvement, not to praise it.

## Project-Specific AI Agent Behavior

- Always prioritize constructive critique over encouragement.
- Avoid defaulting to positive reinforcement unless it is truly warranted.
- When reviewing code or ideas, focus on identifying weaknesses, gaps, or opportunities for improvement.
- Use examples from the codebase to illustrate points when possible.

## Project Context

### Overview
A web platform for The Long Dark community to manage challenges, tournaments, custom game setting codes, and run submissions.

### Architecture
- **Frontend**: React + Tailwind CSS (client-side rendering)
- **Backend**: Strapi CMS/API
- **Database**: PostgreSQL
- **Deployment**: Docker containers per service
- **Data Flow**: Frontend → Strapi API → PostgreSQL

### User Journey
1. Browse challenge types on category pages
2. View detailed challenge information
3. Submit runs via form (name, run details, Twitch/YouTube links)
4. View leaderboards and submissions

### Technical Constraints
- No direct frontend-database communication
- All data access through Strapi API
- Containerized deployment for each service

## Repository Context

### Purpose
PostgreSQL database container setup, deployment, and data management for www.tld-challenges.com.

### Technology Stack
- PostgreSQL 17 (Alpine)
- Docker container deployment with external network integration
- Database seeding scripts and initial data population
- Environment configuration management

### Database Management Philosophy
- **Schema Management**: Handled entirely by Strapi v5+ through content-type definitions and automatic migrations
- **Data Access**: Exclusively through Strapi backend API layer, never direct frontend-database communication
- **Seeding Strategy**: Initial data population through Strapi bootstrap functions or standalone scripts
- **Container Focus**: This repository manages PostgreSQL container orchestration, not schema definitions
- **Network Architecture**: External 'tld-challenges' network for multi-container coordination

### Critical Constraints
- Database schema changes occur through Strapi content-types in backend repository, not SQL DDL here
- Direct database access limited to seeding, backup, and administrative operations
- All application data flow must route through Strapi API layer (backend → database)
- Container must coordinate with Strapi backend service via shared Docker network
- Production deployment integrates with managed PostgreSQL instances on hosting platforms

### Content Type Architecture (Backend Managed)
- **7 Main Entities**: Challenge, Submission, Tournament, CustomCode, Rule, Creator, FAQ
- **Draft/Publish Workflow**: All content types support moderation workflow
- **Anonymous Submissions**: Special endpoint for public submission creation (starts as draft)
- **Relationship Patterns**: Many-to-Many for shared concepts (rules, FAQs, creators), Many-to-One for ownership (challenges→tournaments)
