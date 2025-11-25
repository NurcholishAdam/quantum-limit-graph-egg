# Quantum LIMIT-Graph v2.4.2 - Federated Orchestration (egg)

Production-ready federated orchestration system with advanced backend runners, governance policies, RD computation, and multi-backend storage.

## 🚀 Quick Start

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
cargo run --bin api
```

### Run Examples
```bash
# Basic session example
cargo run --example basic_session

# Federated orchestration example
cargo run --example federated_orchestration

# Agent benchmarking example
cargo run --example agent_benchmark

# Advanced features demo (NEW!)
cargo run --example advanced_features
```

---

## 📦 Architecture

The egg system is organized into modular crates:

### Core Crates
- **limit-core**: Foundation types, session management, backend runners, RD computation
- **limit-storage**: Multi-backend storage (File, KV, SQLite, PostgreSQL)
- **limit-orchestration**: Federated orchestration with governance policies
- **limit-agents**: Modular agents with async boundaries

### Services
- **api**: REST API service with hardened endpoints

---

## ✨ Key Features

### 1. Advanced Backend Runners (NEW!)

Execute code across multiple backends with complete session isolation:

```rust
// Python Runner
let python = PythonRunner::new("python3".to_string())
    .with_venv("/opt/venv".to_string());
let result = python.execute_isolated(code, session_id, trace_id).await?;

// Llama Runner
let llama = LlamaRunner::new("/models/llama-7b.gguf".to_string(), 4096);
let result = llama.execute_isolated(prompt, session_id, trace_id).await?;

// Large Model Runner (OpenAI, Anthropic, etc.)
let gpt = LargeModelRunner::new("openai".to_string(), "gpt-4".to_string())
    .with_api_key("sk-...".to_string());
let result = gpt.execute_isolated(prompt, session_id, trace_id).await?;
```

**Features**:
- Complete session isolation
- Health checks
- Configurable timeouts and memory limits
- Environment variable support

---

### 2. Enhanced Governance Policies (NEW!)

Protect your system with advanced governance rules:

```rust
let policy = GovernancePolicy::strict();
let orchestrator = Orchestrator::new(storage, policy);

// Automatic jailbreak detection
orchestrator.flag_trace(trace_id, TraceFlagInfo {
    flag: TraceFlag::Jailbreak,
    reason: "Detected jailbreak attempt".to_string(),
    severity: 10,
    auto_detected: true,
    timestamp: chrono::Utc::now(),
}).await?;

// Validate merge (blocks flagged traces)
orchestrator.validate_merge(session_id, trace_id).await?;
```

**Trace Flags**:
- `Jailbreak`: Prompt injection attempts
- `Anomaly`: Statistical anomalies
- `HighRisk`: High-risk operations
- `Unsafe`: Unsafe code patterns
- `Malicious`: Malicious activity
- `Unverified`: Missing provenance

**Policy Presets**:
- `GovernancePolicy::permissive()`: Minimal restrictions
- `GovernancePolicy::default()`: Balanced security
- `GovernancePolicy::strict()`: Maximum security

---

### 3. RD Computation with FGW Distortion (NEW!)

Optimize performance using rate-distortion theory:

```rust
let config = FGWConfig {
    alpha: 0.5,  // Balance feature vs. structure
    epsilon: 0.01,
    max_iter: 100,
    tol: 1e-6,
};

let mut rd_comp = RDComputation::new(config);

// Add refinement steps
for (distortion, variance) in refinement_steps {
    rd_comp.add_refinement_point(distortion, variance);
}

// Find optimal operating point
if let Some(knee) = rd_comp.find_knee_point() {
    println!("Optimal: Rate={}, Distortion={}", knee.reward, knee.difficulty);
}
```

**Features**:
- FGW (Fused Gromov-Wasserstein) distortion computation
- Automatic knee detection using Menger curvature
- Shannon rate-distortion theory
- Per-refinement tracking

---

### 4. Multi-Backend Storage (NEW!)

Swap storage backends without code changes:

```rust
// File Storage (default)
let storage = FileStorage { root: "data".into() };

// KV Storage (embedded, fast)
let storage = KVStorage::new("data/kv")?;

// SQLite (with feature flag)
#[cfg(feature = "sqlite")]
let storage = SqliteStorage::new("sqlite://data.db").await?;

// PostgreSQL (with feature flag)
#[cfg(feature = "postgres")]
let storage = PostgresStorage::new("postgres://localhost/db").await?;
```

**Build with Features**:
```bash
# SQLite support
cargo build --features sqlite

# PostgreSQL support
cargo build --features postgres

# All databases
cargo build --features all-databases
```

---

### 5. Hardened API Endpoints (NEW!)

RESTful API for all operations:

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/health` | GET | Health check |
| `/traces` | POST | Create trace |
| `/traces/:id/provenance` | POST | Append provenance |
| `/rd/knee` | GET | Get RD knee point |
| `/artifacts/export` | POST | Export artifacts |
| `/governance/stats` | GET | Governance statistics |
| `/governance/flag` | POST | Flag a trace |
| `/sessions` | POST | Create session |
| `/sessions/:id` | GET | Get session info |

**Example Usage**:
```bash
# Start API server
cargo run --bin api

# Create a trace
curl -X POST http://localhost:8080/traces \
  -H "Content-Type: application/json" \
  -d '{"session_id": "...", "data": {"key": "value"}}'

# Get RD knee point
curl http://localhost:8080/rd/knee
```

