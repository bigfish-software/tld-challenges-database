# TLD Challenges Database

PostgreSQL database container setup, deployment, and data management for [www.tld-challenges.com](https://www.tld-challenges.com) - a web platform for The Long Dark community to manage challenges, tournaments, custom game settings, and run submissions.

## Architecture Overview

This repository manages the **PostgreSQL database layer** that serves as the data persistence layer for the TLD Challenges platform.

### Core Architecture Pattern
```
Frontend (React) → Strapi API (backend repo) → PostgreSQL Database (this repo)
```

### Integration Points
- **Backend Repository**: [tld-challenges-backend](https://github.com/bigfish-software/tld-challenges-backend) - Strapi v5+ CMS/API
- **Frontend Repository**: Coming soon - React + Tailwind CSS client
- **Database Repository**: This repository - PostgreSQL 17 setup and container orchestration

### Data Flow
All data operations follow this strict pattern:
1. **Frontend** makes API requests to Strapi backend
2. **Strapi backend** processes requests and performs database operations
3. **PostgreSQL database** stores and retrieves data
4. **No direct frontend-database communication** is permitted

## Technology Stack

- **PostgreSQL**: 17 (Alpine)
- **Docker**: Container deployment with external network integration
- **Strapi Integration**: Schema managed by Strapi v5+ content-types
- **Network Architecture**: External 'tld-challenges' Docker network for multi-container coordination

## Database Purpose

The PostgreSQL database stores and manages data for:

### Content Types (7 Main Entities)
- **Challenge**: Main challenge definitions with rules, custom codes, and metadata
- **Submission**: Anonymous run submissions with validation and moderation workflow
- **Tournament**: Tournament structures with challenge organization and participant management
- **CustomCode**: Reusable custom game configuration codes with unique constraints
- **Rule**: Modular rule definitions with many-to-many challenge relationships
- **Creator**: Challenge creator profiles with social media links and attribution
- **FAQ**: Frequently asked questions with multi-entity associations

### Data Features
- **Draft/Publish Workflow**: All content types support moderation through Strapi
- **Anonymous Submissions**: Public submission endpoint creates draft entries for admin approval
- **Relationship Architecture**: Many-to-Many for shared concepts (rules, FAQs, creators), Many-to-One for ownership (challenges→tournaments)
- **External Media Integration**: Twitch/YouTube links for submission videos and creator profiles

### Security Model
- **Public API**: Only published content visible through Strapi API
- **Admin Access**: Full draft and published content access through Strapi admin
- **Anonymous Submissions**: Direct creation allowed, starts as draft requiring approval
- **Data Integrity**: Foreign key constraints and validation handled by Strapi ORM

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

# Create the shared Docker network (required for multi-container setup)
docker network create tld-challenges

# Build the database image (if you change the Dockerfile or image name)
docker-compose build

# Start PostgreSQL container
docker-compose up -d

# Verify database is running
# The container name should be 'tld-challenges-db' and the network 'tld-challenges'
docker ps
# The image name should be 'tld-challenges-db:latest' (if set in docker-compose.yml)

# Troubleshooting
# If you see network errors, ensure the 'tld-challenges' network exists:
docker network ls
# If missing, create it as shown above.
```

### Production Deployment
Database deployment occurs via Docker containers, integrated with the broader TLD Challenges platform infrastructure.

## Docker Compose Details
- **Container Name**: `tld-challenges-db`
- **Image Name**: `tld-challenges-db:latest` (if specified)
- **Network**: `tld-challenges` (external)

These names are set in `docker-compose.yml` and help with multi-container orchestration and troubleshooting.

## Database Schema Management

**Important**: Schema definitions and migrations are handled entirely by **Strapi v5+**, not this repository. The backend repository manages:

### Strapi-Managed Schema Operations
- **Content-Type Definitions**: All table schemas defined through Strapi Content-Type Builder
- **Automatic Migrations**: Schema changes automatically migrated when content-types are modified
- **Relationship Management**: Foreign keys and junction tables created automatically
- **Index Optimization**: Database indexes managed by Strapi ORM
- **Data Validation**: Field validation and constraints enforced at Strapi level

### Database Repository Responsibilities
This repository focuses exclusively on:
- **PostgreSQL Container Setup**: Docker container configuration and orchestration
- **Environment Management**: Database connection parameters and credentials
- **Network Integration**: External Docker network configuration for multi-container coordination
- **Data Seeding**: Initial data population scripts (when not handled by Strapi bootstrap)
- **Backup and Recovery**: Database backup strategies for production environments

### Integration with Backend Repository
- **Schema Source of Truth**: [tld-challenges-backend](https://github.com/bigfish-software/tld-challenges-backend) contains all content-type definitions
- **Migration Coordination**: Schema changes require backend deployment before database updates
- **Development Workflow**: Strapi manages schema, database provides persistence layer

## Integration Notes

### Strapi v5+ Backend Integration
- **Content-Type Architecture**: 7 main entities with Draft/Publish workflow across all types
- **Schema Management**: All database tables, indexes, and relationships defined and managed by Strapi content-types
- **API Security**: Database operations restricted to Strapi backend, with JWT authentication for frontend access
- **Anonymous Submissions**: Special public endpoint allows submission creation (starts as draft)

### Container Orchestration
- **External Network**: `tld-challenges` Docker network enables communication between database and backend containers
- **Container Naming**: `tld-challenges-db` for consistent multi-container identification
- **Environment Integration**: Database credentials and connection parameters managed through environment files

### Development Environment
- **Local Development**: Database container starts independently, backend connects via Docker network
- **Schema Synchronization**: Strapi automatically creates/updates database schema on backend startup
- **Data Persistence**: PostgreSQL data volume maintains state across container restarts

### Production Deployment
- **Managed Database Integration**: Container setup compatible with DigitalOcean, Railway, Render managed PostgreSQL
- **Environment-Based Configuration**: Database connection parameters configurable per deployment environment
- **Backup and Recovery**: Production requires managed database backup strategies

## Development Guidelines

### Database Access Patterns
- **Schema Changes**: All schema modifications occur through Strapi Content-Type Builder in backend repository
- **Data Operations**: Database interactions exclusively through Strapi API endpoints, never direct SQL
- **Seeding Strategy**: Initial data population via Strapi bootstrap functions or standalone scripts in this repository
- **Environment Coordination**: Database container must be running before Strapi backend startup

### Security and Validation
- **API Layer Security**: All security and validation occurs at Strapi API level
- **Database Integrity**: PostgreSQL provides referential integrity through foreign key constraints
- **Access Control**: Database access limited to Strapi backend service and administrative operations
- **Anonymous Submissions**: Public submission creation handled by Strapi with draft/publish moderation workflow

### Performance Considerations
- **Query Optimization**: Strapi ORM handles query optimization and database indexes
- **Relationship Queries**: Efficient population strategies for Many-to-Many relationships (rules, FAQs, creators)
- **Content Moderation**: Draft/Publish workflow ensures only approved content appears in public API responses
- **Submission Workflow**: Anonymous submissions start as drafts, requiring admin approval for publication

### Container and Network Management
- **External Network**: Database container must join `tld-challenges` external Docker network
- **Container Dependencies**: Backend service depends on database container being healthy and accessible
- **Environment Variables**: Database connection parameters configurable through environment files
- **Volume Persistence**: PostgreSQL data persisted through Docker volumes for development and testing

## Seeding Strategy

### Seeding Approaches
This repository supports multiple seeding strategies for initial data population:

#### 1. Strapi Bootstrap Integration (Recommended)
- **Primary Method**: Seeding handled by backend repository through Strapi's bootstrap lifecycle
- **Content Types**: All 7 content types (Challenge, Submission, Tournament, CustomCode, Rule, Creator, FAQ) populated via Strapi API
- **Draft/Publish Workflow**: Seeded content follows moderation workflow with published state
- **Relationship Management**: Many-to-Many relationships (rules↔challenges, faqs↔entities) properly established

#### 2. Standalone Database Scripts (Alternative)
- **Direct SQL**: Initial data insertion via SQL scripts during container initialization
- **Reference Data**: Game configuration defaults, challenge categories, tournament templates
- **Development Environment**: Test data population for local development and testing
- **Administrative Data**: System configuration and initial admin user setup

### Data Categories for Seeding
- **Challenge Categories**: Survival, skill-based, time trial, exploration challenges
- **Default Custom Codes**: Common game configuration templates
- **Tournament Templates**: Recurring tournament structures and rules
- **FAQ Content**: Common questions and answers across all content types
- **Creator Profiles**: Known community challenge creators and their social media links
- **Rule Definitions**: Reusable rule components for challenge construction

### Environment-Specific Seeding
- **Development**: Comprehensive test data including draft and published content
- **Staging**: Production-like data for testing without real user submissions
- **Production**: Minimal reference data and configuration, real content created through admin interface

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

## Related Repositories

### Backend Integration
- **[tld-challenges-backend](https://github.com/bigfish-software/tld-challenges-backend)** - Strapi v5+ CMS/API backend
  - Contains all content-type definitions and schema management
  - Provides API endpoints for frontend consumption
  - Manages authentication, permissions, and content moderation
  - Handles database migrations and relationship management

### Frontend Integration  
- **tld-challenges-frontend** (Coming soon) - React + Tailwind CSS client
  - Consumer of Strapi API endpoints
  - No direct database access
  - Handles user interface and user experience
  - Integrates with external media services (Twitch/YouTube)

### Documentation and Setup
- **Initial Setup**: Refer to [backend repository setup guide](https://github.com/bigfish-software/tld-challenges-backend/blob/main/docs/initial-setup.md) for complete development environment configuration
- **Content Type Architecture**: Detailed specifications in [backend ORM documentation](https://github.com/bigfish-software/tld-challenges-backend/blob/main/docs/orm.md)
- **Security Implementation**: Authentication and authorization handled by backend repository

## License

MIT License - see LICENSE file for details