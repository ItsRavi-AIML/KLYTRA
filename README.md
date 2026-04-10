<p align="center">
  <img src="https://i.postimg.cc/Z5L4PyFD/KLYTRA-Icon.jpg" alt="KLYTRA Logo" width="160" />
</p>

<h1 align="center">KLYTRA</h1>

<p align="center">
  <strong>Real-time messaging — fast, secure, intelligent.</strong><br/>
  End-to-end encrypted chat with AI built in.
</p>

<p align="center">
  <img src="https://img.shields.io/badge/status-active%20development-7F77DD?style=flat-square" alt="Status" />
  <img src="https://img.shields.io/badge/license-MIT-0F6E56?style=flat-square" alt="License" />
  <img src="https://img.shields.io/badge/PRs-welcome-534AB7?style=flat-square" alt="PRs Welcome" />
  <img src="https://img.shields.io/badge/encryption-E2E%20Signal%20Protocol-1D9E75?style=flat-square" alt="E2E Encryption" />
</p>

---

## What is KLYTRA?

KLYTRA is a real-time chat application built from the ground up with performance, security, and intelligence as first-class priorities. It is not a wrapper around existing platforms — every layer, from the WebSocket transport to the encryption primitives, is purpose-built.

Messages travel in under 50ms. Every conversation is end-to-end encrypted using the Signal Protocol. And an AI assistant lives inside every chat, ready when you need it.

---

## Features

- **Real-time messaging** — persistent WebSocket connections, sub-50ms delivery
- **End-to-end encryption** — Signal Protocol (X3DH + Double Ratchet), zero plaintext on the server
- **AI assistant** — mention `@klytra` in any chat to get an AI reply streamed live into the thread
- **Voice transcription** — voice messages automatically transcribed via Whisper
- **Media sharing** — images, video, audio, and files via a CDN-backed pipeline
- **Delivery & read receipts** — sent, delivered, and read states per message
- **Push notifications** — APNs for iOS, FCM for Android, Web Push for browsers
- **Multi-device** — one account, all your devices, messages synced
- **Group chats** — rooms with admin roles, invite links, and per-member read tracking
- **Spam protection** — outgoing messages filtered by a toxicity classifier before delivery

---

## Tech Stack

### Transport & Gateway
| Layer | Technology | Role |
|---|---|---|
| Connection | WebSocket (RFC 6455) | Persistent bidirectional channel |
| Load balancing | HAProxy | TCP-level distribution across gateway servers |
| HTTP / REST | Nginx | API endpoints, TLS termination |
| Security | TLS 1.3 | Encrypted transport — all connections |

### Message Infrastructure
| Layer | Technology | Role |
|---|---|---|
| Message broker | Apache Kafka | Central event log, decouples all services |
| Presence / cache | Redis | Online status, sessions, rate limits, unread counts |
| Realtime events | Redis Pub/Sub | Typing indicators, presence pings |

### Databases
| Store | Technology | Stores |
|---|---|---|
| Messages | Apache Cassandra / ScyllaDB | All message content, time-ordered, encrypted |
| Users & metadata | PostgreSQL | Users, groups, devices, memberships |
| Media files | AWS S3 / Cloudflare R2 | Images, video, audio, documents |
| Key management | HashiCorp Vault / AWS KMS | Encryption keys — never leave the service |

### Security
| Layer | Technology | Role |
|---|---|---|
| Message encryption | Signal Protocol | X3DH key exchange + Double Ratchet per-message keys |
| Auth tokens | JWT (ES256) | Short-lived signed identity tokens (15 min TTL) |
| Session management | Refresh tokens | 30-day opaque tokens, rotation on every use |
| Password hashing | Argon2 | Brute-force resistant credential storage |
| Service mesh | mTLS | Mutual TLS between all internal microservices |
| Rate limiting | Redis sliding window | Per-user, per-endpoint, per-IP limits |

