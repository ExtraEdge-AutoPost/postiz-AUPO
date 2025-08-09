# CLAUDE.md - Postiz Fork Development Guide

## Overview
This is a fork of Postiz (formerly Gitroom), an open-source social media scheduling platform. This guide provides comprehensive instructions for developing, customizing, and white-labeling the application for your proprietary SaaS product.

## Tech Stack
- **Frontend**: Next.js 14 with React 18.3, TailwindCSS
- **Backend**: NestJS with Prisma ORM
- **Database**: PostgreSQL
- **Queue/Cache**: Redis with BullMQ
- **Monorepo**: PNPM workspaces with NX
- **Node Version**: 20.x (required)

## Project Structure
```
postiz-app/
├── apps/
│   ├── frontend/          # Next.js frontend application
│   ├── backend/           # NestJS API server
│   ├── workers/           # Background job processors
│   ├── cron/             # Scheduled tasks
│   ├── extension/        # Browser extension
│   └── commands/         # CLI commands
├── libraries/
│   ├── react-shared-libraries/    # Shared React components
│   ├── nestjs-libraries/          # Shared backend logic
│   └── helpers/                   # Utility functions
├── .env.example          # Environment variables template
└── package.json          # Root package configuration
```

## Development Setup

### Prerequisites
- Node.js 20.x (use Volta or NVM for version management)
- PostgreSQL database
- Redis server
- Docker (optional but recommended)
- PNPM package manager

### Initial Setup

1. **Clone and Install Dependencies**
```bash
git clone <your-fork-url>
cd postiz-app
pnpm install
```

2. **Database and Redis Setup**

Option A: Using Docker (Recommended)
```bash
docker run -e POSTGRES_USER=root -e POSTGRES_PASSWORD=your_password --name postgres -p 5432:5432 -d postgres
docker run --name redis -p 6379:6379 -d redis
```

Option B: Using docker-compose
```bash
pnpm run dev:docker
```

3. **Environment Configuration**
```bash
cp .env.example .env
```

Edit `.env` with your configuration:
```env
# Required Settings
DATABASE_URL="postgresql://root:your_password@localhost:5432/postiz-db-local"
REDIS_URL="redis://localhost:6379"
JWT_SECRET="your-long-random-jwt-secret"

# Frontend URLs - MUST match your actual access URL
FRONTEND_URL="http://localhost:4200"
NEXT_PUBLIC_BACKEND_URL="http://localhost:3000"
BACKEND_INTERNAL_URL="http://localhost:3000"

# Storage (use 'local' for development)
STORAGE_PROVIDER="local"
UPLOAD_DIRECTORY="./uploads"
NEXT_PUBLIC_UPLOAD_STATIC_DIRECTORY="http://localhost:3000/uploads"
```

4. **Database Setup**
```bash
pnpm run prisma-db-push
```

5. **Start Development Server**
```bash
pnpm run dev
```

Access the application at: http://localhost:4200

## White-Labeling Guide

### 1. Logo Replacement

**Primary Logo Locations:**
- `/apps/frontend/public/logo.svg` - Main application logo
- `/apps/frontend/public/postiz.svg` - Postiz-specific logo
- `/apps/frontend/public/postiz-text.svg` - Text logo variant
- `/apps/frontend/public/postiz-fav.png` - Favicon variant
- `/apps/frontend/public/favicon.ico` - Browser favicon
- `/apps/frontend/public/favicon.png` - PNG favicon

**Logo Component:**
- `/apps/frontend/src/components/new-layout/logo.tsx` - React component for logo rendering

To replace the logo:
1. Replace the SVG/PNG files in `/apps/frontend/public/`
2. Update the Logo component in `/apps/frontend/src/components/new-layout/logo.tsx`
3. Update favicon files for browser tabs

### 2. Color Theme Customization

**Color Configuration Files:**
- `/apps/frontend/src/app/colors.scss` - CSS variables for light/dark themes
- `/apps/frontend/tailwind.config.js` - TailwindCSS color mappings

**Key Color Variables (in colors.scss):**
```scss
:root {
  .dark {
    --new-btn-primary: #612bd3;    // Primary button color
    --new-ai-btn: #d82d7e;          // AI features button
    --new-bgColor: #0e0e0e;         // Background color
    --new-btn-text: #ffffff;        // Text color
    // ... more variables
  }
  .light {
    --new-btn-primary: #612bd3;    // Primary button color
    --new-ai-btn: #d82d7e;          // AI features button
    --new-bgColor: #f0f2f4;         // Background color
    --new-btn-text: #0E0E0E;        // Text color
    // ... more variables
  }
}
```

To customize colors:
1. Edit `/apps/frontend/src/app/colors.scss`
2. Update both `.dark` and `.light` theme sections
3. The TailwindCSS config automatically uses these CSS variables

### 3. Branding Text Replacement

**Key Files to Update:**
- `/apps/frontend/src/app/(app)/auth/layout.tsx` - Auth pages branding
- `/apps/frontend/src/components/layout/top.menu.tsx` - Top navigation
- Update all "Postiz" references with your brand name

