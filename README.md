# BRMA - Bakery Reseller Management App

## 1. Overview

BRMA (Bakery Reseller Management App) is a platform for managing the flow of bread from bakeries to depots and from depots to resellers. It tracks distribution, issuing, reseller payouts, and unsold stock carried forward between cycles.

This repository provides scripts to automate the setup and validation of both the execution environment and the application itself.

The workflow follows a simple sequence:

- Prepare the execution environment.
- Validate the environment.
- Install the application.
- Validate the application.

## 2. Application Architecture

BRMA is organized into four macro-services, split into nine microservices:

- **Identity/Config** - authentication, user roles, and system configuration
- **Operations/Settlement** - distribution (bakery to depot), issuing (depot to reseller), and settlement of reseller payouts
- **Finance/Reporting** - profit tracking, monthly payout calculation, and reporting
- **Audit/Compliance** - activity logs and compliance checks across the platform

Diagram: `docs/images/application-architecture.png`

**Core business logic:**
- Distribution moves stock from bakery to depot.
- Issuing moves stock from depot to reseller.
- Reseller profit accumulates monthly and is settled as a payout.
- Unsold bread is not written off; it carries forward to the next cycle.

## 3. Network Architecture

Diagram: `docs/images/network-architecture.png`

- **Network zones**: frontend (Vercel), backend API (Flask), database (Supabase, Postgres-based)
- **Servers and services**: React/TypeScript frontend, Flask/Python backend, Supabase for data and auth storage
- **Communication flows**: frontend calls backend REST API over HTTPS; backend communicates with Supabase over its client library
- **Required ports and protocols**: HTTPS (443) for all external traffic; backend API default port 5000 in local development
- **External dependencies**: Supabase (database + storage), Vercel (frontend hosting)

## 4. Script Overview

| Script | Purpose | Automatically Runs |
|---|---|---|
| `EnvSetup.sh` | Installs and configures the execution environment | `EnvCheck.sh` |
| `EnvCheck.sh` | Verifies that the execution environment is correctly configured | None |
| `AppInstall.sh` | Installs and configures the application from source code | `AppCheck.sh` |
| `AppCheck.sh` | Verifies that the application has been installed correctly | None |

**Workflow:**

```
EnvSetup.sh
    ↓
EnvCheck.sh

AppInstall.sh
    ↓
AppCheck.sh
```

> Note: in this repository, environment setup is currently handled by `setup.sh` and application install by `install.sh`. Rename or symlink these to match the naming above if you want the automated chaining described in this section, or update the script names below to match your actual filenames.

## 5. Prerequisites

### Supported Operating Systems

- Ubuntu 22.04
- Ubuntu 24.04
- Debian 12
- macOS
- Windows (WSL)

### Required Dependencies

- Bash
- Git
- Curl
- Node.js (for the React/TypeScript/Vite frontend)
- Python 3.14
- Docker (optional, for containerized deployment)

### References

- [Node.js installation guide](https://nodejs.org/en/download)
- [Python installation guide](https://www.python.org/downloads/)
- [Supabase documentation](https://supabase.com/docs)
- [Vercel documentation](https://vercel.com/docs)

## 6. Environment Installation (setup.sh)

### Purpose

`setup.sh` prepares the execution environment by installing and configuring all required components (Node.js, Python, and any system packages needed by the frontend and backend).

### Usage

```bash
chmod +x setup.sh
./setup.sh
```

### Installation Steps

1. Verify execution permissions.
2. Detect the operating system.
3. Install required packages (Node.js, Python, Git, Curl).
4. Configure the execution environment.
5. Configure environment variables (`.env` file for Flask and Supabase keys).
6. Perform cleanup if required.
7. Run environment validation.

### Expected Result

```
Environment installation completed.
Launching environment validation...
```

## 7. Environment Validation

### Purpose

Verifies that the execution environment is fully operational before installing the application.

### Validation Checks

- Operating system compatibility
- Bash version
- Git installation
- Node.js and npm version
- Python version
- Environment variables (Supabase keys, JWT secret)
- File permissions

### Validation Output

```
[PASS] Operating System
[PASS] Git
[PASS] Node.js
[PASS] Python
[PASS] Environment Variables
[PASS] Permissions
```

## 8. Application Installation (install.sh)

### Purpose

`install.sh` installs and configures BRMA after the source code has been retrieved. This includes installing frontend dependencies, backend dependencies, and setting up the database connection.

### Prerequisites

- Environment successfully validated
- Source code available (`git clone` this repository)

### Usage

```bash
chmod +x install.sh
./install.sh
```

### Installation Steps

1. Verify prerequisites.
2. Install frontend dependencies (`npm install`) and backend dependencies (`pip install -r requirements.txt`).
3. Build the frontend (`npm run build`) if required.
4. Apply configuration (Flask config, Supabase connection settings, JWT secret).
5. Initialize required resources (database tables via Supabase migrations).
6. Generate required files (`.env`, build artifacts).
7. Run application validation.

### Expected Result

```
Application installation completed.
Launching application validation...
```

## 9. Application Validation

### Purpose

Verifies that BRMA is correctly installed and ready for use.

### Validation Checks

- Required dependencies (Node.js packages, Python packages)
- Configuration files (`.env`, Flask config)
- Database connectivity (Supabase connection)
- Running services (frontend dev server, Flask API)
- API availability (health check endpoint)
- File permissions

### Validation Output

```
[PASS] Configuration
[PASS] Database
[PASS] Services
[PASS] API
```

## 10. Docker Deployment

### Docker Image

```
brma-app:latest
```

### Environment Variables

| Variable | Description | Required | Default |
|---|---|---|---|
| `APP_ENV` | Application environment | Yes | production |
| `APP_PORT` | Backend API port | No | 5000 |
| `SUPABASE_URL` | Supabase project URL | Yes | N/A |
| `SUPABASE_KEY` | Supabase service key | Yes | N/A |
| `JWT_SECRET_KEY` | Secret key for JWT auth | Yes | N/A |

### Run the Container

```bash
docker run \
   --name brma-app \
   -p 5000:5000 \
   --env-file .env \
   brma-app:latest
```

If a `docker-compose.yml` is provided:

```bash
docker compose up -d
```

## 11. Troubleshooting

| Issue | Possible Cause | Recommended Solution |
|---|---|---|
| `setup.sh` fails on package install | Unsupported OS or missing package manager | Check the supported OS list, run with `sudo` if needed |
| `install.sh` fails to connect to Supabase | Missing or incorrect `SUPABASE_URL` / `SUPABASE_KEY` | Verify `.env` values against your Supabase project settings |
| Frontend fails to build | Node.js version mismatch | Confirm Node.js version matches project requirements |
| `AppCheck` fails on Database | Supabase project paused or unreachable | Check Supabase project status and network access |
| Permission denied on scripts | Script not executable | Run `chmod +x setup.sh install.sh` |
| JWT auth errors | Missing or mismatched `JWT_SECRET_KEY` | Ensure the same secret is used across environments |

## 12. References

- [Project repository](#)
- [React documentation](https://react.dev)
- [Flask documentation](https://flask.palletsprojects.com)
- [Supabase documentation](https://supabase.com/docs)
- [Vercel documentation](https://vercel.com/docs)
- [flask-jwt-extended documentation](https://flask-jwt-extended.readthedocs.io)
