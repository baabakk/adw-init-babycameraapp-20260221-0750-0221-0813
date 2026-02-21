# Architecture Guardrails: init-babycameraapp-20260221-0750

> Generated at 2026-02-21T08:08:07.229Z
> Source: Claude-enhanced
> Derived from: tpm-contract.json

## Interfaces

### WebRTC Peer Connection (api)

Peer-to-peer media streaming between sender and viewer browser instances

**Contract:** RTCPeerConnection API with manual SDP offer/answer exchange. Sender creates offer with video/audio tracks from getUserMedia. Viewer receives offer, creates answer. ICE candidates exchanged manually via copy-paste. Connection established over local network only (no STUN/TURN). MediaStream transmitted with <3s latency.

### Media Capture Interface (api)

Camera and microphone access in sender mode

**Contract:** navigator.mediaDevices.getUserMedia({video: true, audio: true}). Returns MediaStream with video and audio tracks. Requires user permission grant. Graceful error handling for permission denial or device unavailability.

### Browser Notification Interface (api)

Cry detection alerts when viewer tab is backgrounded

**Contract:** Notification API with permission request on viewer mode activation. Notifications triggered by cry detection events with title, body, and icon. Must work when tab is not in focus but browser is open. Graceful degradation if permission denied.

### Audio Analysis Interface (api)

Real-time cry detection from audio stream

**Contract:** Web Audio API with AudioContext, AnalyserNode for frequency/amplitude analysis. Runs on both sender and viewer sides. Configurable sensitivity threshold (optional FR-11). Emits cry detection events when threshold exceeded. High sensitivity prioritized over precision per NFR-05.

### Connection Signaling Interface (shared-lib)

Manual SDP offer/answer exchange mechanism

**Contract:** In-memory state management for connection codes. Sender generates SDP offer as base64-encoded string. Viewer accepts SDP answer as base64-encoded string. Copy-paste UI for code exchange. No external signaling server. Validation for malformed codes with error messaging.

### Mode State Interface (shared-lib)

Application mode switching between sender and viewer

**Contract:** Single-page state machine with modes: 'idle', 'sender', 'viewer'. Mode transitions trigger cleanup of previous mode resources (media streams, peer connections). UI updates reflect current mode. No page reload required per FR-07.

## Data Contracts

### MediaStream

- **Storage:** In-memory (browser runtime)
- **Ownership:** Browser MediaDevices API
- **Notes:** Video/audio tracks captured from device. Lifetime bound to peer connection. Released on mode switch or connection close. Not persisted per non-goal.

### RTCPeerConnection State

- **Storage:** In-memory (browser runtime)
- **Ownership:** WebRTC API
- **Notes:** Connection state, ICE candidates, SDP offer/answer. Ephemeral per session. No persistence. Recreated on reconnection.

### SDP Connection Codes

- **Storage:** Transient (clipboard/UI display)
- **Ownership:** Application UI layer
- **Notes:** Base64-encoded SDP offer/answer strings. Displayed for manual copy-paste. Not stored. Expires when connection established or user navigates away.

### Cry Detection Events

- **Storage:** In-memory (event queue)
- **Ownership:** Audio analysis module
- **Notes:** Timestamp, amplitude, frequency data when cry detected. Triggers notifications. Not persisted. No historical tracking per non-goal.

### Application Configuration

- **Storage:** In-memory (optional localStorage for sensitivity)
- **Ownership:** Application state manager
- **Notes:** Current mode, connection status, cry detection sensitivity threshold. Reset on page reload. Optional localStorage for sensitivity persistence (FR-11).

### Audio Analysis Buffer

- **Storage:** In-memory (Web Audio API)
- **Ownership:** AudioContext/AnalyserNode
- **Notes:** Real-time frequency domain data for cry detection. Circular buffer, continuously overwritten. No retention beyond analysis window.

## Scalability

**Expected Load:** Single peer-to-peer connection per browser instance. One sender, one viewer. No concurrent connections. Local network bandwidth (typically 100Mbps+ WiFi). Audio analysis runs at ~20-50Hz sampling rate. Video stream at standard webcam resolution (720p or lower).

**Bottlenecks:**
- Client-side audio analysis CPU usage may impact low-end devices
- WebRTC encoding/decoding performance on older laptops
- Local network bandwidth if WiFi signal weak or congested
- Browser tab backgrounding may throttle JavaScript execution, delaying cry detection
- Single HTML file size constraint limits embedded asset optimization
- Manual SDP exchange creates user friction and error potential

