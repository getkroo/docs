# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a **Mintlify documentation site** for Kroo, a construction data platform that ingests, normalizes, and flattens project data from multiple sources into ready-to-use tables. The documentation covers data modeling methodology, API sync processes, and usage guides.

## Development Commands

### Local Development
```bash
# Install Mintlify CLI globally
npm i -g mint

# Start local development server (runs on http://localhost:3000)
mint dev

# Run on custom port
mint dev --port 3333

# Update CLI to latest version
npm mint update

# Validate all links in documentation
mint broken-links
```

## Architecture & Structure

### Content Organization
- **Main documentation files**: `.mdx` files in root directory
- **API Reference**: `/api-reference/` directory with endpoint documentation
- **Guides**: Organized by tabs in `docs.json` navigation
- **Snippets**: Reusable content in `/snippets/` directory
- **Assets**: Images in `/images/`, logos in `/logo/`

### Key Configuration Files
- `docs.json`: Mintlify configuration with navigation, theming, and site structure
- `custom.css`: Custom styling overrides
- `.gitignore`: Git ignore patterns

### Documentation Structure
The site uses a tabbed navigation with:
- **Guides** tab: "How Kroo Works" section covering methodology, historical backfill, and record deletion
- **Troubleshooting** tab: "Data Freshness" section with manual refresh guidance

### Content Patterns
- All content files use `.mdx` format (MDX = Markdown + JSX)
- Frontmatter with `title` and `description` fields
- Mintlify-specific components like `<Info>`, `<Steps>`, `<Step>`, `<Frame>`, `<AccordionGroup>`
- Code blocks with language-specific syntax highlighting

## Key Technical Concepts

### Kroo Data Methodology
The platform follows a specific data modeling approach:
1. Fetch raw JSON from APIs
2. Flatten primary resources into main tables  
3. Extract nested arrays into sub-tables
4. Link sub-tables to main tables via join keys

### Sync Efficiency
- Client-side rate limits with back-off strategies
- Incremental refresh for changed data only
- Minimizes load on source systems and warehouse

## Pipeline Status API

The Pipeline Status API allows you to monitor the last sync times for all data pipelines in Kroo.

### Authentication
The API uses Basic Authentication. Credentials can be found in the Kroo App under **Settings > Company**.

### Available Endpoints

#### List Available Partners
```bash
curl --location 'https://api.getkroo.com/api/v1/pipeline_status/describe' \
--header 'Authorization: Basic ***'
```

Returns:
```json
{
    "partners": [
        "procore",
        "procore_correspondence"
    ]
}
```

#### Get Pipeline Status for a Partner
```bash
curl --location 'https://api.getkroo.com/api/v1/pipeline_status?partner=procore' \
--header 'Authorization: Basic ***'
```

Returns a map of pipeline names to their last sync timestamps:
```json
{
    "procore_commitment_change_orders": "2025-09-07T23:02:36.395-07:00",
    "procore_vendors": "2025-09-08T07:02:21.876-07:00",
    "procore_direct_costs": "2025-09-07T21:03:10.634-07:00",
    ...
}
```

#### Get Next Sync Times for a Partner
```bash
curl --location 'https://api.getkroo.com/api/v1/pipeline_status/next_sync_times?partner=procore' \
--header 'Authorization: Basic ***'
```

Returns a map of pipeline names to their scheduled next sync times:
```json
{
    "procore_coordination_issues": "2025-12-09T17:00:00.000-08:00",
    "procore_action_plans": "2025-12-09T02:00:00.000-08:00",
    "procore_schedule_tasks": "2025-12-09T03:00:00.000-08:00",
    "procore_submittals": "2025-12-09T18:00:00.000-08:00",
    "procore_departments": "2025-12-09T15:00:00.000-08:00",
    "procore_offices": "2025-12-09T18:00:00.000-08:00"
}
```

### PowerBI Integration

To access Pipeline Status data in PowerBI:

1. **Create a new data source** using "Web" connector
2. **Enter the API URL**: `https://api.getkroo.com/api/v1/pipeline_status?partner=procore`
3. **Configure authentication**:
   - Select "Basic" authentication
   - Enter your credentials from Kroo App Settings > Company
4. **Transform data** as needed using Power Query Editor
5. **Set up refresh schedule** to monitor pipeline status regularly

The API returns timestamps in ISO 8601 format with timezone information, which PowerBI can parse automatically for time-based analysis and alerting.

## Publishing

Changes are automatically deployed to production when pushed to the default branch via GitHub app integration. Local preview available at `http://localhost:3000` during development.