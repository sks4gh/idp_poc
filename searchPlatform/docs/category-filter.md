---
layout: default
title: Category Filter
parent: Data Management (Entries)
nav_order: 2
---

# Category Filter

Category filtering enables hierarchical navigation through your data. Products can belong to multiple category paths simultaneously, all starting from a single root category.
{: .fs-6 .fw-300 }

## Category Structure Requirements

**All category paths for your entity must start with the same root category.**

**✅ Correct - Same root for all paths:**
```json
[
  [
    {"id": "Food", "title": "Food"},
    {"id": "dairy", "title": "Dairy"},
    {"id": "milk", "title": "Milk"}
  ],
  [
    {"id": "Food", "title": "Food"},        // Same root
    {"id": "organic", "title": "Organic"},
    {"id": "beverages", "title": "Beverages"},
    {"id": "milk", "title": "Milk"}
  ]
]
```

**❌ Wrong - Different roots:**
```json
[
  [
    {"id": "Food", "title": "Food"},
    {"id": "dairy", "title": "Dairy"}
  ],
  [
    {"id": "Store", "title": "Store"},  // Different root - NOT allowed
    {"id": "aisle12", "title": "Aisle 12"}
  ]
]
```

## Setting Up Categories

### 1. Add Category Field to Entity
```json
{
  "name": "products",
  "owner": "your-team",
  "properties": {
    "sku": {
      "type": "string",
      "primaryKey": true
    },
    "category": {
      "type": "array",
      "validation": {
        "isArray": true,
        "required": true
      }
    }
  }
}
```

### 2. Upload Data with Categories
```json
[
  {
    "sku": "MILK001",
    "name": "Organic Whole Milk 1L",
    "category": [
      [
        {"id": "Food", "title": "Food"},
        {"id": "dairy", "title": "Dairy"}, 
        {"id": "milk", "title": "Milk"}
      ],
      [
        {"id": "Food", "title": "Food"},
        {"id": "organic", "title": "Organic"},
        {"id": "beverages", "title": "Beverages"},
        {"id": "milk", "title": "Milk"}
      ]
    ]
  }
]
```

**Format**: `[[CategoryItem]]`
- **Outer array**: Multiple paths for same product
- **Inner array**: Single path from root to leaf
- **CategoryItem**: `{"id": "unique-id", "title": "Display Name"}`

## Search Response Details

### What You Get in Search Results

When you search with category filter, the response includes:
- **`categories`**: Child categories of your current category with counts
- **`breadcrumbs`**: Navigation path showing where you are

### Practical Example

**Setup Data:**
```json
{
  "sku": "MILK001",
  "name": "Organic Whole Milk",
  "category": [
    [
      {"id": "Food", "title": "Food"},
      {"id": "dairy", "title": "Dairy"},
      {"id": "milk", "title": "Milk"}
    ],
    [
      {"id": "Food", "title": "Food"}, 
      {"id": "organic", "title": "Organic"},
      {"id": "beverages", "title": "Beverages"},
      {"id": "milk", "title": "Milk"}
    ]
  ]
}
```

**Search at Root Level:**
```bash
curl -X GET "/api/v1/search/your-team/products?category=Food"
```

**Response:**
```json
{
  "items": [...],
  "count": 100,
  "categories": [
    {
      "id": "dairy",
      "title": "Dairy", 
      "count": 25
    },
    {
      "id": "organic",
      "title": "Organic",
      "count": 15
    },
    {
      "id": "beverages", 
      "title": "Beverages",
      "count": 30
    }
  ],
  "breadcrumbs": [
    {
      "id": "Food",
      "title": "Food"
    }
  ]
}
```

**Search at Second Level:**
```bash
curl -X GET "/api/v1/search/your-team/products?category=dairy"
```

**Response:**
```json
{
  "items": [...],
  "count": 25,
  "categories": [
    {
      "id": "milk",
      "title": "Milk",
      "count": 15
    },
    {
      "id": "cheese",
      "title": "Cheese", 
      "count": 8
    },
    {
      "id": "yogurt",
      "title": "Yogurt",
      "count": 2
    }
  ],
  "breadcrumbs": [
    {
      "id": "Food", 
      "title": "Food"
    },
    {
      "id": "dairy",
      "title": "Dairy"
    }
  ]
}
```

**Search at Leaf Level:**
```bash
curl -X GET "/api/v1/search/your-team/products?category=milk"
```

**Response:**
```json
{
  "items": [...],
  "count": 15,
  "categories": [],  // No child categories (leaf level)
  "breadcrumbs": [
    {
      "id": "Food",
      "title": "Food"
    },
    {
      "id": "dairy", 
      "title": "Dairy"
    },
    {
      "id": "milk",
      "title": "Milk"
    }
  ]
}
```

### Key Points About Response Data

**Categories Array:**
- Shows **direct children** of current category
- Includes **count** of products in each child category
- Empty array `[]` when you're at a leaf category

**Breadcrumbs Array:**
- Shows **navigation path** from root to current category
- Always starts with root category
- Includes current category as last item

## Category Tree API

Get the complete hierarchy structure:

```bash
curl -X GET "/api/v1/categories/{owner}/{entity-name}/tree"
```

**Response:**
```json
{
  "id": "Food",
  "title": "Food", 
  "children": [
    {
      "id": "dairy",
      "title": "Dairy",
      "children": [
        {
          "id": "milk",
          "title": "Milk",
          "children": []
        }
      ]
    },
    {
      "id": "organic",
      "title": "Organic",
      "children": [
        {
          "id": "beverages", 
          "title": "Beverages",
          "children": [
            {
              "id": "milk",
              "title": "Milk",
              "children": []
            }
          ]
        }
      ]
    }
  ]
}
```

## API Usage Patterns

### Browse Navigation
```bash
# Start at root
curl -X GET "/api/v1/search/your-team/products?category=Food"

# Go deeper 
curl -X GET "/api/v1/search/your-team/products?category=dairy"

# Go to specific category
curl -X GET "/api/v1/search/your-team/products?category=milk"
```

### Search Within Category
```bash
curl -X GET "/api/v1/search/your-team/products?q=organic&category=dairy"
```

### Category + Other Filters
```bash
curl -X GET "/api/v1/search/your-team/products?category=dairy&filters=brand:Happy Cow"
```

### Default Behavior (No Category)
```bash
curl -X GET "/api/v1/search/your-team/products?q=milk"
```
Automatically uses root category if entity has categories.

## Integration Examples

### Basic Category Navigation
```bash
# Get category tree
curl -X GET "/api/v1/categories/your-team/products/tree"

# Browse root category
curl -X GET "/api/v1/search/your-team/products?category=Food"

# Navigate to subcategory
curl -X GET "/api/v1/search/your-team/products?category=dairy"
```

### Search with Category Context
```bash
# Search within specific category
curl -X GET "/api/v1/search/your-team/products?q=organic&category=dairy"

# Browse category without search query
curl -X GET "/api/v1/search/your-team/products?category=milk&limit=20"
```