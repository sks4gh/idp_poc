---
layout: default
title: Search API
nav_order: 5
---

# Search API

The core search functionality that allows you to query your data with text search, filtering, pagination, and more.
{: .fs-6 .fw-300 }

## Available API

### Search Endpoint
```bash
curl -X GET "/api/v1/search/{owner}/{entity-name}"
```

## Query Parameters

### Basic Parameters
- **`q`** (optional): Search query text
- **`limit`** (optional): Results per page (default: 20, max: 100)
- **`offset`** (optional): Results to skip for pagination (default: 0)
- **`debugMode`** (optional): Include debug information (true/false)

### Filtering Parameters
- **`category`** (optional): Filter by category ID (see [Category Filter](category-filter.md))
- **`filters`** (optional): Additional field filters (see [Faceting](faceting.md))
- **`properties`** (optional): Comma-separated list of fields to return

## Search Examples

### Basic Text Search
```bash
curl -X GET "/api/v1/search/your-team/products?q=milk"
```

### Search with Pagination
```bash
curl -X GET "/api/v1/search/your-team/products?q=organic&limit=10&offset=20"
```

### Browse All (No Query)
```bash
curl -X GET "/api/v1/search/your-team/products?limit=50"
```

### Specific Fields Only
```bash
curl -X GET "/api/v1/search/your-team/products?q=bread&properties=sku,name,price"
```

### With Category Filter
```bash
curl -X GET "/api/v1/search/your-team/products?q=milk&category=dairy"
```

### With Field Filters
```bash
curl -X GET "/api/v1/search/your-team/products?q=milk&filters=brand:Happy Cow;inStock:true"
```

### Debug Mode
```bash
curl -X GET "/api/v1/search/your-team/products?q=milk&debugMode=true"
```

## Search Response

```json
{
  "items": [
    {
      "sku": "PROD001",
      "name": "Organic Whole Milk 1L",
      "brand": "Happy Cow",
      "price": 3.99
    }
  ],
  "count": 150,
  "facets": {
    "brand": {
      "Happy Cow": 25,
      "Baker's Best": 12
    }
  },
  "categories": [
    {
      "id": "dairy",
      "title": "Dairy",
      "count": 8
    }
  ],
  "breadcrumbs": [
    {
      "id": "food",
      "title": "Food"
    }
  ]
}
```

### Response Fields
- **`items`**: Array of matching entries
- **`count`**: Total number of matching results (across all pages)
- **`facets`**: Available filters with counts (see [Faceting](faceting.md))
- **`categories`**: Child categories with counts (see [Category Filter](category-filter.md))
- **`breadcrumbs`**: Category navigation path (see [Category Filter](category-filter.md))

## Search Features

### Text Search Capabilities
The platform searches through all fields marked as `searchable` in your entity definition:

- **Exact matches**: Finds products containing "organic" and "milk"
- **Typo tolerance**: Finds "orgnic milk" or "organik milk" 
- **Partial matches**: Finds "organic" in "100% organic whole milk"

### Field Projection
Limit returned fields to reduce response size:

```bash
curl -X GET "/api/v1/search/your-team/products?q=milk&properties=sku,name,price"
```

Only returns specified fields in the `items` array.

### Empty Query Browsing
Search without query text to browse all data:

```bash
curl -X GET "/api/v1/search/your-team/products?limit=20"
```

Returns all entries with facets and category information for browsing.

## Pagination

### Pagination Examples
```bash
# Page 1
curl -X GET "/api/v1/search/your-team/products?q=milk&limit=10&offset=0"

# Page 2
curl -X GET "/api/v1/search/your-team/products?q=milk&limit=10&offset=10"

# Page 3
curl -X GET "/api/v1/search/your-team/products?q=milk&limit=10&offset=20"
```

### Calculating Total Pages
Use the `count` field in the response:
- **Total pages**: `Math.ceil(count / limit)`
- **Example**: If `count: 150` and `limit: 10`, then 15 total pages

## API Usage Patterns

### Basic Search
```bash
# Simple text search
curl -X GET "/api/v1/search/your-team/products?q=organic"

# Browse all products
curl -X GET "/api/v1/search/your-team/products?limit=20"
```

### Search with Filters
```bash
# Category + text search
curl -X GET "/api/v1/search/your-team/products?q=milk&category=dairy"

# Multiple filters
curl -X GET "/api/v1/search/your-team/products?q=milk&filters=brand:Happy Cow;inStock:true"
```

### Pagination
```bash
# First page
curl -X GET "/api/v1/search/your-team/products?q=milk&limit=10&offset=0"

# Second page  
curl -X GET "/api/v1/search/your-team/products?q=milk&limit=10&offset=10"
```

### Field Projection
```bash
# Only return specific fields
curl -X GET "/api/v1/search/your-team/products?q=milk&properties=sku,name,price"
```

## Best Practices

### Performance
- **Use pagination**: Don't request all results at once (max limit: 100)
- **Limit fields**: Use `properties` parameter for large datasets
- **Reasonable limits**: Use appropriate page sizes (10-50 items)

### Search Queries
- **Keep queries simple**: Short, natural language queries work best
- **Typo tolerance**: Built-in, no special handling needed
- **Empty queries**: Use for browsing/filtering without text search

### Response Handling
- **Check `count`**: Shows total results across all pages
- **Use facets**: For building filter interfaces
- **Use categories**: For hierarchical navigation
- **Handle empty results**: When `items` array is empty

## Next Steps

- [🏷️ Faceting](faceting.md) - Add field-based filtering
- [📂 Category Filter](category-filter.md) - Hierarchical navigation