---
name: secure-reliable-systems
description: Use when designing, implementing, or reviewing systems for security and reliability — covers threat modeling, least privilege, resilience patterns, secure deployment, incident response, and security culture. Based on Google's "Building Secure and Reliable Systems".
---

# Building Secure and Reliable Systems

## Core Insight

Security and reliability are **emergent properties** — impossible to retrofit. Both must be designed in from the start. They share the same root causes: complexity, hidden assumptions, and cascading failures.

**Fundamental tension:** Reliability favors fail-open; security requires fail-closed. Resolve this tension explicitly at design time.

---

## Understanding Adversaries

Before designing defenses, model who you're defending against.

### Attacker Profiles
| Profile | Motivation | Capability |
|---|---|---|
| Hobbyist | Challenge, curiosity | Low — opportunistic |
| Vulnerability researcher | Recognition, bounties | Medium — targeted |
| Activist | Ideology, exposure | Medium — sustained |
| Criminal actor | Financial gain | High — persistent |
| Nation-state / law enforcement | Intelligence, control | Very high — patient |
| Insider | Grievance, profit, coercion | Very high — trusted access |
| AI/automation | Amplifies any of the above | Scales attack volume |

**Insiders are the hardest threat**: they have legitimate access, know systems deeply, and bypass perimeter controls. Design as if insiders will turn adversarial.

### Attacker Methods
- **Cyber kill chains**: Reconnaissance → intrusion → lateral movement → exfiltration. Disrupt any link to stop the chain.
- **TTPs (Tactics, Techniques, Procedures)**: Attackers reuse playbooks. Threat intelligence maps known TTPs to defenses.
- **Risk = Probability × Impact**: Prioritize mitigations by this product, not gut feeling.

---

## Design Principles

### Least Privilege
- Grant minimum access needed; reject ambient/implicit authority
- **Zero Trust**: network location grants nothing — require user + device credentials
- **Zero Touch**: automate production access; humans interact via controlled APIs
- Use narrow, typed APIs (CRUD on IDs) instead of broad POSIX-style interfaces
- Classify access by risk (public / sensitive / highly sensitive)
- Audit with structured justification (ticket IDs, case numbers)
- **Multi-Party Authorization (MPA)** for sensitive actions
- Breakglass for emergencies — restrict, monitor, always investigate after use

### Understandability
- Decompose into independently-reasoned components with clear boundaries
- Centralize auth, logging, rate-limiting in frameworks — not scattered across services
- Use **typed interfaces** (SafeHtml, TrustedSqlString) to make invalid states unrepresentable
- Minimize Trusted Computing Base (TCB) — smaller = easier to reason about
- Design **invariants**: properties that hold even under malicious conditions

### Resilience (Defense in Depth)
- Layer independent defenses — attackers must defeat each independently
- **Controlled degradation**: shed less-critical features to preserve essential ones
- **Blast radius control**: compartmentalize by role, location, and time
- **Failure domains**: partition into independent copies so one event can't take all
- Maintain three tiers: primary → cached/HA fallback → minimal-dependency fallback
- Redundancy expands attack surface — design both together

### Recovery
- Decouple deployment speed from policy: same system for normal rollout and emergency rollback
- Know your **intended state** — continuously compare deployed vs. desired, auto-repair deviations
- Avoid wall-clock time dependencies; use version/epoch or validity lists instead
- Rollbacks restore reliability but can reintroduce vulnerabilities — use deny lists + Security Version Numbers
- **Test recovery paths regularly** — untested emergency procedures fail when needed

### Design for Change
- Architecture must support fast, safe changes — security posture decays without it
- Rate-limit as isolated microservice, not embedded logic
- Progressive rollout (canary → tested → full) applies to security changes too

### DoS Mitigation
Treat DoS as a design constraint, not an ops problem.

**Attacker strategy**: Exhaust the cheapest resource (bandwidth, memory, threads, DB connections) relative to defender cost.

**Defender strategy**: Make attacks expensive for the attacker and cheap to absorb.

- **Defendable architecture**: Push filtering upstream (CDN, load balancer, API gateway); never let cheap requests reach expensive backends
- **Graceful degradation**: Shed non-critical features first; maintain core functionality under load
- **Rate limiting as isolated service**: Failure of rate-limiting infra shouldn't take down the protected service
- **Self-inflicted attacks**: Client retry storms and thundering herds are the most common real-world DoS. Require exponential backoff + jitter in all clients.
- **Strategic response**: Have pre-negotiated upstream filtering; know your ISP/CDN escalation path before an attack

---

## Implementation

### Writing Secure Code
| Vulnerability | Mitigation |
|---|---|
| SQL Injection | Typed APIs (`TrustedSqlString`) |
| XSS | Type system + contextual escaping (`SafeHtml`) |
| Memory corruption | Use memory-safe languages (Go, Java) |
| Insecure deserialization | Protocol Buffers for untrusted input |

- Prefer **frameworks** over per-component implementations — fix once, protect all
- Strong static types for domain concepts (User, Width, Radius) not raw primitives
- YAGNI — don't add speculative features that expand attack surface
- Enable sanitizers (AddressSanitizer, ThreadSanitizer) in CI/CD

