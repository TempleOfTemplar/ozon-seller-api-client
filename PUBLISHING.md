# Publishing Guide

This document describes how to publish the `ozon-seller-api-client` package to npm.

## Prerequisites

1. **npm account**: Create an account at [npmjs.com](https://www.npmjs.com/)
2. **Login**: Run `npm login` and enter your credentials
3. **Unique name**: Verify the package name `ozon-seller-api-client` is available on npm

## Pre-publication Checklist

- [ ] Update version in `package.json` (follow [semver](https://semver.org/))
- [ ] Ensure all tests pass (if any)
- [ ] Build the project: `npm run build`
- [ ] Verify `dist/` directory contains compiled files
- [ ] Check that `.npmignore` excludes development files
- [ ] Review `README.md` is up-to-date

## Build

```bash
npm run build
```

This compiles TypeScript to the `dist/` directory with:
- ESM JavaScript files (`.js`)
- TypeScript declaration files (`.d.ts`)
- Source maps (`.js.map`, `.d.ts.map`)

## Test Package Locally

Before publishing, test the package:

```bash
# Create a test project
mkdir test-package && cd test-package
npm init -y

# Install the local package
npm install ../ozon-seller-api-client

# Create a test file
cat > test.ts << 'EOF'
import { OzonSellerAPI, HttpClient } from 'ozon-seller-api-client';

const http = new HttpClient({
  securityWorker: async () => ({
    'Client-Id': 'test',
    'Api-Key': 'test',
  }),
});

const api = new OzonSellerAPI(http);
console.log('API client created successfully');
EOF

# Run the test
npx ts-node test.ts
```

## Publish

### Dry Run (Recommended)

First, do a dry run to see what will be published:

```bash
npm pack --dry-run
```

This shows the exact files that will be included in the package.

### Publish to npm

```bash
npm publish
```

For a scoped package (private), you might need:
```bash
npm publish --access public
```

## After Publishing

1. **Verify**: Check the package on https://www.npmjs.com/package/ozon-seller-api-client
2. **Tag**: Consider creating a GitHub release with the same version
3. **Announce**: Share with your users

## Troubleshooting

### "Package name already taken"
- Use a scoped name: `@your-username/ozon-seller-api-client`
- Update `package.json` name field accordingly

### "You need to authorize this browser"
- Run `npm login` again
- Or use `npm adduser` for two-factor auth

### Build errors
- Ensure TypeScript is installed: `npm install`
- Check `tsconfig.json` settings
- Verify source file compiles: `npx tsc --noEmit`

### Files missing from package
- Check `.npmignore` rules
- Run `npm pack --dry-run` to inspect contents
- Ensure `dist/` exists after build

## Versioning

Follow semantic versioning (semver):
- **Major** (`1.0.0` → `2.0.0`): Breaking changes
- **Minor** (`1.0.0` → `1.1.0`): New features, backward compatible
- **Patch** (`1.0.0` → `1.0.1`): Bug fixes

Update version in `package.json` before each publish.

## Continuous Deployment

Consider setting up automated publishing with:
- **GitHub Actions**: Use `npm publish` on tag creation
- **semantic-release**: Automate versioning and publishing

Example GitHub Action:
```yaml
name: Publish to npm
on:
  push:
    tags:
      - 'v*'
jobs:
  publish:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: '18'
          registry-url: 'https://registry.npmjs.org'
      - run: npm ci
      - run: npm run build
      - run: npm publish
        env:
          NODE_AUTH_TOKEN: ${{ secrets.NPM_TOKEN }}
```