**Search and Replace:**
```bash
# Find all Postiz references
grep -r "Postiz" apps/frontend/src
grep -r "postiz" apps/frontend/src
```

### 4. Authentication Background

**Auth Page Styling:**
- `/apps/frontend/public/auth/bg-login.png` - Login background image
- `/apps/frontend/public/auth/login-box.png` - Login box decoration

The auth layout uses these images via Tailwind classes:
- `bg-loginBg` - Background pattern
- `bg-loginBox` - Login form container

## Frontend Development

### Key Frontend Directories
- `/apps/frontend/src/app/` - Next.js app router pages
- `/apps/frontend/src/components/` - React components
- `/libraries/react-shared-libraries/src/` - Shared UI components

### Component Architecture
- Uses React 18.3 with TypeScript
- Form handling with react-hook-form
- State management with Zustand
- Styling with TailwindCSS
- Icons and media in `/apps/frontend/public/`

### Important Components
- `launches/` - Post scheduling interface
- `analytics/` - Analytics dashboard
- `billing/` - Subscription management
- `settings/` - User settings
- `new-layout/` - Main layout components

## API and Backend

### Backend Structure
- `/apps/backend/src/api/routes/` - API controllers
- `/libraries/nestjs-libraries/src/database/` - Database services
- `/libraries/nestjs-libraries/src/integrations/` - Social media integrations

### Key API Endpoints
- Auth: `/auth/*`
- Posts: `/posts/*`
- Integrations: `/integrations/*`
- Analytics: `/analytics/*`

## Development Commands

### Daily Development
```bash
pnpm run dev                 # Start all services
pnpm run dev:frontend        # Frontend only
pnpm run dev:backend         # Backend only
pnpm run dev:workers         # Workers only
```

### Database Management
```bash
pnpm run prisma-generate     # Generate Prisma client
pnpm run prisma-db-push     # Push schema changes
pnpm run prisma-reset       # Reset database (WARNING: data loss)
```

### Building for Production
```bash
pnpm run build              # Build all services
pnpm run build:frontend     # Build frontend only
pnpm run build:backend      # Build backend only
```

### Testing
```bash
pnpm test                   # Run tests with coverage
```

## Environment Variables

### Critical Variables for White-Labeling
```env
# Brand Configuration
IS_GENERAL="false"          # Set to false for custom branding

# Upload Configuration
STORAGE_PROVIDER="local"    # or "cloudflare" for production
UPLOAD_DIRECTORY="./uploads"
NEXT_PUBLIC_UPLOAD_STATIC_DIRECTORY="http://localhost:3000/uploads"

# Email Configuration (optional)
RESEND_API_KEY=""           # If set, enables email verification
EMAIL_FROM_ADDRESS=""
EMAIL_FROM_NAME="Your Brand"
```

## Deployment Considerations

### Production Checklist
- [ ] Replace all logo files
- [ ] Update color scheme in colors.scss
- [ ] Replace "Postiz" branding text
- [ ] Configure production environment variables
- [ ] Set up SSL/HTTPS (required for social media OAuth)
- [ ] Configure cloud storage (Cloudflare R2 recommended)
- [ ] Set up email service (Resend or SMTP)
- [ ] Configure social media API keys

### Security Notes
- Always use HTTPS in production
- Keep JWT_SECRET secure and random
- Implement rate limiting
- Use environment variables for sensitive data
- Regular security updates

## Common Customizations

### Adding New Social Platforms
1. Create provider in `/libraries/nestjs-libraries/src/integrations/social/`
2. Add platform icon in `/apps/frontend/public/icons/platforms/`
3. Update integration manager
4. Add UI components for platform-specific settings

### Modifying Post Scheduling
- Edit components in `/apps/frontend/src/components/launches/`
- Update post services in `/libraries/nestjs-libraries/src/database/prisma/posts/`

### Custom Analytics
- Modify `/apps/frontend/src/components/analytics/`
- Update analytics controllers in `/apps/backend/src/api/routes/analytics.controller.ts`

## Troubleshooting

### Common Issues

1. **Port conflicts**: Ensure ports 3000, 4200, 5432, 6379 are available
2. **Database connection**: Check DATABASE_URL in .env
3. **Missing dependencies**: Run `pnpm install` and `pnpm run prisma-generate`
4. **Build errors**: Ensure Node.js 20.x is being used

### Development Tips
- Use `pnpm run dev` for hot-reload development
- Check browser console for frontend errors
- Monitor backend logs for API issues
- Use Redis CLI to debug queue issues

## Support Resources

- Original Postiz Documentation: https://docs.postiz.com
- GitHub Issues: Check original repo for known issues
- Discord Community: Available for developers

## License Compliance

This fork is based on Postiz which is licensed under AGPL-3.0. Ensure your usage complies with the license terms, particularly regarding:
- Source code availability
- License preservation
- Modification disclosure

---

*This guide is specific to your fork of Postiz for developing a proprietary SaaS product. Update this document as you make changes to maintain accurate documentation.*