**Mitigations:**
- Use efficient FFT implementation in Web Audio API (native browser optimization)
- Limit video resolution to 720p or lower to reduce encoding overhead
- Implement connection quality indicators to alert users of network issues
- Use Web Workers for audio analysis to avoid blocking main thread (if time permits within 1-day scope)
- Document minimum device requirements (modern laptop, Chrome 80+)
- Provide clear error messages when performance insufficient
- Accept that background tab throttling may delay notifications by 1-2 seconds as acceptable tradeoff

## Security Baseline

- **Auth Method:** None - peer-to-peer trust model. Users manually exchange connection codes out-of-band (messaging, verbal). No authentication or authorization. Assumes trusted peer relationship (parent devices).
- **Data Classification:** Sensitive - live video/audio of infant. Ephemeral transmission only, no storage. Data remains on local network, not transmitted to external servers per NFR-01.

**Threat Vectors:**
- Malicious peer connection if SDP code intercepted or shared with untrusted party
- Man-in-the-middle attack on local network (WiFi eavesdropping)
- Browser extension or malware accessing MediaStream APIs
- Accidental sharing of HTML file with embedded malicious code (supply chain)
- Clipboard hijacking during SDP code exchange
- Browser vulnerability in WebRTC implementation
- Notification content exposure on lock screen
- Unintended camera/microphone access if permissions not revoked

**Mitigations:**
- Document that connection codes should only be shared with trusted devices via secure channels
- Rely on WebRTC's built-in DTLS-SRTP encryption for media streams (enabled by default)
- Recommend users connect devices to password-protected WiFi networks
- Provide clear UI indicators when camera/microphone are active
- Include instructions to revoke browser permissions when not in use
- Recommend users verify HTML file integrity if obtained from third-party source
- Limit notification content to generic 'Crying detected' message without sensitive details
- Document that application has no server-side component, reducing attack surface
- Accept residual risk: local network security and device security are user responsibility
- Out of scope for 1-day prototype: code signing, integrity verification, advanced encryption

## Architecture Decision Records

### ADR-001: Use Manual SDP Exchange Instead of Signaling Server

**Status:** accepted

**Context:** FR-02, FR-10 require peer connection establishment. NFR-01 mandates no external dependencies. NFR-11 limits scope to local network. Traditional WebRTC requires signaling server for SDP exchange. 1-day timeline precludes building signaling infrastructure.

**Decision:** Implement manual SDP offer/answer exchange via copy-paste UI. Sender generates offer as base64 string, user copies to viewer via external channel (messaging app, verbal). Viewer generates answer, user copies back to sender. No signaling server required.

**Consequences:** Positive: Zero infrastructure, works offline, simple implementation. Negative: Poor UX, error-prone, not scalable beyond prototype. Acceptable for 1-day MVP demonstrating feasibility. Must document workaround clearly per FR-14.

### ADR-002: Embed All Dependencies in Single HTML File

**Status:** accepted

**Context:** FR-01 requires single HTML file delivery. NFR-01 mandates offline functionality. NFR-03 constrains file size to <5MB. Need to include CSS, JavaScript, and any UI assets.

**Decision:** Inline all CSS in <style> tags, all JavaScript in <script> tags. Use data URIs for small images/icons. Minify code to reduce size. No external CDN links. No separate asset files.

**Consequences:** Positive: True offline capability, easy distribution, no CORS issues. Negative: Harder to debug, slower iteration, limited asset richness. File size constraint limits UI polish. Acceptable tradeoff for distribution simplicity and offline requirement.

### ADR-003: Run Cry Detection on Both Sender and Viewer

**Status:** accepted

**Context:** FR-05 requires cry detection on both sides. FR-06 requires notifications on viewer. Edge case notes potential duplicate notifications. NFR-05 prioritizes sensitivity over precision.

**Decision:** Implement audio analysis on both sender and viewer independently. Sender provides local feedback. Viewer triggers notifications. Accept potential duplicate detection as acceptable given high-sensitivity requirement and 1-day timeline.

**Consequences:** Positive: Redundancy increases detection reliability, local feedback on sender useful. Negative: Duplicate notifications possible, increased CPU usage. Mitigation: document behavior, consider notification debouncing if time permits. AC-12 validates no errors occur.

### ADR-004: Limit WebRTC to Local Network Without STUN/TURN

**Status:** accepted

**Context:** NFR-11 scopes prototype to local network. External dependency on NAT/firewall marked high risk. 1-day timeline precludes STUN/TURN server setup or integration. FR-13, FR-14 require clear error messaging for connection failures.

