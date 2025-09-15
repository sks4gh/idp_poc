---
layout: default
title: Data Management (Entries)
nav_order: 4
has_children: true
nav_fold: false
---

# Data Management (Entries)

Once you've defined your entity structure, you can add, retrieve, and manage your actual data. Individual data records are called "entries."
{: .fs-6 .fw-300 }

## Available APIs

### Add/Update Entries
```bash
curl -X POST "/api/v1/entries/{owner}/{entity-name}" \
  -H "Content-Type: application/json" \
  -d '[
    {
      "sku": "PROD001",
      "name": "Organic Whole Milk 1L",
      "brand": "Happy Cow",
      "price": 3.99
    },
    {
      "sku": "PROD002",
      "name": "Whole Grain Bread", 
      "brand": "Baker's Best",
      "price": 2.49
    }
  ]'
```

**Important**: Data must always be sent as JSON array, even for single entries:
```json
[
  {
    "sku": "PROD003",
    "name": "Greek Yogurt 500g"
  }
]
```

### Get Single Entry
```bash
curl -X GET "/api/v1/entries/{owner}/{entity-name}/{primary-key-value}"
```

**Example:**
```bash
curl -X GET "/api/v1/entries/your-team/products/PROD001"
```

**Response:**
```json
{
  "sku": "PROD001",
  "name": "Organic Whole Milk 1L",
  "brand": "Happy Cow", 
  "price": 3.99
}
```

### Delete Single Entry
```bash
curl -X DELETE "/api/v1/entries/{owner}/{entity-name}/{primary-key-value}"
```

**Example:**
```bash
curl -X DELETE "/api/v1/entries/your-team/products/PROD001"
```

### Delete All Entries (Flush)
```bash
curl -X DELETE "/api/v1/entries/{owner}/{entity-name}/flush"
```

**⚠️ Warning**: This removes ALL data for this entity permanently.

## Working with Data

### Adding Single Entry
Even for one entry, use array format:
```bash
curl -X POST "/api/v1/entries/your-team/products" \
  -H "Content-Type: application/json" \
  -d '[
    {
      "sku": "PROD001",
      "name": "Organic Milk",
      "brand": "Happy Cow",
      "price": 3.99,
      "inStock": true
    }
  ]'
```

### Updating Existing Data
Use the same POST endpoint with existing primary key values:
```bash
curl -X POST "/api/v1/entries/your-team/products" \
  -H "Content-Type: application/json" \
  -d '[
    {
      "sku": "PROD001",
      "price": 4.29,
      "inStock": false
    }
  ]'
```

### Bulk Operations
Upload multiple entries in single request:
```bash
curl -X POST "/api/v1/entries/your-team/products" \
  -H "Content-Type: application/json" \
  -d '[
    {"sku": "PROD001", "name": "Product 1", "price": 9.99},
    {"sku": "PROD002", "name": "Product 2", "price": 19.99},
    {"sku": "PROD003", "name": "Product 3", "price": 29.99}
  ]'
```

## Data with Special Fields

### Facetable Fields
For fields marked as `facetable` in your entity definition, just provide the values normally. See [🏷️ Faceting](faceting.md) for details on how faceting works.

### Category Fields
For entities with category fields, see [📂 Category Filter](category-filter.md) for the specific data format required for hierarchical categories.

## Data Validation

The platform validates your data against the entity definition:

### Valid Examples
```json
// ✅ Single entry (in array)
[
  {
    "sku": "PROD001",
    "name": "Product Name",
    "price": 19.99
  }
]

// ✅ Multiple entries
[
  {"sku": "PROD001", "name": "Product 1", "price": 9.99},
  {"sku": "PROD002", "name": "Product 2", "price": 19.99}
]
```

### Invalid Examples
```json
// ❌ Not an array
{
  "sku": "PROD001",
  "name": "Product Name"
}

// ❌ Missing required field
[
  {
    "name": "Product Name"
    // Missing required "sku" field
  }
]

// ❌ Wrong data type
[
  {
    "sku": "PROD001",
    "price": "not a number"  // Should be number
  }
]
```

## Best Practices

### Data Format
- **Always use arrays**: Even single entries must be wrapped in `[...]`
- **Batch efficiently**: Upload 100-1000 entries per request for best performance
- **Primary keys**: Use stable, meaningful identifiers

### Error Handling
- Check API responses for validation errors
- Handle failed uploads gracefully in your integration
- Use incremental updates for large datasets

## Integration Examples

### Daily Data Sync
```bash
# Upload daily product updates (always as array)
curl -X POST /api/v1/entries/your-team/products \
  -H "Content-Type: application/json" \
  -d '[{"sku":"PROD001","name":"Updated Product","price":29.99}]'
```

## Next Steps

- [🔍 Search](search.md) - Search through your data
- [🏷️ Faceting](faceting.md) - Filter and count by field values  
- [📂 Category Filter](category-filter.md) - Navigate hierarchical categories