# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

SendGrid MCP Server - A Model Context Protocol server that provides AI assistants with access to SendGrid's Marketing API for email marketing and contact management operations.

**Stack:** TypeScript 5.3, Node.js 18+, ES Modules, MCP SDK v0.6.0

## Commands

```bash
npm run build      # Compile TypeScript to build/ (makes output executable)
npm run watch      # TypeScript compiler in watch mode
npm test           # Run Jest integration tests (requires SENDGRID_API_KEY)
npm run inspector  # Debug with MCP Inspector
```

## Architecture

```
index.ts (MCP Server Entry Point)
    ↓ Loads SENDGRID_API_KEY, initializes server
tools/index.ts (Tool Interface Layer)
    ↓ getToolDefinitions() - 20+ tool schemas
    ↓ handleToolCall() - routes to service methods
services/sendgrid.ts (Business Logic)
    ↓ 30+ methods wrapping SendGrid APIs
types/index.ts (TypeScript interfaces)
```

**Key patterns:**
- MCP handlers in index.ts use StdioServerTransport
- Tools are defined with JSON Schema for input validation
- Service layer wraps @sendgrid/client and @sendgrid/mail packages
- Uses SendGrid Marketing API v3 (not legacy APIs)
- Dynamic templates only (not legacy templates)
- Single Sends API for bulk email campaigns

## Testing

Tests are integration tests against real SendGrid API:
- Require `SENDGRID_API_KEY` environment variable (set in `.env` file)
- Use long timeouts (60000ms) for eventual consistency
- Create timestamped test resources for isolation
- Include retry logic for eventually consistent operations

Run single test file:
```bash
node --experimental-vm-modules node_modules/jest/bin/jest.js src/services/__tests__/sendgrid.test.ts
```

## SendGrid API Notes

- All operations use Marketing API v3 endpoints
- Contact operations are eventually consistent (may need retries in tests)
- Email sending requires verified sender identity
- Lists use `external_id` for deduplication when specified
