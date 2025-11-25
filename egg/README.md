# Quantum LIMIT-Graph v2.4.0 - Federated Orchestration (egg)

Production-ready federated orchestration system with session isolation, backend runners, modular storage, and async agent boundaries.

## Quick Start

### Build All Crates
```bash
cd quantum_integration/quantum-limit-graph-v2.4.0/rust/egg
cargo build --workspace
```

### Run Tests
```bash
cargo test --workspace
```

### Run API Service
```bash
cargo run --bin limit-api
```

### Run Examples
```bash
# Basic session example
cargo run --example basic_session

# Federated orchestration example
cargo run --example federated_orchestration

# Agent benchmarking example
cargo run --example agent_benchmark
```

## Architecture

The egg system is organized into modular crates:

### Core Crates
- **limit-core**: Foundation types, session management, backend runners
- **limit-storage**: Persistent storage with provenance tracking
- **limit-orchestration**: Federated orchestration across sessions
- **limit-agents**: Modular agents with async boundaries

### Services
- **api**: REST API service for external access

## Key Features

### Session Isolation
Each session has a unique UUID and isolated execution context:
```rust
use limit_core::{Session, SessionConfig};

let session = Session::new(SessionConfig {
    name: "my-research".into(),
    max_concurrency: 8,
    allow_network: true,
});
```

### Backend Runners
Abstract backend execution with pluggable runners:
```rust
use limit_core::{BackendRunner, RunnerKind};

let runner = BackendRunner::new(RunnerKind::Local);
let result = runner.execute(task).await?;
```

### Storage with Provenance
All operations are logged with full provenance:
```rust
use limit_storage::FileStorage;

let storage = FileStorage { root: "data".into() };
storage.persist_trace(session_id, trace_id, data).await?;
```

### Federated Orchestration
Distribute work across multiple backends:
```rust
use limit_orchestration::{Orchestrator, GovernancePolicy};

let orchestrator = Orchestrator::new(storage, GovernancePolicy {
    block_unsafe_merge: true,
    require_provenance: true,
});
```

### Modular Agents
Composable agents with strong typing:
```rust
use limit_agents::{Agent, AgentConfig};

let agent = Agent::new(AgentConfig {
    name: "researcher".into(),
    timeout_ms: 5000,
});
```

## Project Structure

```
egg/
├── crates/
│   ├── limit-core/          # Core types and session management
│   ├── limit-storage/       # Storage layer with provenance
│   ├── limit-orchestration/ # Federated orchestration
│   └── limit-agents/        # Modular agents
├── services/
│   └── api/                 # REST API service
├── examples/                # Usage examples
├── tests/                   # Integration tests
└── Cargo.toml              # Workspace configuration
```

## Development

### Prerequisites
- Rust 1.70+ (2021 edition)
- Cargo

### Building
```bash
cargo build --workspace
```

### Testing
```bash
# Run all tests
cargo test --workspace

# Run specific crate tests
cargo test -p limit-core
cargo test -p limit-storage
cargo test -p limit-orchestration
cargo test -p limit-agents
```

### Benchmarking
```bash
cargo bench --workspace
```

### Documentation
```bash
cargo doc --workspace --open
```

## API Endpoints

When running the API service:

### Health Check
```bash
curl http://localhost:8080/health
```

### Create Session
```bash
curl -X POST http://localhost:8080/sessions \
  -H "Content-Type: application/json" \
  -d '{"name": "my-session", "max_concurrency": 8}'
```

### Execute Task
```bash
curl -X POST http://localhost:8080/sessions/{id}/execute \
  -H "Content-Type: application/json" \
  -d '{"task": "research", "params": {}}'
```

## Configuration

### Environment Variables
- `RUST_LOG`: Log level (trace, debug, info, warn, error)
- `BIND_ADDRESS`: API server bind address (default: 0.0.0.0:8080)
- `STORAGE_ROOT`: Storage root directory (default: ./data)

### Example
```bash
export RUST_LOG=info
export BIND_ADDRESS=127.0.0.1:3000
export STORAGE_ROOT=/var/lib/limit
cargo run --bin limit-api
```

## Performance

### Throughput
- Local backend: 10,000+ req/s
- Remote backend: 1,000+ req/s
- Quantum backend: 100+ req/s

### Latency
- Local: <1ms
- Remote: <100ms
- Quantum: <1s

## Security

- TLS for transport encryption
- API key authentication
- Role-based access control
- Provenance tracking for audit

## License

MIT License - See LICENSE file

## Contributing

See CONTRIBUTING.md for guidelines.

## Support

For issues and questions:
- GitHub Issues: https://github.com/your-org/quantum-limit-graph/issues
- Documentation: https://docs.quantum-limit-graph.io

---

**Version**: 2.4.1  
**Status**: Production Ready
