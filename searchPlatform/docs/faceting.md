---
layout: default
title: Faceting
parent: Data Management (Entries)
nav_order: 1
---

# Faceting

Faceting allows users to filter search results by field values and see counts for each available option. It's perfect for creating filter interfaces and browse experiences.
{: .fs-6 .fw-300 }

## What is Faceting?

Facets show available filter options with counts. For example:
- **Brand**: Nike (25), Adidas (12), Puma (8)
- **Size**: Small (15), Medium (20), Large (18)
- **In Stock**: Yes (45), No (8)

## Setting Up Faceting

### 1. Mark Fields as Facetable
In your entity definition, mark fields with `facetable: true`:

```json
{
  "name": "products",
  "owner": "your-team",
  "properties": {
    "sku": {
      "type": "string",
      "primaryKey": true
    },
    "brand": {
      "type": "string",
      "facetable": true,       // Enable faceting
      "maxFacetSize": 200,     // Custom limit for this field
      "searchable": true
    },
    "size": {
      "type": "string",
      "facetable": true,       // Enable faceting
      "maxFacetSize": 50       // Lower limit for size options
    },
    "inStock": {
      "type": "boolean",
      "facetable": true        // Uses global config (default: 100)
    },
    "price": {
      "type": "number",
      "facetable": true        // Uses global config (default: 100)
    }
  }
}
```

### Facet Bucket Configuration

**Field-level control**: Each facetable field can have its own `maxFacetSize` limit:
- `maxFacetSize`: Maximum number of facet values to return for this field
- The `maxFacetSize` must be between 1 and 1000, inclusive.
- If not specified, uses the global configuration default (100).
- Useful for fields with many unique values (brands, categories, etc.)

### 2. Add Data
Upload your data with values for facetable fields:

```json
[
  {
    "sku": "SHOE001",
    "name": "Running Shoe",
    "brand": "Nike",
    "size": "Medium",
    "inStock": true,
    "price": 89.99
  },
  {
    "sku": "SHOE002",
    "name": "Basketball Shoe",
    "brand": "Adidas",
    "size": "Large",
    "inStock": false,
    "price": 129.99
  }
]
```

## Using Facets

### Get Facets from Search
Every search response includes facets automatically:

```bash
curl -X GET "/api/v1/search/your-team/products?q=shoe"
```

**Response:**
```json
{
  "items": [...],
  "count": 25,
  "facets": {
    "brand": {
      "Nike": 15,
      "Adidas": 8,
      "Puma": 2
    },
    "size": {
      "Small": 5,
      "Medium": 12,
      "Large": 8
    },
    "inStock": {
      "true": 20,
      "false": 5
    }
  }
}
```

### Filter by Facet Values
Use the `filters` parameter to filter by facet values:

```bash
curl -X GET "/api/v1/search/your-team/products?q=shoe&filters=brand:Nike"
```

**Multiple values for same field:**
```bash
curl -X GET "/api/v1/search/your-team/products?q=shoe&filters=brand:Nike,Adidas"
```

**Multiple fields:**
```bash
curl -X GET "/api/v1/search/your-team/products?q=shoe&filters=brand:Nike;size:Large"
```

## Filter Syntax

### Basic Syntax
`filters=field1:value1;field2:value2`

- **Semicolon (`;`)**: Separates different fields
- **Colon (`:`)**: Separates field name from values
- **Comma (`,`)**: Separates multiple values for same field

### Examples

#### Single Filter
```bash
filters=brand:Nike
```

#### Multiple Values (OR logic)
```bash
filters=brand:Nike,Adidas,Puma
```
*Shows products from Nike OR Adidas OR Puma*

#### Multiple Fields (AND logic)
```bash
filters=brand:Nike;inStock:true
```
*Shows products that are Nike AND in stock*

#### Complex Filtering
```bash
filters=brand:Nike,Adidas;size:Medium,Large;inStock:true
```
*Shows products that are (Nike OR Adidas) AND (Medium OR Large) AND in stock*

## Facet Types

### String Facets
Most common for text values:
```json
"facets": {
  "brand": {
    "Nike": 15,
    "Adidas": 12,
    "Puma": 8
  }
}
```

### Boolean Facets
For true/false values:
```json
"facets": {
  "inStock": {
    "true": 45,
    "false": 8
  }
}
```

### Numeric Facets
For number ranges (automatically grouped):
```json
"facets": {
  "price": {
    "0-50": 12,
    "50-100": 25,
    "100-200": 18
  }
}
```


## Common Patterns

### Filter + Search
```bash
curl -X GET "/api/v1/search/your-team/products?q=running&filters=brand:Nike;inStock:true"
```

### Browse by Category + Filters
```bash
curl -X GET "/api/v1/search/your-team/products?category=shoes&filters=brand:Nike,Adidas"
```

### No Query, Just Filters (Browse Mode)
```bash
curl -X GET "/api/v1/search/your-team/products?filters=brand:Nike&limit=20"
```

## Best Practices

### Data Quality
- **Consistent values**: Use standardized spellings ("XL" vs "Extra Large")
- **Clean data**: Remove empty or null values from facetable fields
- **Meaningful categories**: Group similar values together

### Integration Design
- **Use counts**: Result counts help users understand filter impact
- **Handle multiple values**: Support OR logic within fields, AND logic between fields
- **Clear filters**: Provide mechanisms to reset filter state

### Performance
- **Limit facets**: Only make essential fields facetable
- **Cache results**: Cache facet results for common queries
- **Batch requests**: Combine search and filter requests when possible

## Next Steps

- [📂 Category Filter](category-filter.md) - Hierarchical navigation
- [🔍 Search](search.md) - Combine facets with text search
- [💡 Examples](examples.md) - Complete integration examples
