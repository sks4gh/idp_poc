---
layout: default
title: Home
nav_order: 1
description: "Jumbo Search Platform - comprehensive search functionality for internal teams"
permalink: /
---

# Jumbo Search Platform Documentation

A Spring Boot microservice providing comprehensive search functionality for Jumbo's internal teams. The platform supports advanced features including multi-category hierarchies, faceted search, and MongoDB Atlas Search integration.
{: .fs-6 .fw-300 }

## Platform Access

### Internal APIs (VPN Required)

| Environment | Base URL | Swagger Documentation |
|-------------|----------|----------------------|
| Production | `https://search-platform.internal.prod.cloud.jumbo.com/` | [Swagger UI](https://search-platform.internal.prod.cloud.jumbo.com/swagger-ui/index.html#/){: .btn } |
| Acceptance | `https://search-platform.internal.acc.cloud.jumbo.com/` | [Swagger UI](https://search-platform.internal.acc.cloud.jumbo.com/swagger-ui/index.html#/){: .btn } |
| Test | `https://search-platform.internal.test.cloud.jumbo.com/` | [Swagger UI](https://search-platform.internal.test.cloud.jumbo.com/swagger-ui/index.html#/){: .btn } |

**Note**: You need to be connected to Jumbo VPN or within the Jumbo network (for service-to-service calls) to access these endpoints.

### Public Search API

For search-only access without VPN:
- **Endpoint**: `https://jumbo.com/api/search/{owner}/{collectionName}`
- **Example**: `https://jumbo.com/api/search/store-employee-team/products?q=melk`

## Documentation

- [🚀 Getting Started](getting-started.md) - Setup and first steps
- [📝 Entities](entities.md) - Define your data structure
- [📊 Entries](entries.md) - Add and manage your data
- [🔍 Search](search.md) - Search API and features
- [🏷️ Faceting](faceting.md) - Field-based filtering
- [📂 Category Filter](category-filter.md) - Hierarchical navigation

## What Teams Get

### Core Features
- **Dynamic Entity Management**: Define entities with custom properties and validation rules
- **Full-Text Search**: MongoDB Atlas Search with typo tolerance
- **Faceted Search**: Filter and count results by various attributes
- **Multi-Category Support**: Products can belong to multiple category hierarchies
- **Category Trees**: Hierarchical navigation with breadcrumbs

### Current Users
- **Products Team**: Managing product catalog with food categories
- **Store Employee App**: Location-based product search for store staff

## Platform Benefits

### For Your Team
- **Easy Integration**: Add search to your data in minutes
- **Flexible Schema**: Define any entity structure you need
- **Zero Maintenance**: Platform handles indexing and optimization
- **Rich APIs**: Full-text search, filtering, category navigation

### For Store Operations
- **Faster Product Location**: Employees find items quickly with category filters
- **Multiple Organization Views**: Same products, different category structures
- **Intuitive Navigation**: Hierarchical browsing with clear breadcrumbs

## Need Help?

- **Quick Start**: Follow our [Getting Started Guide](getting-started.md)
- **API Questions**: Check the [API Reference](api-reference.md)
- **Category Setup**: See [Category Features](category-features.md)
- **Integration Help**: Contact the Platform Team

---

**Platform Team Contact**: Internal Slack #search-platform  
**Status**: ✅ Production Ready  
**Current Version**: Latest from `master` branch