### Deploying Securely
Threat model: benign insiders (mistakes) + malicious insiders + external attackers on insider accounts.

- **Mandatory code review** = multi-party authorization for code
- All build/test/deploy steps automated and locked down
- Verify **what** is deployed (artifact provenance), not just who triggered it
- Config-as-code: same review/test rigor as source code
- Never check secrets into version control; use dedicated secret management
- **Binary provenance**: document inputs, transformations, builder identity
- Route all deploys through choke points; breakglass with full audit trail

### Logging and Investigation

**Logs are your only source of truth during incidents.** Design them as infrastructure, not afterthought.

- **Immutable logs**: Write to append-only storage; prevent modification even by privileged accounts
- **Structured logging**: Machine-parseable formats enable fast querying during crises; include request IDs for distributed tracing
- **Privacy-aware**: Log *what happened* not *what was in the data* — avoid logging PII, credentials, or sensitive content
- **Budget explicitly**: Logging has real cost; define retention tiers (hot/warm/cold) and stick to them
- **Security logs to always retain**: Auth events, privilege escalations, config changes, access to sensitive data

**Debugging access security**:
- Debugging paths are high-value attack targets — apply same access controls as production
- Require audit logging on all debug access
- Prefer read-only debugging interfaces; avoid live-attach debuggers in production
- Emergency debug access should follow breakglass patterns (restrict, monitor, investigate after use)

### Testing
- Unit + integration + dynamic (fuzzing, sanitizers) + static analysis
- Test *of* least privilege: verify profiles have no excess permissions
- Test *with* least privilege: use separate credentials to prevent production impact
- **Adversarial testing**: simulate attacks from defined adversary perspective (reliability assumes independence; security cannot)

---

## Incident Response

### Crisis Management
1. Declare early — false alarms cost less than delayed response
2. Assign clear Incident Commander; avoid committee decisions under pressure
3. Information sharing: reliability → broad; security → need-to-know (don't tip off adversaries)
4. Keep a live incident doc; communicate status at regular intervals
5. After recovery: blameless postmortem, fix root causes, update runbooks

### Recovery Aftermath
After containment, recovery is its own discipline:

- **Scope before acting**: Enumerate all affected systems before remediating any — incomplete recovery is worse than slow recovery
- **Quarantine first**: Isolate compromised assets; preserve forensic state before wiping
- **Credential rotation**: Rotate all secrets that could have been exposed — assume broader exposure than confirmed
- **System rebuilds over patching**: For serious compromises, rebuild from known-good images rather than patching in place
- **Recovery data integrity**: Verify backups weren't themselves compromised before restoring from them
- **Postmortems**: Blameless, written, shared. Focus on systemic fixes not individual fault. Track action items to completion.

### Disaster Planning
- Define disaster tiers with pre-agreed response strategies
- Pre-stage systems and people before incidents occur
- **Tabletop exercises** and game days — untested plans fail
- Emergency access: low-dependency, tested regularly, integrated into on-call

---

## Culture

| Culture | Practice |
|---|---|
| Review | Code, config, and access changes all require peer review |
| Awareness | Just-in-time education > passive documentation |
| Yes (managed risk) | Measure risk; layered defenses reduce individual reviewer burden |
| Inevitability | Blameless postmortems; study failures to build resilience |
| Sustainability | Balance reactive work with proactive investment; prevent burnout |

**Roles and responsibilities**:
- Security is everyone's job, but specialists amplify it — embed security engineers in teams rather than isolating in a separate org
- **Red teams** simulate realistic attacks; **blue teams** detect and respond. Both are needed; red-only gives a false sense of offense advantage.
- External researchers (bug bounty, academic) find what internal teams miss — build a responsible disclosure program
- Certifications signal baseline knowledge but don't substitute for engineering judgment

- **Leadership buy-in**: align security investment with business metrics
- Reduce fear through canary deploys, dogfooding, progressive rollout
- Job shadowing breaks silos — empathy across teams improves shared ownership

---

## Quick Checklist

**Design phase:**
- [ ] Least privilege on all access paths
- [ ] Failure domains and blast radius explicitly defined
- [ ] Invariants documented and testable
- [ ] Recovery path designed and tested

**Implementation:**
- [ ] Memory-safe language or memory-safe wrappers
- [ ] Typed interfaces for security-sensitive data
- [ ] Centralized auth/logging/rate-limiting framework
- [ ] No secrets in version control

**Deployment:**
- [ ] Mandatory code review enforced
- [ ] Artifact provenance verified
- [ ] Incremental rollout with rollback capability
- [ ] Config changes treated as code

**Operations:**
- [ ] Runbooks tested under realistic conditions
- [ ] Emergency access provisioned and exercised
- [ ] Blameless postmortem process established
- [ ] Immutable, privacy-aware logging in place
- [ ] DoS mitigation: rate limiting upstream, client retry backoff enforced
- [ ] Threat model includes insider and automated adversaries
- [ ] Credential rotation runbook ready before incidents
- [ ] Red team / external disclosure program established
