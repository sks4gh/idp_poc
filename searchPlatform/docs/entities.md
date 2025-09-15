---
layout: default
title: Entity Management
nav_order: 3
---

# Entity Management

Entities define the structure of your searchable data. They describe what fields your data contains and how those fields should be indexed for search.
{: .fs-6 .fw-300 }

## Available APIs

### Create/Update Entity
```bash
curl -X POST /api/v1/entities/ \
  -H "Content-Type: application/json" \
  -d '{
    "name": "products",
    "owner": "your-team-name",
    "description": "Product catalog for our team",
    "properties": {
      "sku": {
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
        "facetable": true,
        "searchable": true
      },
      "price": {
        "type": "number",
        "facetable": true
      }
    }
  }'
```

**Response:**
```
"Entity schema processed successfully"
```

### Get Entity
```bash
curl -X GET "/api/v1/entities/{owner}/{entity-name}"
```

**Example:**
```bash
curl -X GET "/api/v1/entities/your-team/products"
```

**Response:**
```json
{
  "id": "your-team_products",
  "name": "products",
  "owner": "your-team",
  "description": "Product catalog for our team",
  "properties": {
    "sku": {
      "type": "string",
      "primaryKey": true,
      "validation": {"required": true}
    },
    "name": {
      "type": "string",
      "searchable": true,
      "typoTolerant": true,
      "validation": {"required": true}
    }
  }
}
```

### Delete Entity
```bash
curl -X DELETE "/api/v1/entities/{owner}/{entity-name}"
```

**Response:**
```
"Entity schema has been successfully deleted"
```

**⚠️ Warning**: Deleting an entity removes all associated data permanently.

## Property Configuration

### Field Types
- `string`: Text data
- `number`: Numeric values  
- `boolean`: True/false values
- `array`: List of values

### Field Options
- **`primaryKey`**: Unique identifier (required, one per entity)
- **`searchable`**: Include in text search queries
- **`facetable`**: Enable filtering and counting by this field
- **`typoTolerant`**: Allow fuzzy matching for typos
- **`projected`**: Include in search results (default: true)

### Validation Rules
- **`required`**: Field must have a value
- **`isArray`**: Field expects array of values

## Example Entity Definitions

### Basic Product Entity
```json
{
  "name": "products",
  "owner": "ecommerce-team",
  "description": "E-commerce product catalog",
  "properties": {
    "sku": {
      "type": "string",
      "primaryKey": true,
      "validation": {"required": true}
    },
    "title": {
      "type": "string",
      "searchable": true,
      "typoTolerant": true,
      "validation": {"required": true}
    },
    "brand": {
      "type": "string",
      "facetable": true,
      "searchable": true
    },
    "price": {
      "type": "number",
      "facetable": true
    },
    "inStock": {
      "type": "boolean",
      "facetable": true
    }
  }
}
```

### Entity with Categories
```json
{
  "name": "products", 
  "owner": "retail-team",
  "description": "Products with category hierarchy",
  "properties": {
    "id": {
      "type": "string",
      "primaryKey": true,
      "validation": {"required": true}
    },
    "name": {
      "type": "string",
      "searchable": true,
      "typoTolerant": true
    },
    "category": {
      "type": "array",
      "validation": {
        "isArray": true,
        "required": true
      }
    },
    "brand": {
      "type": "string",
      "facetable": true
    }
  }
}
```

## Field Requirements

### Mandatory Fields
- **`name`**: Entity name (required)
- **`owner`**: Team identifier (required)  
- **`properties`**: At least one property with `primaryKey: true` (required)

### Optional Fields
- **`description`**: Entity description (max 500 characters)

## Best Practices

### Naming Conventions
- **Entity names**: Use lowercase, plural nouns (`products`, `employees`)
- **Owner names**: Use your team identifier (`ecommerce-team`, `hr-team`)
- **Field names**: Use camelCase (`firstName`, `productCode`)

### Performance Tips
- **Primary keys**: Keep them short and meaningful
- **Searchable fields**: Only mark fields that users will actually search
- **Facetable fields**: Only mark fields you'll use for filtering

## Next Steps

- [📝 Entries](entries.md) - Add data to your entity
- [🔍 Search](search.md) - Search through your data
- [🏷️ Faceting](faceting.md) - Filter by field values