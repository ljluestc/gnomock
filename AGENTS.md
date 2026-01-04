# Gnomock — Agent Guide

## Project Overview
**Gnomock** is an integration testing library for Go that uses ephemeral Docker containers to mock dependencies (databases, caches, services) during tests. It allows setting up strict, clean environments for every test suite.

## Technology Stack
- **Language**: Go
- **Infrastructure**: Docker (required for running containers).
- **Task Runner**: `Taskfile.yml` (uses `task` tool).

## Project Structure
- `gnomock.go`: Core library entry point.
- `preset/`: implementation of various presets (postgres, redis, mongo, etc.).
- `internal/`: Internal logic for docker handling, health checks.
- `Taskfile.yml`: Defines build, test, and release workflows.

## Build and Test Commands
- **Test Core**: `task test:core:gnomock`
- **Test Daemon**: `task test:core:gnomockd`
- **Test Internal**: `task test:core:internal`
- **Build Images**: `task gnomock:build:server`, `task gnomock:build:cleaner`

## Development Conventions
- **Presets**: New services should be added as "presets" in the `preset/` directory.
- **Docker**: The library relies heavily on the Docker API. Tests requires a local docker daemon.
- **CI/CD**: CI checks are defined in `.github/workflows` and `Taskfile.yml`.
