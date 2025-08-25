# TLD Challenges Database

PostgreSQL database setup, deployment, and migration management for [www.tld-challenges.com](https://www.tld-challenges.com) - a web platform for The Long Dark community to manage challenges, tournaments, custom game settings, and run submissions.

## Architecture Overview

This repository manages the PostgreSQL database layer that supports:
- **Frontend**: React + Tailwind CSS (client-side rendering)
- **Backend**: Strapi CMS/API
- **Database**: PostgreSQL 14+ (this repository)
- **Deployment**: Docker containers per service

### Data Flow
```
Frontend → Strapi API → PostgreSQL Database
```

**Critical Constraint**: The frontend has no direct database communication. All data access occurs exclusively through the Strapi backend API.

## Technology Stack

- **PostgreSQL**: 17 (Alpine)
- **Docker**: Container deployment
- **Seeding Scripts**: Initial database population

## Database Purpose

The database stores and manages:
- Challenge definitions and categories
- Tournament structures and rules
- Custom game setting codes
- User run submissions with metadata
- Leaderboards and ranking data
- Twitch/YouTube integration links

## User Journey Data Support

1. **Browse Challenges**: Category and challenge metadata storage
2. **View Details**: Complete challenge specifications and rules
3. **Submit Runs**: Run data, timestamps, and media links
4. **Leaderboards**: Aggregated performance and ranking data

## Setup and Deployment

### Prerequisites
- Docker and Docker Compose
- PostgreSQL 17 compatible environment

### Local Development
```bash
# Clone repository
git clone https://github.com/bigfish-software/tld-challenges-database.git
cd tld-challenges-database

# Copy environment variables
cp db/.env.sample db/.env
# Edit db/.env with your preferred database credentials

# Start PostgreSQL container
docker-compose up -d

# Verify database is running
docker-compose ps
```

### Production Deployment
Database deployment occurs via Docker containers, integrated with the broader TLD Challenges platform infrastructure.

## Database Schema Management

**Important**: Schema definitions and migrations are handled entirely by Strapi, not this repository. Strapi automatically:
- Creates and manages database tables based on content-type definitions
- Handles schema migrations when content-types change
- Maintains referential integrity and indexes

This repository focuses on:
- PostgreSQL container setup and configuration
- Database seeding scripts for initial data population
- Environment-specific database configurations

## Integration Notes

- **Strapi Schema Management**: All table schemas, indexes, and relationships are defined and managed by Strapi content-types
- **API Constraints**: All database operations must route through Strapi API endpoints
- **Container Orchestration**: Database container coordinates with Strapi and frontend services
- **Bootstrap Seeding**: Initial data population occurs via Strapi's bootstrap function, not direct SQL

## Development Guidelines

- Database schema changes occur through Strapi content-type modifications, not direct SQL
- Seeding scripts should be implemented as Strapi bootstrap functions or standalone scripts in this repository
- Database access patterns must accommodate Strapi API layer
- Performance considerations for leaderboard queries and challenge data retrieval
- Data validation occurs at the Strapi API level, with database providing referential integrity

## Seeding Strategy

This repository may include seeding scripts for:
- Initial challenge categories and types
- Default game settings and configurations
- Reference data for tournaments and ranking systems
- Development environment test data

Seeding can be implemented through:
- Standalone SQL scripts executed during container initialization
- Node.js scripts that connect to the database directly
- Integration with Strapi's bootstrap lifecycle function

## Repository Structure

```
tld-challenges-database/
├── README.md
├── LICENSE
├── docker-compose.yml          # PostgreSQL container orchestration
├── db/                         # Database configuration
│   ├── Dockerfile             # PostgreSQL 17 Alpine image
│   ├── .env                   # Database environment variables
│   ├── .env.sample           # Environment template
│   └── data/                  # PostgreSQL data volume (created on startup)
├── seeds/                      # Database seeding scripts (planned)
│   ├── development/           # Development environment seeds
│   ├── staging/              # Staging environment seeds
│   └── production/           # Production environment seeds
└── docs/                      # Additional documentation
```

*Note: Seeding directory structure will be added as requirements are defined.*