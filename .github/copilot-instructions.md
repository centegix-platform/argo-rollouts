# Argo Rollouts - Copilot Instructions

This repository contains Argo Rollouts, a Kubernetes controller and set of CRDs which provide advanced deployment capabilities such as blue-green, canary, canary analysis, experimentation, and progressive delivery features to Kubernetes.

## Technology Stack

- **Primary Language**: Go (version 1.24+)
- **UI Framework**: React 17 with TypeScript
- **Build Tool**: Make
- **Package Manager**: Go modules for backend, Yarn for frontend
- **Kubernetes**: Custom Resource Definitions (CRDs) and controllers
- **Testing**: Go testing framework, Jest for UI
- **Linting**: golangci-lint for Go code

## Repository Structure

- `cmd/` - Main application entry points and executables
  - `rollouts-controller/` - Main rollout controller
- `pkg/` - Core Go packages
  - `apis/rollouts/v1alpha1/` - API type definitions and CRDs
  - `apiclient/` - API client code
  - `kubectl-argo-rollouts/` - Plugin CLI code
- `rollout/` - Rollout controller implementation
- `controller/` - Controller logic
- `analysis/` - Analysis controller
- `experiments/` - Experiment controller
- `service/` - Service controller
- `ingress/` - Ingress controller
- `metric/` - Metric providers integration
- `metricproviders/` - Specific metric provider implementations
- `ui/` - React-based web UI
  - `src/` - UI source code
- `docs/` - Documentation (MkDocs-based)
- `test/` - Test files and e2e tests
- `manifests/` - Kubernetes manifests and CRDs
- `examples/` - Example rollout configurations
- `hack/` - Build and development scripts
- `utils/` - Utility packages

## Development Workflow

### Initial Setup

Before contributing, install required tools:
- Docker
- Go (1.24+)
- kubectl
- kustomize (>= 4.5.5)
- k3d (recommended for local testing)
- golangci-lint
- protoc (for protobuf generation)
- yarn (for UI development)

Quick install on macOS:
```bash
brew install go kubectl kustomize golangci-lint protobuf k3d
```

Install all local development tools:
```bash
make install-tools-local
```

### Building

- **Build controller**: `make controller`
- **Build plugin**: `make plugin`
- **Build UI**: `cd ui && yarn install && yarn build`
- **Build everything**: `make all`

### Testing

- **Run unit tests**: `make test`
- **Run tests with coverage**: `make coverage`
- **Run e2e tests**: 
  1. Start cluster: `k3d cluster create`
  2. Setup: `kubectl create ns argo-rollouts && kubectl apply -k manifests/crds && kubectl apply -f test/e2e/crds`
  3. Start controller: `make start-e2e`
  4. Run tests: `make test-e2e`
- **Run UI tests**: `cd ui && yarn test`

### Linting

- **Lint Go code**: `make lint` (runs golangci-lint with auto-fix)
- Always run linting before committing

### Code Generation

When making changes to API types or protobuf definitions, regenerate code:
- **Generate all code**: `make codegen`
- **Generate CRDs**: `make gen-crd`
- **Generate protobuf**: `make gen-proto`
- **Generate mocks**: `make gen-mocks`
- **Generate OpenAPI**: `make gen-openapi`

### Running Locally

Run the controller locally:
```bash
go run ./cmd/rollouts-controller/main.go
```

Run the UI locally:
```bash
kubectl argo rollouts dashboard  # or: make plugin && ./dist/kubectl-argo-rollouts dashboard
cd ui && yarn install && yarn start
```

## Code Standards

### Go Code Standards

1. Follow standard Go best practices and idiomatic patterns
2. Use dependency injection patterns where appropriate
3. Maintain existing code structure and organization
4. All exported functions and types must have documentation comments
5. Keep functions focused and single-purpose
6. Use meaningful variable and function names

### Before Each Commit

- Run `make lint` to ensure proper code formatting
- Run `make test` to ensure all tests pass
- If you modified CRDs or types, run `make gen-crd` to regenerate manifests
- If you modified protobuf definitions, run `make gen-proto`

### Testing Requirements

- Add unit tests for new functionality
- Update existing tests when modifying behavior
- E2E tests should be added for significant feature changes
- Tests should be consistent with existing test patterns in the repository
- Mock external dependencies using the generated mocks

### Documentation

- Update documentation in `docs/` for user-facing changes
- Preview docs locally: `make serve-docs` (available at http://localhost:8000)
- Build docs: `make docs`

### UI Development

- Follow React and TypeScript best practices
- Use existing component patterns from the `ui/src` directory
- Format code consistently with existing style
- Run `yarn test` before committing UI changes

## Key Guidelines for Contributors

1. **CRD Changes**: When modifying CRDs (in `pkg/apis/rollouts/v1alpha1/`), always regenerate them with `make gen-crd` before submitting PRs. CRDs are controlled by annotations in the types files (e.g., `analysis_types.go`)

2. **Controller Architecture**: Argo Rollouts consists of multiple specialized controllers:
   - Rollout Controller (main orchestrator)
   - Service Controller
   - Ingress Controller
   - Experiment Controller
   - AnalysisRun Controller
   
   Changes should respect this separation of concerns.

3. **Traffic Shaping**: The project integrates with various ingress controllers and service meshes (NGINX, ALB, Istio, etc.). Be mindful of these integrations when making changes.

4. **Metric Providers**: The project supports multiple metric providers (Prometheus, Datadog, New Relic, etc.). Follow existing patterns when adding or modifying metric provider integrations.

5. **Backward Compatibility**: Maintain backward compatibility with existing CRDs and APIs unless explicitly planning a breaking change.

6. **Pre-commit Checks**: Run `make precheckin` which executes both tests and linting.

## Common Tasks

### Adding a New Feature

1. Update relevant type definitions in `pkg/apis/rollouts/v1alpha1/`
2. Run `make gen-crd` to regenerate CRDs
3. Implement controller logic in appropriate controller package
4. Add unit tests alongside implementation
5. Add e2e tests in `test/e2e/`
6. Update documentation in `docs/`
7. Run `make precheckin` to verify

### Fixing a Bug

1. Add a test that reproduces the bug
2. Fix the bug with minimal code changes
3. Verify the test now passes
4. Run `make precheckin`
5. Update documentation if the bug fix changes behavior

### Updating Dependencies

- **Go dependencies**: Update `go.mod` and run `go mod tidy`
- **Kubernetes libraries**: Use `./hack/update-k8s-dependencies.sh`
- **UI dependencies**: Update `ui/package.json` and run `yarn install`

## CI/CD

The repository uses GitHub Actions for continuous integration. Key checks include:
- Linting with golangci-lint
- Unit tests
- E2E tests
- Docker image builds
- Documentation builds

All checks must pass before merging pull requests.

## Additional Resources

- Full documentation: https://argo-rollouts.readthedocs.io/
- Contributing guide: `docs/CONTRIBUTING.md`
- User discussions: https://github.com/argoproj/argo-rollouts/discussions
- Slack: #argo-rollouts channel