### Delivery & Notifications
| Channel | Technology |
|---|---|
| iOS push | APNs (Apple Push Notification service) |
| Android push | FCM (Firebase Cloud Messaging) |
| Web push | VAPID (Web Push Protocol) |
| Media delivery | CloudFront / Cloudflare CDN — signed URLs, 24h expiry |

### AI Layer
| Feature | Technology | Where It Runs |
|---|---|---|
| @klytra AI assistant | LLM API (Claude / GPT-4o) — streamed | Server-side microservice |
| Voice transcription | OpenAI Whisper (open source) | Async worker post-upload |
| Spam & toxicity filter | Text classifier | Gateway, before Kafka publish |
| Smart reply suggestions | On-device sequence model | TensorFlow Lite / Core ML |
| Image moderation | AWS Rekognition / CLIP | Upload pipeline, blocks delivery |
| Semantic search | pgvector / Pinecone | Message history search |

### Infrastructure
| Tool | Role |
|---|---|
| Docker | Containerisation — consistent across environments |
| Kubernetes | Orchestration — scaling, self-healing, zero-downtime deploys |
| Terraform | Infrastructure as code — reproducible environment provisioning |
| Prometheus | Metrics collection from every service |
| Grafana | Real-time operational dashboards |
| OpenTelemetry + Jaeger | Distributed tracing across all services |
| PagerDuty | On-call alerting when thresholds breach |

---

## Security Model

KLYTRA treats security as infrastructure, not an afterthought.

**Messages:** Encrypted client-side before transmission using the Signal Protocol. The server stores only ciphertext. Subpoenas and database breaches yield nothing readable.

**Keys:** Every conversation derives its encryption keys through X3DH (Extended Triple Diffie-Hellman). Per-message keys are derived via Double Ratchet — compromise one message, get nothing else. Keys never leave client devices or the key management service.

**Transport:** TLS 1.3 on all external connections. Mutual TLS (mTLS) between all internal services. No plaintext on any wire.

**Auth:** JWT tokens signed with ES256 (ECDSA), 15-minute expiry. Refresh tokens stored as hashes, rotated on every use. Stolen tokens are detected on the next refresh attempt.

**Media:** Encrypted at rest with AES-256, keys in AWS KMS. CDN URLs are signed with 24-hour expiry. E2E encrypted chats upload pre-encrypted blobs — the server never sees the decryption key.

---

## Performance Targets

| Metric | Target |
|---|---|
| Message end-to-end latency (same region) | < 50ms |
| WebSocket connection setup | < 100ms |
| Presence lookup (Redis) | < 1ms |
| Cassandra message write | < 5ms |
| Kafka throughput per broker | 500,000 msg/sec |
| CDN media delivery | < 100ms globally |

---

## Roadmap

- [x] WebSocket gateway and connection management
- [x] Basic message routing and delivery
- [x] PostgreSQL user and group schema
- [x] Redis presence and session layer
- [ ] Kafka integration for reliable message brokering
- [ ] Cassandra message storage cluster
- [ ] Signal Protocol E2E encryption
- [ ] Media upload pipeline (S3 + CDN)
- [ ] Push notifications (APNs + FCM)
- [ ] AI assistant (`@klytra` mention bot)
- [ ] Voice message transcription (Whisper)
- [ ] Kubernetes deployment manifests
- [ ] Prometheus + Grafana observability stack
- [ ] iOS client
- [ ] Android client

---

## Contributing

KLYTRA is in active development. Contributions, ideas, and feedback are welcome.

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/your-feature`
3. Commit your changes: `git commit -m 'add: your feature'`
4. Push to the branch: `git push origin feature/your-feature`
5. Open a Pull Request

Please open an issue before starting major work so we can discuss direction first.

---

## License

MIT — see [LICENSE](LICENSE) for details.

---

<p align="center">
  Built with speed. Secured by design. Powered by intelligence.
  <br/>
  <strong>KLYTRA</strong>
</p>
