# Gnomock — Agent Guide
 
 ## Project overview
 
 Gnomock is an integration / end-to-end testing toolkit built around ephemeral Docker containers.
 
 From `README.md`, it can be used in two ways:
 
 - As a Go library (import and call `gnomock.Start(...)` with a preset).
 - As a daemon over HTTP (to use from non-Go languages).
 
 Both approaches require a local Docker daemon. External `DOCKER_HOST` support is described as experimental.
 
 ## Tech stack
 
 - **Language**: Go (`go.mod` uses `go 1.24.0`).
 - **Container runtime**: Docker (the library talks to the Docker API).
 - **Task runner**: `Taskfile.yml` (the repo uses `task` to group CI-like commands).
 
 ## Repo layout
 
 - `gnomock.go`, `options.go`, `docker.go`: core library API and Docker integration.
 - `preset/`: official presets (Postgres, Redis, Kafka, etc.). Presets encapsulate image/version + setup/seed logic.
 - `internal/`: internal packages, including daemon implementation and utility code.
 - `cmd/`:
   - `cmd/cleaner/`: builds a helper image used to cleanup resources.
   - (daemon/server code is under `internal/` per `Taskfile.yml` test targets).
 - `swagger/` + `docs/`: OpenAPI spec and server documentation.
 - `Taskfile.yml`: build/test/release tasks (including docker image build/manifest workflows).
 
 ## Build and test commands
 
 ### Go tests
 
 The `Taskfile.yml` defines the main test groupings (they run `go test` with `-race` and coverage):
 
 - **Core package tests**:
 
 ```bash
 task test:core:gnomock
 ```
 
 - **Daemon tests**:
 
 ```bash
 task test:core:gnomockd
 ```
 
 - **Selected internal package tests**:
 
 ```bash
 task test:core:internal
 ```
 
 Notes:
 
 - These tasks set `GOMAXPROCS=2` and generate coverage profiles like `gnomock-cover.txt`.
 - Many tests will pull and run real Docker images; test timeouts can be impacted by image download speed (see `README.md` troubleshooting).
 
 ### Docker images (daemon + cleaner)
 
 - **Build daemon/server images**:
 
 ```bash
 task gnomock:build:server
 ```
 
 - **Build cleaner images**:
 
 ```bash
 task gnomock:build:cleaner
 ```
 
 - **Run a local daemon container for testing**:
 
 ```bash
 task gnomock:run
 ```
 
 ## How to use (quick pointers)
 
 - Go library usage example is in `README.md` (e.g., Postgres preset + `gnomock.Start`).
 - Daemon usage and HTTP API are documented under `docs/` (see `docs/server.md`).
 
 ## Development conventions
 
 - Add new integrations as presets under `preset/<name>`.
 - Keep preset versions aligned with what CI actually tests (the README calls out “supported versions”).
 
 ## Security considerations
 
 - Running the daemon/container typically requires mounting `/var/run/docker.sock` into the container (see `task gnomock:run`). Treat this as privileged access.
 - If you use file-based setup via the daemon, you must ensure the files exist inside the daemon container (README troubleshooting suggests mounting the working directory).

## AI Agent Workflow

### 1. Requirements Discovery
- **Primary Source**: `PRD.md` (Always prioritize this if present).
- **Secondary**: `requirements.txt`, `README.md`, or specific task files.
- **Goal**: Understand the full scope before writing code.

### 2. Implementation Protocol
- **Branching**: Work on a dedicated feature branch (e.g., `feat/implementation-details`).
- **Development**:
  - Analyze code structure.
  - Implement changes in `src/` or relevant directories.
  - Adhere to existing code style.
- **Verification**:
  - Run build commands (see above).
  - Run test suite (see above).
  - Ensure no regressions.

### 3. Delivery
- **Commit**: Use conventional commits (e.g., `feat: ...`, `fix: ...`).
- **PR Creation**:
  - Push branch: `git push -u origin <branch-name>`
  - Create a Pull Request against the main branch.
  - Summary: Link to `PRD.md` requirements solved.

## Task Implementation
1. **Analyze Requirements**: Refer to `README.md` for detailed feature specifications and system design.
2. **Implementation**: Modify source code in the respective directories (e.g., `src/`, `internal/`).
3. **Verification**: Run provided build and test commands (see above) to ensure correctness.
4. **Push Changes**:
   - Commit changes: `git commit -m "feat: implement <feature>"`
   - Push to remote: `git push origin <branch-name>`
