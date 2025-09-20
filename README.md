# TLD Challenges Database

PostgreSQL database container setup for [www.tld-challenges.com](https://www.tld-challenges.com) - a web platform for The Long Dark community to manage challenges, tournaments, and run submissions.

## Overview

This repository provides the PostgreSQL database layer for the TLD Challenges platform. The database stores data for challenges, submissions, tournaments, and related entities.

## Relation to Backend Repository

The database schema is managed by the [tld-challenges-backend](https://github.com/bigfish-software/tld-challenges-backend) repository using Strapi v5+. All data access occurs through the Strapi API, with no direct frontend-database communication.

## Local Development Setup

### Prerequisites
- Docker and Docker Compose

### Steps
```bash
# Clone repository
git clone https://github.com/bigfish-software/tld-challenges-database.git
cd tld-challenges-database

# Create the shared Docker network
docker network create tld-challenges

# Start PostgreSQL container
docker-compose up -d

# Verify
docker ps
```

## Useful Documentation

- [Backend Repository](https://github.com/bigfish-software/tld-challenges-backend)
- [Hosting Guide](./docs/hosting.md)
- [Strapi Documentation](https://docs.strapi.io/)

## License

MIT License - see LICENSE file for details