---

### 6. Session Isolation

Each session operates in complete isolation:

```rust
let session = Session::new(SessionConfig {
    name: "my-session".into(),
    max_concurrency: 4,
    allow_network: false,
});
```

**Isolation Guarantees**:
- Separate storage namespaces
- Independent backend contexts
- Isolated provenance chains
- Per-session governance

---

## 📚 Documentation

- **[QUICK_START.md](QUICK_START.md)**: Get started in 5 minutes
- **[DEVELOPER_GUIDE.md](DEVELOPER_GUIDE.md)**: Comprehensive development guide
- **[ADVANCED_FEATURES_COMPLETE.md](ADVANCED_FEATURES_COMPLETE.md)**: Advanced features documentation
- **[EGG_STRUCTURE_VERIFICATION.md](EGG_STRUCTURE_VERIFICATION.md)**: Architecture verification
- **[FEDERATED_ARCHITECTURE.md](FEDERATED_ARCHITECTURE.md)**: Federated design details
- **[IMPLEMENTATION_COMPLETE.md](IMPLEMENTATION_COMPLETE.md)**: Implementation status

---

## 🧪 Testing

```bash
# Run all tests
cargo test --workspace

# Run with all features
cargo test --workspace --features all-databases

# Run specific crate tests
cargo test -p limit-core
cargo test -p limit-storage
cargo test -p limit-orchestration

# Run with output
cargo test -- --nocapture
```

---

## 🐳 Docker

```bash
# Build image
docker build -t quantum-limit-graph-egg:latest .

# Run with docker-compose
docker-compose up

# Run API service
docker run -p 8080:8080 quantum-limit-graph-egg:latest
```

---

## 🔧 Configuration

### Environment Variables

```bash
# API Configuration
export API_PORT=8080
export API_HOST=0.0.0.0

# Storage Backend
export STORAGE_BACKEND=postgres  # file, kv, sqlite, postgres
export DATABASE_URL=postgres://localhost/quantum_limit

# Governance Policy
export GOVERNANCE_POLICY=strict  # permissive, default, strict

# RD Computation
export FGW_ALPHA=0.5
export FGW_EPSILON=0.01
```

### Cargo Features

```toml
[features]
default = []
sqlite = ["limit-storage/sqlite"]
postgres = ["limit-storage/postgres"]
all-databases = ["sqlite", "postgres"]
```

---

## 📊 Performance

### Backend Runners
- **Python**: ~10-50ms overhead
- **Llama**: Model-dependent
- **Large Models**: Network + API latency

### Storage Backends
- **FileStorage**: ~1-5ms per operation
- **KVStorage**: ~0.1-1ms per operation
- **SQLite**: ~1-10ms per operation
- **PostgreSQL**: ~5-20ms per operation

### RD Computation
- **FGW Distortion**: O(n²) complexity
- **Knee Detection**: O(n) complexity
- **Rate Computation**: O(1) complexity

---

## 🔒 Security

### Best Practices
1. Use `GovernancePolicy::strict()` in production
2. Enable `auto_quarantine` for high-risk environments
3. Always enable `session_isolation` for runners
4. Set appropriate timeouts and memory limits
5. Use HTTPS for API in production
6. Implement authentication/authorization
7. Enable rate limiting

---

## 🎯 Use Cases

### 1. Multi-Model AI Systems
Execute tasks across Python, Llama, and cloud LLMs with unified governance.

### 2. Secure AI Pipelines
Detect and block jailbreak attempts, anomalies, and malicious activity.

### 3. Performance Optimization
Use RD computation to find optimal quality/cost trade-offs.

### 4. Multi-Tenant Systems
Isolate sessions for different users with separate storage and governance.

### 5. Audit and Compliance
Track full provenance with governance checkpoints for regulatory compliance.

---

## 🚀 Deployment

### Production Checklist
- [ ] Use `GovernancePolicy::strict()`
- [ ] Enable PostgreSQL storage
- [ ] Configure proper timeouts
- [ ] Set up monitoring and logging
- [ ] Enable HTTPS
- [ ] Implement authentication
- [ ] Set up rate limiting
- [ ] Configure backup strategy
- [ ] Test disaster recovery

---

## 📈 Roadmap

### v2.5.0 (Planned)
- [ ] Prometheus metrics
- [ ] OpenTelemetry tracing
- [ ] Redis caching layer
- [ ] WebSocket support
- [ ] gRPC API

### v3.0.0 (Future)
- [ ] Distributed orchestration
- [ ] Model serving integration
- [ ] Advanced ML pipelines
- [ ] Real-time streaming
- [ ] Multi-region support

---

## 🤝 Contributing

See [CONTRIBUTING.md](../../CONTRIBUTING.md) for guidelines.

---

## 📄 License

CC BY-NC-SA 4.0 License - See [LICENSE](../../LICENSE) for details.

---

## 🆘 Support

1. Check [DEVELOPER_GUIDE.md](DEVELOPER_GUIDE.md)
2. Review [examples/](examples/)
3. Run validation: `python validate_egg_structure.py`
4. Check API health: `curl http://localhost:8080/health`

---

**Version**: 2.4.2  
**Status**: ✅ Production Ready  
**Last Updated**: November 25, 2025