**Decision:** Configure RTCPeerConnection without STUN/TURN servers. Connection succeeds only when both peers on same local network. Display clear error message when connection fails, explaining NAT/firewall limitation and suggesting local network workaround.

**Consequences:** Positive: Simple implementation, no infrastructure, fast development. Negative: Severely limits usability across networks. Acceptable for prototype demonstrating core feasibility. Must document limitation prominently per FR-13, FR-14. Future versions require STUN/TURN for production use.

### ADR-005: Use Frequency/Amplitude Analysis for Cry Detection

**Status:** accepted

**Context:** FR-05 requires cry detection. NFR-05 prioritizes sensitivity over precision. NFR-12 requires testing with public audio samples. Non-goal excludes ML optimization. 1-day timeline limits algorithm complexity.

**Decision:** Implement simple cry detection using Web Audio API AnalyserNode. Analyze frequency spectrum (300-600Hz range typical for infant crying) and amplitude thresholds. Tune thresholds using publicly available baby crying audio samples. Accept higher false positive rate per NFR-05.

**Consequences:** Positive: Fast implementation, no ML training, testable with public samples, runs in browser. Negative: Lower accuracy than ML models, sensitive to background noise. Acceptable for prototype. FR-11 allows sensitivity adjustment. AC-08 validates detection with public samples.

### ADR-006: Use Single-Page State Machine for Mode Switching

**Status:** accepted

**Context:** FR-07 requires mode switching without page reload. Need to manage sender/viewer modes, connection state, media streams. Must clean up resources on mode change.

**Decision:** Implement state machine with modes: idle, sender, viewer. Mode transitions trigger cleanup (stop media streams, close peer connections) and initialization of new mode. UI updates reactively based on current state. No page navigation.

**Consequences:** Positive: Smooth UX, no reload latency, simple state management. Negative: Must carefully manage resource cleanup to avoid memory leaks. Acceptable complexity for single-page application. AC-05 validates mode switching functionality.

### ADR-007: Target Chrome 80+ as Primary Browser for Prototype

**Status:** accepted

**Context:** NFR-02 requires modern browser support. 1-day timeline limits cross-browser testing. WebRTC, Notification API, Web Audio API have varying support. Need to prioritize for rapid development.

**Decision:** Develop and test primarily on Chrome 80+. Document compatibility with Firefox 75+, Safari 14+, Edge 80+ but accept potential issues. Provide browser detection and graceful degradation per NFR-10. Focus on Chrome for AC validation.

**Consequences:** Positive: Faster development, consistent API behavior, broad user base. Negative: Potential issues on other browsers undiscovered. Acceptable for prototype. Document browser requirements clearly. Future versions require comprehensive cross-browser testing.

### ADR-008: Request Notification Permission on Viewer Mode Activation

**Status:** accepted

**Context:** FR-06 requires notifications when tab backgrounded. NFR-07 requires notifications work when tab not in focus. Notification API requires explicit user permission. Must handle permission denial gracefully.

**Decision:** Request Notification.requestPermission() when user switches to viewer mode. Display clear explanation of why permission needed. If denied, show warning but allow viewer mode to continue (user must keep tab in focus). Notifications only sent if permission granted.

**Consequences:** Positive: Clear permission context, user understands purpose. Negative: Additional permission prompt may reduce adoption. Acceptable tradeoff for notification functionality. AC-04 validates notification delivery when backgrounded.

## Confidence & Gaps

**Confidence:**
- interfaces: *confirmed*
- dataContracts: *confirmed*
- scalability: *inferred*
- securityBaseline: *inferred*
- adrs: *confirmed*

**Gaps:**
- Specific cry detection algorithm parameters (frequency ranges, amplitude thresholds, analysis window size) not defined - requires experimentation with public audio samples
- Web Worker implementation for audio analysis not specified - may be deferred due to 1-day timeline
- Notification debouncing strategy for duplicate cry detection events not defined
- Connection quality indicators (FR-09) implementation approach not specified
- Error message content and UX for NAT/firewall failures (FR-13) not detailed
- localStorage schema for sensitivity persistence (FR-11) not defined
- Browser compatibility fallback strategies beyond error messaging not specified
- Performance benchmarks for minimum device requirements not established
- Accessibility considerations (screen readers, keyboard navigation) not addressed
- Privacy policy or user consent flow for camera/microphone access not defined
- Distribution mechanism and file integrity verification not specified
- Post-prototype graduation criteria and production roadmap unclear
