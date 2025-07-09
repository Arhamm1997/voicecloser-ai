# voicecloser-ai# 🗄️

## Overview

This directory contains all database migrations for the VoiceCloser AI CRM system. Migrations are automatically generated when you make changes to the Prisma schema.

## Setup Instructions

### 1. Initial Setup

```bash
# Install dependencies
npm install

# Set up environment variables
cp .env.example .env.local
# Edit .env.local with your database URL

# Generate Prisma client
npx prisma generate

# Push schema to database (for development)
npx prisma db push

# OR create and apply migrations (for production)
npx prisma migrate dev --name init
```

### 2. Database Seeding

```bash
# Seed the database with sample data
npm run db:seed

# Or manually run seed
npx prisma db seed
```

### 3. Migration Commands

```bash
# Create a new migration
npx prisma migrate dev --name your_migration_name

# Apply pending migrations
npx prisma migrate deploy

# Reset database (WARNING: This deletes all data)
npx prisma migrate reset

# View migration status
npx prisma migrate status

# View database in browser
npx prisma studio
```

## Migration History

### Initial Migration (001_init)

- Created all core tables
- Set up user authentication
- Created leads and calls management
- Added payment processing tables
- Implemented analytics tracking

### Database Schema Overview

#### 🔐 Authentication Tables

- `users` - User accounts and roles
- `accounts` - OAuth provider accounts
- `sessions` - Active user sessions
- `verification_tokens` - Email verification
- `user_settings` - User preferences

#### 📞 CRM Core Tables

- `leads` - Prospect information
- `calls` - Call records and outcomes
- `call_messages` - Conversation transcripts
- `agents` - AI agent configurations
- `activities` - CRM activity tracking

#### 💳 Business Tables

- `payments` - Payment processing
- `campaigns` - Marketing campaigns
- `analytics` - Performance metrics
- `system_config` - Application settings

## Development Workflow

### Making Schema Changes

1. Edit `prisma/schema.prisma`
2. Run `npx prisma migrate dev --name description_of_change`
3. Review the generated migration file
4. Test the migration in development
5. Apply to production with `npx prisma migrate deploy`

### Best Practices

- Always backup production data before migrations
- Test migrations in development first
- Use descriptive migration names
- Review generated SQL before applying
- Keep migrations small and focused

## Environment-Specific Configs

### Development (SQLite)

```env
DATABASE_URL="file:./dev.db"
```

### Production (PostgreSQL)

```env
DATABASE_URL="postgresql://username:password@localhost:5432/voicecloser_ai"
```

### Testing

```env
DATABASE_URL="file:./test.db"
```

## Troubleshooting

### Common Issues

#### Migration Conflicts

```bash
# If migrations are out of sync
npx prisma migrate resolve --applied "migration_name"
```

#### Schema Drift

```bash
# Reset and reapply migrations
npx prisma migrate reset
npx prisma migrate dev
```

#### Prisma Client Issues

```bash
# Regenerate client
npx prisma generate
```

### Database Connection Issues

1. Check DATABASE_URL in .env.local
2. Ensure database server is running
3. Verify connection permissions
4. Check firewall settings

## Performance Considerations

### Indexing Strategy

- Phone numbers are indexed for quick lead lookup
- Email addresses have unique constraints
- Call timestamps are indexed for analytics
- Lead status and priority are indexed for filtering

### Query Optimization

- Use `include` instead of multiple queries
- Implement pagination for large datasets
- Use database-level aggregations
- Consider read replicas for analytics

## Backup Strategy

### Automated Backups

```bash
# Create backup script
#!/bin/bash
DATE=$(date +%Y%m%d_%H%M%S)
sqlite3 prisma/dev.db ".backup backup_$DATE.db"
```

### Manual Backup

```bash
# SQLite backup
cp prisma/dev.db backup/dev_backup_$(date +%Y%m%d).db

# PostgreSQL backup
pg_dump $DATABASE_URL > backup/backup_$(date +%Y%m%d).sql
```

## Monitoring & Maintenance

### Regular Tasks

- Monitor database size growth
- Analyze slow queries
- Update statistics
- Vacuum/optimize tables
- Review and archive old data

### Health Checks

```bash
# Check database connectivity
npx prisma db execute --stdin < health_check.sql

# Validate schema
npx prisma validate
```

## Data Retention Policy

### Call Data

- Keep detailed call logs for 1 year
- Archive transcripts after 2 years
- Retain analytics summaries indefinitely

### Lead Data

- Active leads: Keep indefinitely
- Closed/Lost leads: Archive after 1 year
- DNC requests: Keep permanently

### Payment Data

- Transaction records: 7 years (compliance)
- Payment methods: Remove after 1 year
- Refund records: 3 years

## Security Considerations

### Data Protection

- All PII is encrypted at rest
- Phone numbers are hashed for privacy
- Payment data follows PCI DSS standards
- Access logs are maintained

### Access Control

- Database users have minimal required permissions
- Connection strings use SSL/TLS
- Regular password rotation
- IP-based access restrictions

## Support & Documentation

### Prisma Resources

- [Prisma Documentation](https://www.prisma.io/docs/)
- [Schema Reference](https://www.prisma.io/docs/reference/api-reference/prisma-schema-reference)
- [Migration Guide](https://www.prisma.io/docs/concepts/components/prisma-migrate)

### Project Resources

- Database Schema: `prisma/schema.prisma`
- Seed Data: `prisma/seed.ts`
- Environment Config: `.env.example`

For questions or issues, please check the troubleshooting section or contact the development team.

"scripts": {
  "db:seed": "ts-node --compiler-options '{\"module\":\"CommonJS\"}' prisma/seed.ts"
}
