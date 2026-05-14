# Ozon Seller API Client

TypeScript client for [Ozon Seller API](https://developer.ozon.ru/). This package provides a type-safe, autocompleted interface to interact with Ozon's seller marketplace API.

## Installation

```bash
npm install ozon-seller-api-client
```

## Requirements

- Node.js >= 18.0.0 (for native fetch support)
- TypeScript >= 5.0.0 (for development)

## Quick Start

```typescript
import { OzonSellerAPI, HttpClient } from 'ozon-seller-api-client';

// Create HTTP client with your API key
const httpClient = new HttpClient({
  baseUrl: 'https://api-seller.ozon.ru',
  securityWorker: async (securityData) => {
    return {
      'Client-Id': 'your-client-id',
      'Api-Key': 'your-api-key',
    };
  },
});

// Create API instance
const api = new OzonSellerAPI(httpClient);

// Use API methods
const sellerInfo = await api.v1.sellerApiSellerInfo();
console.log('Seller info:', sellerInfo.data);
```

## Configuration

### HttpClient Options

The `HttpClient` accepts the following configuration:

```typescript
interface ApiConfig<SecurityDataType = unknown> {
  baseUrl?: string;           // Base URL (default: 'https://api-seller.ozon.ru')
  securityWorker?: (data: SecurityDataType) => Promise<Record<string, string>>;
  customFetch?: typeof fetch; // Custom fetch implementation
}
```

### Authentication

Ozon Seller API uses header-based authentication with `Client-Id` and `Api-Key`. Configure this via the `securityWorker`:

```typescript
const httpClient = new HttpClient({
  securityWorker: async () => ({
    'Client-Id': process.env.OZON_CLIENT_ID!,
    'Api-Key': process.env.OZON_API_KEY!,
  }),
});
```

## Usage Examples

### Get Seller Information

```typescript
const response = await api.v1.sellerApiSellerInfo();
console.log(response.data);
```

### List Orders

```typescript
const response = await api.v1.postingV1PostingFbsList({
  dir: 'ASC',
  limit: 100,
  offset: 0,
  since: '2024-01-01T00:00:00Z',
  to: '2024-12-31T23:59:59Z',
});
console.log(response.data);
```

### Get Product List

```typescript
const response = await api.v1.productV1ProductList({
  limit: 100,
  offset: 0,
});
console.log(response.data);
```

### Create Order

```typescript
const response = await api.v2.orderV2CreateOrder({
  postings: [
    {
      order_id: 123456,
      products: [
        {
          product_id: 789,
          quantity: 1,
        },
      ],
    },
  ],
});
console.log(response.data);
```

## API Structure

The API client is organized by version and endpoint:

- `api.v1.*` - Version 1 endpoints
- `api.v2.*` - Version 2 endpoints

Each endpoint method accepts an optional `RequestParams` object that can include:
- `body` - Request body
- `query` - Query parameters
- `headers` - Additional headers
- `cancelToken` - Cancel token for aborting requests

## Type Safety

All types are exported from the package for full TypeScript support:

```typescript
import type {
  SellerApiSellerInfoData,
  PostingV1PostingFbsListRequest,
  DeliveryTypeEnum,
} from 'ozon-seller-api-client';
```

## Error Handling

API errors throw an `HttpResponse` with error data:

```typescript
try {
  const response = await api.v1.sellerApiSellerInfo();
  console.log(response.data);
} catch (error) {
  if (error instanceof Error) {
    console.error('API error:', error.message);
  }
}
```

## Development

### Build

```bash
npm run build
```

### Generate API from Swagger

If you need to regenerate the client from the Ozon API specification:

```bash
npm run generate
```

The generated code will be placed in `src/OzonSellerAPI.ts`.

## Publishing

This package is ready to be published to npm:

```bash
npm publish
```

Before publishing:
1. Ensure you have an npm account (`npm login`)
2. Update the version in `package.json`
3. Run `npm run build` to generate the distribution files
4. Test the package locally with `npm pack`

## License

MIT - see [LICENSE](LICENSE) for details.

## Links

- [Ozon Seller API Documentation](https://developer.ozon.ru/)
- [Ozon for Developers](https://dev.ozon.ru/)
- [Report Issues](https://github.com/your-repo/issues)

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.
