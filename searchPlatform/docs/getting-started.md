---
layout: default
title: Getting Started
nav_order: 2
---

# Getting Started

Get your team up and running with the Jumbo Search Platform in minutes.
{: .fs-6 .fw-300 }

## What You Need

- Your team's API access credentials
- Data you want to make searchable
- 5 minutes to follow this guide

## Quick Setup (3 Steps)

### Step 1: Define Your Entity

Tell the platform what kind of data you want to search. Create an entity definition:

```bash
curl -X POST /api/v1/entities/ \
  -H "Content-Type: application/json" \
  -d '{
    "name": "products",
    "owner": "your-team-name",
    "properties": {
      "id": {
        "type": "string",
        "primaryKey": true,
        "validation": {"required": true}
      },
      "name": {
        "type": "string",
        "searchable": true,
        "typoTolerant": true,
        "validation": {"required": true}
      },
      "brand": {
        "type": "string",
        "facetable": true
      }
    }
  }'
```

### Step 2: Upload Your Data

Send your data to the platform:

```bash
curl -X POST /api/v1/entries/your-team-name/products \
  -H "Content-Type: application/json" \
  -d '[
    {
      "id": "PROD001",
      "name": "Organic Whole Milk",
      "brand": "Happy Cow"
    },
    {
      "id": "PROD002", 
      "name": "Whole Grain Bread",
      "brand": "Baker Best"
    }
  ]'
```

### Step 3: Start Searching

Your data is now searchable:

```bash
curl "GET /api/v1/search/your-team-name/products?query=milk&limit=10"
```

## Adding Categories (Optional)

Want to organize your data into categories? Add a category field to your entity definition:

```json
{
  "category": {
    "type": "array",
    "validation": {
      "isArray": true,
      "required": true
    }
  }
}
```

Then include category information in your data:

```json
{
  "id": "PROD001",
  "name": "Organic Whole Milk",
  "category": [
    [
      {"id": "food", "title": "Food"},
      {"id": "dairy", "title": "Dairy"},
      {"id": "milk", "title": "Milk"}
    ]
  ]
}
```

## API Base URL

All requests go to: `{environment-url}/api/v1`

See [Platform Access](../#platform-access) for environment-specific URLs.

## Next Steps

- [📝 Entity Management](entities.md) - Define your data structure
- [📊 Data Management](entries.md) - Add and manage your data
  - [🏷️ Faceting](faceting.md) - Filter and count by field values
  - [📂 Category Filter](category-filter.md) - Hierarchical navigation
- [🔍 Search API](search.md) - Query your data with advanced features

## Need Help?

Contact the Platform Team on Slack: #search-platform