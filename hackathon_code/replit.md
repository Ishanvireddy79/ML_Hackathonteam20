# Workspace

## Overview

pnpm workspace monorepo using TypeScript + Python. Hosts a Real-Time Phishing URL Detector web application.

## Stack

- **Monorepo tool**: pnpm workspaces
- **Node.js version**: 24
- **Python version**: 3.11
- **Package manager**: pnpm (Node), uv (Python)
- **TypeScript version**: 5.9
- **API framework**: Express 5
- **ML framework**: scikit-learn (Random Forest + TF-IDF)
- **Database**: PostgreSQL + Drizzle ORM
- **Validation**: Zod (`zod/v4`), `drizzle-zod`
- **API codegen**: Orval (from OpenAPI spec)
- **Build**: esbuild (CJS bundle)

## Structure

```text
artifacts-monorepo/
├── artifacts/              # Deployable applications
│   ├── api-server/         # Express API server (proxies to ML service)
│   ├── phishing-detector/  # React + Vite frontend
│   └── ml-service/         # Python Flask ML service (Random Forest)
├── lib/                    # Shared libraries
│   ├── api-spec/           # OpenAPI spec + Orval codegen config
│   ├── api-client-react/   # Generated React Query hooks
│   ├── api-zod/            # Generated Zod schemas from OpenAPI
│   └── db/                 # Drizzle ORM schema + DB connection
├── scripts/                # Utility scripts
└── ...
```

## Key Features

- **Real-Time Phishing URL Detection** using Random Forest ML model
- **URL Feature Extraction**: length, dots, hyphens, special chars, digits, subdomain depth, HTTPS, IP detection
- **TF-IDF n-gram analysis**: character-level n-grams (2,4), 5000 max features
- **Model auto-training**: trains on startup if no pre-trained model found
- **Dataset support**: uses `PhiUSIIL_Phishing_URL_Dataset.csv` if present, otherwise generates synthetic training data
- **Model persistence**: saves trained model as `phishing_model.pkl` and `tfidf.pkl`

## Services

1. **ML Service** (Python Flask, port 5001) - `/ml/predict` and `/ml/status` endpoints
2. **API Server** (Node.js/Express, port 8080) - `/api/predict` and `/api/ml-status` proxies
3. **Frontend** (React/Vite, port 22772) - serves at `/`

## API Endpoints

- `GET /api/healthz` - Health check
- `POST /api/predict` - Predict if URL is phishing
- `GET /api/ml-status` - Get ML model training status

## Using the Real Dataset

For best accuracy, place `PhiUSIIL_Phishing_URL_Dataset.csv` in `artifacts/ml-service/`. The model will use it automatically on next startup. Without it, synthetic data is used for demo purposes.

## TypeScript & Composite Projects

Every package extends `tsconfig.base.json` which sets `composite: true`. The root `tsconfig.json` lists all packages as project references.

## Root Scripts

- `pnpm run build` — runs `typecheck` first, then recursively runs `build` in all packages that define it
- `pnpm run typecheck` — runs `tsc --build --emitDeclarationOnly` using project references
