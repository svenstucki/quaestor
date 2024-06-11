# Invoice Generator

## Setup

Uses old versions of frontend components, install like this to circumvent python 2 dependency:

```
npm install node-sass@9.0 --force
npm install sass-loader@^10
```

Uses [this](https://stackoverflow.com/a/72219174/182689) hack to support newer OpenSSL library versions.

## Running

Build the frontend and start the backend:

```
(cd frontend/ && npm run build)
cargo run
```

Open http://localhost:8000.

### Development

Start the backend:

```
cargo run
```

Start the frontend development server:

```
cd frontend/
npm run build
```

Open http://localhost:8080 to access the frontend dev server (not the backend!).
