# StegaCrypt Expansion Master Guide

This document is a project-specific roadmap for expanding your existing StegaCrypt app. It covers:

- your current 50 enhancement ideas
- 100 additional enhancement ideas
- backend and frontend add-on strategy
- step-by-step integration guidance for the current codebase
- starter code templates for major feature groups
- a practical order to build everything without breaking the current app

This guide is written for the current repository structure:

- backend: Spring Boot 3.2 + Java 17 + Maven
- frontend: React 18 + Vite + Axios
- current storage: in-memory maps
- current auth: token-in-memory demo auth
- current secure share engine: RSA + AES-GCM + LSB steganography

## 1. Current project baseline

Before adding new features, your current app already has:

- key generation API
- embed/extract API
- image capacity check
- demo auth/register/login
- in-memory users and secure shares
- seeded demo members
- frontend API service in `frontend/src/services/api.js`
- backend controllers under `backend/src/main/java/com/stegacrypt/controller`
- backend services under `backend/src/main/java/com/stegacrypt/service`
- crypto utilities under `backend/src/main/java/com/stegacrypt/util`

That means the correct approach is not to rewrite everything. The right approach is to evolve the app in layers.

## 2. Recommended implementation order

Do not build all 150 enhancements at once. Build them in phases:

1. Foundation and security
2. Persistence and real auth
3. Messaging improvements
4. Payload/media support
5. Advanced crypto and stego
6. Admin, analytics, and operations
7. UX, collaboration, and platform scale

This order matters because many features depend on earlier layers like database support, JWT auth, and file storage.

## 3. Your original 50 enhancements

These are your existing ideas:

1. Real user database storage instead of in-memory storage.
2. JWT-based authentication.
3. Secure password hashing with `BCrypt`.
4. Forgot password feature.
5. Reset password via email OTP.
6. Email verification after registration.
7. Role-based access control for admin and users.
8. Real-time chat using WebSockets.
9. Group secure messaging.
10. Broadcast stego message to multiple recipients.
11. Unread/read message tracking.
12. Message delivery status.
13. Decrypt-once messages.
14. Self-destructing messages after timeout.
15. Scheduled message sending.
16. Chat search by sender, recipient, or date.
17. Message history pagination.
18. Export chat history.
19. Upload and hide text files inside images.
20. Upload and hide PDFs inside images.
21. Upload and hide audio files inside images.
22. Multi-file secret payload support.
23. Batch embedding into multiple images.
24. Batch extraction from multiple images.
25. Multi-image payload splitting for large messages.
26. Support for group decryption keys.
27. Public key fingerprint verification before sending.
28. Sender digital signatures for authenticity.
29. Private key encryption with passphrase.
30. Key import/export in multiple formats.
31. QR-based public key sharing.
32. Image capacity estimator before upload completion.
33. Auto-select best carrier image based on payload size.
34. Stego image integrity verification before extraction.
35. Error correction support for damaged images.
36. JPEG steganography support.
37. BMP steganography support.
38. Audio steganography support.
39. Video steganography support.
40. Adaptive LSB embedding based on image regions.
41. Multiple embedding modes selectable by user.
42. Compression mode selection before encryption.
43. Image metadata stripping before embedding.
44. Admin dashboard for user/share management.
45. Audit logs for login, send, decrypt, and download events.
46. Rate limiting for login and upload endpoints.
47. Notification system for new secure messages.
48. User profile management.
49. Contact/friend list for frequent recipients.
50. Conversation/thread view for secure chats.

## 4. 100 additional enhancements

Here are 100 more ideas, continuing from 51:

51. Device/session management page.
52. Logout from all devices.
53. Account lockout after repeated failed logins.
54. Two-factor authentication with TOTP.
55. Backup recovery codes for 2FA.
56. Session expiry and refresh token rotation.
57. Password strength meter on frontend.
58. Password reuse prevention.
59. Security questions fallback recovery.
60. Login alerts by email.
61. Browser fingerprint monitoring for suspicious access.
62. IP-based risk scoring for login attempts.
63. Geo-location login history.
64. Admin user suspension and reactivation.
65. Temporary account mute/ban in chat.
66. User presence status: online, away, offline.
67. Typing indicators in secure chat.
68. Message reactions.
69. Reply-to-message support.
70. Forward secure share to another user.
71. Pinned conversations.
72. Starred messages.
73. Draft message saving.
74. Conversation mute settings.
75. Archive conversation feature.
76. Block user feature.
77. Report abuse flow.
78. Rich message labels and tags.
79. Chat folders and categories.
80. Attachment thumbnail preview.
81. Upload progress bar for embedding jobs.
82. Extraction progress indicator.
83. Retry failed upload/download jobs.
84. Download manager for generated stego images.
85. Drag-and-drop multi-image upload UI.
86. Dark mode and theme personalization.
87. Accessibility improvements with keyboard-only flow.
88. Multi-language UI support.
89. Responsive mobile-first secure chat layout.
90. PWA offline shell for the frontend.
91. Push notifications for new shares.
92. Desktop notification permissions support.
93. Recent activity timeline page.
94. User dashboard with personal usage stats.
95. Storage quota per user.
96. Share expiration policy configuration.
97. Custom message retention policy.
98. Legal/consent banner for secure data usage.
99. Terms acceptance tracking.
100. Privacy settings page.
101. File virus scanning before processing.
102. MIME type sniffing instead of trusting extension only.
103. Signed URL delivery for downloaded stego files.
104. Object storage integration for images.
105. CDN delivery for static artifacts.
106. Background job queue for heavy embed/extract tasks.
107. Scheduled worker for cleanup tasks.
108. Dead-letter queue for failed processing jobs.
109. Database migration support with Flyway.
110. Multi-environment config profiles.
111. Secrets management through env variables or vault.
112. Structured logging in JSON format.
113. Metrics endpoint for Prometheus.
114. Dashboard integration with Grafana.
115. Distributed tracing support.
116. Centralized exception/error event pipeline.
117. Health checks for DB, email, and storage.
118. API versioning support.
119. OpenAPI or Swagger documentation.
120. Postman collection export.
121. Contract tests for API endpoints.
122. Frontend component tests.
123. End-to-end Playwright tests.
124. Load testing for embed and extract.
125. Performance benchmark suite for stego algorithms.
126. Pluggable crypto provider abstraction.
127. Elliptic-curve key support.
128. Hybrid ECDH + AES session sharing mode.
129. Hardware-backed key storage support.
130. Key revocation workflow.
131. Public key trust levels.
132. Message authenticity verification badge.
133. Tamper-evident download receipts.
134. Secure clipboard controls on sensitive pages.
135. Watermarked preview mode for shared content.
136. OCR extraction from embedded document previews.
137. AI-assisted carrier image recommendation.
138. AI-assisted anomaly detection for suspicious usage.
139. Bulk user import for admin onboarding.
140. Team/workspace support.
141. Organization-level policies.
142. SSO support with Google or Microsoft.
143. API keys for developer integrations.
144. Webhook support for share events.
145. External audit export in CSV and JSON.
146. Immutable compliance event log.
147. Conversation backup and restore.
148. Cross-device secure sync settings.
149. Message template library.
150. Guided onboarding/tutorial mode for new users.

## 5. Grouping all 150 enhancements into implementation tracks

### Track A: Foundation and real backend security

Build these first:

- 1, 2, 3, 7, 46, 51, 52, 53, 56, 109, 110, 111, 117, 118, 119

### Track B: Account recovery and trust

- 4, 5, 6, 54, 55, 57, 58, 59, 60, 61, 62, 63, 99, 100, 130, 131

### Track C: Chat and secure share UX

- 8, 11, 12, 16, 17, 18, 47, 48, 49, 50, 66, 67, 68, 69, 71, 72, 73, 74, 75, 76, 77, 78, 79, 81, 82, 83, 84, 85, 89, 91, 92, 93, 94, 149, 150

### Track D: Payload and media

- 19, 20, 21, 22, 23, 24, 25, 80, 95, 101, 102, 103, 104, 105, 106, 107, 108, 147

### Track E: Crypto and steganography

- 26, 27, 28, 29, 30, 31, 34, 35, 36, 37, 38, 39, 40, 41, 42, 43, 125, 126, 127, 128, 129, 132, 133, 134, 135

### Track F: Collaboration and scale

- 9, 10, 13, 14, 15, 64, 65, 70, 86, 87, 88, 90, 96, 97, 98, 112, 113, 114, 115, 116, 120, 121, 122, 123, 124, 136, 137, 138, 139, 140, 141, 142, 143, 144, 145, 146, 148

## 6. Exact new backend modules to add

To grow this project cleanly, create these new backend packages:

- `backend/src/main/java/com/stegacrypt/config`
- `backend/src/main/java/com/stegacrypt/security`
- `backend/src/main/java/com/stegacrypt/entity`
- `backend/src/main/java/com/stegacrypt/repository`
- `backend/src/main/java/com/stegacrypt/dto`
- `backend/src/main/java/com/stegacrypt/mapper`
- `backend/src/main/java/com/stegacrypt/job`
- `backend/src/main/java/com/stegacrypt/storage`
- `backend/src/main/java/com/stegacrypt/websocket`
- `backend/src/main/java/com/stegacrypt/audit`
- `backend/src/main/java/com/stegacrypt/notification`

Keep your current packages too. The current crypto and stego services can stay and be reused.

## 7. Exact new frontend modules to add

To support the new features, add these frontend folders:

- `frontend/src/pages`
- `frontend/src/hooks`
- `frontend/src/context`
- `frontend/src/store`
- `frontend/src/layouts`
- `frontend/src/features/auth`
- `frontend/src/features/chat`
- `frontend/src/features/admin`
- `frontend/src/features/profile`
- `frontend/src/features/keys`
- `frontend/src/features/upload`
- `frontend/src/features/settings`
- `frontend/src/components/common`

## 8. Dependency additions for backend

Add these dependencies to `backend/pom.xml` in phases:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-mail</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-websocket</artifactId>
</dependency>

<dependency>
    <groupId>org.flywaydb</groupId>
    <artifactId>flyway-core</artifactId>
</dependency>

<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-api</artifactId>
    <version>0.11.5</version>
</dependency>

<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-impl</artifactId>
    <version>0.11.5</version>
    <scope>runtime</scope>
</dependency>

<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-jackson</artifactId>
    <version>0.11.5</version>
    <scope>runtime</scope>
</dependency>

<dependency>
    <groupId>org.postgresql</groupId>
    <artifactId>postgresql</artifactId>
    <scope>runtime</scope>
</dependency>

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>

<dependency>
    <groupId>io.micrometer</groupId>
    <artifactId>micrometer-registry-prometheus</artifactId>
</dependency>

<dependency>
    <groupId>com.bucket4j</groupId>
    <artifactId>bucket4j-core</artifactId>
    <version>8.10.1</version>
</dependency>
```

## 9. Dependency additions for frontend

Add these in `frontend/package.json` as needed:

```json
{
  "dependencies": {
    "react-router-dom": "^7.0.0",
    "zustand": "^5.0.0",
    "sockjs-client": "^1.6.1",
    "stompjs": "^2.3.3",
    "react-hook-form": "^7.54.0",
    "zod": "^3.24.0",
    "@hookform/resolvers": "^3.9.1",
    "qrcode.react": "^4.2.0",
    "react-dropzone": "^14.3.5",
    "i18next": "^24.0.0",
    "react-i18next": "^15.1.1"
  }
}
```

## 10. Foundation migration plan: in-memory to database

This is the first real upgrade because many later features depend on persistence.

### Step 1

Add JPA and PostgreSQL dependencies.

### Step 2

Add DB configuration in `backend/src/main/resources/application.properties`:

```properties
spring.datasource.url=${DB_URL:jdbc:postgresql://localhost:5432/stegacrypt}
spring.datasource.username=${DB_USERNAME:postgres}
spring.datasource.password=${DB_PASSWORD:postgres}
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=false
spring.jpa.properties.hibernate.format_sql=true
```

### Step 3

Create entities:

- `UserEntity`
- `RoleEntity`
- `ChatShareEntity`
- `PasswordResetOtpEntity`
- `AuditLogEntity`
- `ConversationEntity`
- `MessageEntity`

### Step 4

Create repositories:

- `UserRepository`
- `ChatShareRepository`
- `MessageRepository`
- `AuditLogRepository`
- `PasswordResetOtpRepository`

### Step 5

Refactor `AuthChatService` to use repositories instead of `ConcurrentHashMap`.

### Starter code: `UserEntity`

File:

- `backend/src/main/java/com/stegacrypt/entity/UserEntity.java`

```java
package com.stegacrypt.entity;

import jakarta.persistence.*;
import java.time.Instant;

@Entity
@Table(name = "users")
public class UserEntity {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false)
    private String fullName;

    @Column(nullable = false, unique = true, length = 120)
    private String username;

    @Column(nullable = false, unique = true, length = 160)
    private String email;

    @Column(nullable = false)
    private String passwordHash;

    @Column(nullable = false)
    private boolean enabled = false;

    @Column(nullable = false)
    private boolean seeded = false;

    @Lob
    @Column(nullable = false, columnDefinition = "TEXT")
    private String publicKeyPem;

    @Lob
    @Column(nullable = false, columnDefinition = "TEXT")
    private String privateKeyPemEncrypted;

    @Column(nullable = false)
    private Instant createdAt = Instant.now();

    public Long getId() { return id; }
    public String getFullName() { return fullName; }
    public void setFullName(String fullName) { this.fullName = fullName; }
    public String getUsername() { return username; }
    public void setUsername(String username) { this.username = username; }
    public String getEmail() { return email; }
    public void setEmail(String email) { this.email = email; }
    public String getPasswordHash() { return passwordHash; }
    public void setPasswordHash(String passwordHash) { this.passwordHash = passwordHash; }
    public boolean isEnabled() { return enabled; }
    public void setEnabled(boolean enabled) { this.enabled = enabled; }
    public boolean isSeeded() { return seeded; }
    public void setSeeded(boolean seeded) { this.seeded = seeded; }
    public String getPublicKeyPem() { return publicKeyPem; }
    public void setPublicKeyPem(String publicKeyPem) { this.publicKeyPem = publicKeyPem; }
    public String getPrivateKeyPemEncrypted() { return privateKeyPemEncrypted; }
    public void setPrivateKeyPemEncrypted(String privateKeyPemEncrypted) { this.privateKeyPemEncrypted = privateKeyPemEncrypted; }
    public Instant getCreatedAt() { return createdAt; }
}
```

### Starter code: `UserRepository`

File:

- `backend/src/main/java/com/stegacrypt/repository/UserRepository.java`

```java
package com.stegacrypt.repository;

import com.stegacrypt.entity.UserEntity;
import org.springframework.data.jpa.repository.JpaRepository;

import java.util.Optional;

public interface UserRepository extends JpaRepository<UserEntity, Long> {
    Optional<UserEntity> findByUsername(String username);
    Optional<UserEntity> findByEmail(String email);
    boolean existsByUsername(String username);
    boolean existsByEmail(String email);
}
```

## 11. JWT authentication plan

This replaces the current in-memory token map.

### Step-by-step

1. Add Spring Security and JWT dependencies.
2. Create `JwtService`.
3. Create `JwtAuthenticationFilter`.
4. Create `SecurityConfig`.
5. Replace `X-Auth-Token` with `Authorization: Bearer <token>`.
6. Update frontend `api.js` to attach bearer token automatically.
7. Keep a temporary compatibility path during migration if needed.

### Starter code: `JwtService`

File:

- `backend/src/main/java/com/stegacrypt/security/JwtService.java`

```java
package com.stegacrypt.security;

import io.jsonwebtoken.Claims;
import io.jsonwebtoken.Jwts;
import io.jsonwebtoken.SignatureAlgorithm;
import io.jsonwebtoken.security.Keys;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Service;

import java.nio.charset.StandardCharsets;
import java.security.Key;
import java.util.Date;

@Service
public class JwtService {

    private final Key key;
    private final long accessTokenMs;

    public JwtService(
        @Value("${app.jwt.secret}") String secret,
        @Value("${app.jwt.access-token-ms:3600000}") long accessTokenMs
    ) {
        this.key = Keys.hmacShaKeyFor(secret.getBytes(StandardCharsets.UTF_8));
        this.accessTokenMs = accessTokenMs;
    }

    public String generateToken(String username) {
        long now = System.currentTimeMillis();
        return Jwts.builder()
            .setSubject(username)
            .setIssuedAt(new Date(now))
            .setExpiration(new Date(now + accessTokenMs))
            .signWith(key, SignatureAlgorithm.HS256)
            .compact();
    }

    public String extractUsername(String token) {
        return parseClaims(token).getSubject();
    }

    public boolean isValid(String token) {
        try {
            parseClaims(token);
            return true;
        } catch (Exception ex) {
            return false;
        }
    }

    private Claims parseClaims(String token) {
        return Jwts.parserBuilder()
            .setSigningKey(key)
            .build()
            .parseClaimsJws(token)
            .getBody();
    }
}
```

### Starter code: `SecurityConfig`

File:

- `backend/src/main/java/com/stegacrypt/config/SecurityConfig.java`

```java
package com.stegacrypt.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.http.SessionCreationPolicy;
import org.springframework.security.web.SecurityFilterChain;

@Configuration
public class SecurityConfig {

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .csrf(csrf -> csrf.disable())
            .sessionManagement(sm -> sm.sessionCreationPolicy(SessionCreationPolicy.STATELESS))
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/api/auth/login", "/api/auth/register", "/api/health").permitAll()
                .anyRequest().authenticated()
            );
        return http.build();
    }
}
```

### Frontend update in `frontend/src/services/api.js`

```javascript
import axios from 'axios';

const API_BASE_URL = import.meta.env.VITE_API_BASE_URL || 'http://localhost:8080/api';

const http = axios.create({
  baseURL: API_BASE_URL,
});

http.interceptors.request.use((config) => {
  const token = localStorage.getItem('stegacrypt_token');
  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }
  return config;
});

export default http;
```

## 12. Password hashing with BCrypt

This should be implemented immediately after real DB storage.

### Step-by-step

1. Stop using SHA-256 for passwords.
2. Add a Spring `PasswordEncoder` bean.
3. Hash passwords at registration.
4. Verify using `passwordEncoder.matches(...)` at login.

### Starter code

```java
package com.stegacrypt.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.security.crypto.password.PasswordEncoder;

@Configuration
public class PasswordConfig {

    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
}
```

### Service usage

```java
String hash = passwordEncoder.encode(request.password());
boolean ok = passwordEncoder.matches(loginRequest.password(), user.getPasswordHash());
```

## 13. Forgot password and email OTP flow

Features covered:

- 4, 5, 6, 60

### New backend files

- `entity/PasswordResetOtpEntity.java`
- `repository/PasswordResetOtpRepository.java`
- `service/EmailService.java`
- `service/PasswordRecoveryService.java`
- `controller/AuthRecoveryController.java`

### Step-by-step

1. Add mail dependency and SMTP settings.
2. Create OTP table with expiry time.
3. Generate random 6-digit OTP.
4. Email OTP to registered email.
5. Verify OTP.
6. Allow password reset.
7. Delete or invalidate used OTP.
8. Add email verification token flow for registration.

### Starter code: OTP entity

```java
package com.stegacrypt.entity;

import jakarta.persistence.*;
import java.time.Instant;

@Entity
@Table(name = "password_reset_otps")
public class PasswordResetOtpEntity {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false)
    private String email;

    @Column(nullable = false, length = 6)
    private String otpCode;

    @Column(nullable = false)
    private Instant expiresAt;

    @Column(nullable = false)
    private boolean used = false;

    public Long getId() { return id; }
    public String getEmail() { return email; }
    public void setEmail(String email) { this.email = email; }
    public String getOtpCode() { return otpCode; }
    public void setOtpCode(String otpCode) { this.otpCode = otpCode; }
    public Instant getExpiresAt() { return expiresAt; }
    public void setExpiresAt(Instant expiresAt) { this.expiresAt = expiresAt; }
    public boolean isUsed() { return used; }
    public void setUsed(boolean used) { this.used = used; }
}
```

### Starter code: mail settings

```properties
spring.mail.host=${MAIL_HOST:smtp.gmail.com}
spring.mail.port=${MAIL_PORT:587}
spring.mail.username=${MAIL_USERNAME}
spring.mail.password=${MAIL_PASSWORD}
spring.mail.properties.mail.smtp.auth=true
spring.mail.properties.mail.smtp.starttls.enable=true
```

### Frontend pages to add

- `frontend/src/pages/ForgotPasswordPage.jsx`
- `frontend/src/pages/VerifyOtpPage.jsx`
- `frontend/src/pages/ResetPasswordPage.jsx`

## 14. Role-based access control

Features covered:

- 7, 44, 64

### Backend design

Use roles:

- `ROLE_USER`
- `ROLE_ADMIN`

### Step-by-step

1. Add `RoleEntity`.
2. Map users to roles.
3. Add route guards in Spring Security.
4. Create admin-only APIs for dashboards and moderation.
5. Add frontend route guards for admin pages.

### Example route protection

```java
.authorizeHttpRequests(auth -> auth
    .requestMatchers("/api/auth/login", "/api/auth/register", "/api/health").permitAll()
    .requestMatchers("/api/admin/**").hasRole("ADMIN")
    .anyRequest().authenticated()
)
```

## 15. WebSocket real-time chat

Features covered:

- 8, 66, 67, 68, 69, 91

### Backend files

- `config/WebSocketConfig.java`
- `websocket/ChatSocketController.java`
- `dto/ChatMessageEvent.java`

### Step-by-step

1. Add WebSocket dependency.
2. Configure STOMP endpoint.
3. Publish message events when secure share metadata is created.
4. Use WebSocket only for notifications and UI sync, not for raw secret decryption.
5. Keep file/image embed and decrypt as normal API calls.

### Starter code: WebSocket config

```java
package com.stegacrypt.config;

import org.springframework.context.annotation.Configuration;
import org.springframework.messaging.simp.config.MessageBrokerRegistry;
import org.springframework.web.socket.config.annotation.EnableWebSocketMessageBroker;
import org.springframework.web.socket.config.annotation.StompEndpointRegistry;
import org.springframework.web.socket.config.annotation.WebSocketMessageBrokerConfigurer;

@Configuration
@EnableWebSocketMessageBroker
public class WebSocketConfig implements WebSocketMessageBrokerConfigurer {

    @Override
    public void registerStompEndpoints(StompEndpointRegistry registry) {
        registry.addEndpoint("/ws").setAllowedOriginPatterns("*").withSockJS();
    }

    @Override
    public void configureMessageBroker(MessageBrokerRegistry registry) {
        registry.enableSimpleBroker("/topic", "/queue");
        registry.setApplicationDestinationPrefixes("/app");
    }
}
```

### Frontend socket hook

File:

- `frontend/src/hooks/useChatSocket.js`

```javascript
import { useEffect, useRef } from 'react';
import SockJS from 'sockjs-client';
import Stomp from 'stompjs';

export function useChatSocket(onMessage) {
  const clientRef = useRef(null);

  useEffect(() => {
    const socket = new SockJS(`${import.meta.env.VITE_API_ROOT || 'http://localhost:8080'}/ws`);
    const client = Stomp.over(socket);
    client.connect({}, () => {
      client.subscribe('/topic/chat', (frame) => {
        onMessage(JSON.parse(frame.body));
      });
    });
    clientRef.current = client;
    return () => client.disconnect(() => {});
  }, [onMessage]);
}
```

## 16. Message persistence, unread status, delivery status, and threads

Features covered:

- 11, 12, 16, 17, 18, 50, 71, 72, 73, 74, 75, 79

### New backend entities

- `ConversationEntity`
- `MessageEntity`
- `MessageReadReceiptEntity`

### Step-by-step

1. Split current share model into conversation and message tables.
2. Each secure share becomes a message row.
3. Add columns:
   - `status`
   - `isRead`
   - `readAt`
   - `isStarred`
   - `isDraft`
   - `threadParentId`
4. Add paginated search APIs.
5. Add export endpoint to CSV/JSON.
6. Update frontend chat screen to group by conversation.

### Starter code: message entity outline

```java
@Entity
@Table(name = "messages")
public class MessageEntity {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @ManyToOne(optional = false)
    private UserEntity sender;

    @ManyToOne(optional = false)
    private UserEntity recipient;

    @Lob
    @Column(nullable = false, columnDefinition = "TEXT")
    private String stegoImageBase64;

    @Column(nullable = false)
    private String status;

    @Column(nullable = false)
    private boolean readFlag;

    private Instant readAt;
    private Long threadParentId;
    private boolean starred;
    private boolean draft;
}
```

## 17. Self-destruct, decrypt-once, and scheduled messages

Features covered:

- 13, 14, 15, 96, 97, 107

### Strategy

Add fields on the message table:

- `decryptOnce`
- `selfDestructAt`
- `scheduledAt`
- `expired`
- `maxDecryptCount`
- `decryptCount`

### Step-by-step

1. Add DB fields.
2. Prevent decrypt when expired or decrypt limit reached.
3. Add a scheduled cleanup job.
4. Show countdown in frontend.
5. Use worker/job layer for scheduled sending.

### Example scheduled job

```java
package com.stegacrypt.job;

import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype.Component;

@Component
public class MessageLifecycleJob {

    @Scheduled(fixedDelay = 60000)
    public void expireMessages() {
        // Query scheduled/expired messages and update their state.
    }
}
```

## 18. File payload support: text, PDF, audio, multi-file

Features covered:

- 19, 20, 21, 22, 80, 101, 102

### Important architecture change

Right now the system is text-message oriented. To support files, define a generic payload format.

### New payload model

```json
{
  "version": "2",
  "payloadType": "FILE_BUNDLE",
  "files": [
    {
      "name": "report.pdf",
      "mimeType": "application/pdf",
      "base64": "..."
    }
  ]
}
```

### Step-by-step

1. Add frontend uploader for multiple files.
2. Validate MIME type server-side.
3. Convert files into structured payload JSON.
4. Compress payload if enabled.
5. Encrypt and embed as today.
6. On extraction, reconstruct downloadable files.

### Backend files to add

- `dto/FilePayloadDto.java`
- `service/PayloadSerializationService.java`
- `service/FileValidationService.java`

### Frontend files to add

- `frontend/src/features/upload/FilePayloadUploader.jsx`
- `frontend/src/features/upload/PayloadPreviewCard.jsx`

## 19. Batch embedding and multi-image splitting

Features covered:

- 23, 24, 25, 33, 81, 82, 83, 84, 85, 106

### Use cases

- embed one payload into many carrier images
- extract from many images at once
- split a large payload across multiple images

### Step-by-step

1. Create a batch job table.
2. Create chunk metadata format.
3. Split encrypted payload into chunks if larger than one image capacity.
4. Embed each chunk into a separate image.
5. During extraction, collect all chunks and reassemble.
6. Add frontend batch queue UI with progress bars.

### Chunk metadata fields

- `jobId`
- `chunkIndex`
- `chunkCount`
- `payloadChecksum`
- `chunkBytes`

## 20. Group messaging and group decryption

Features covered:

- 9, 10, 26, 140, 141

### Design options

Option A:

- encrypt the same message separately for each recipient public key

Option B:

- create one AES group session key and wrap it for each group member

For your current architecture, Option B is cleaner for larger groups.

### Step-by-step

1. Create `GroupEntity`, `GroupMemberEntity`.
2. Generate one random AES content key for the message.
3. Wrap that key once per recipient.
4. Store wrapped keys in payload metadata.
5. During extraction, recipient unwraps their copy of the content key.

## 21. Fingerprint verification, signatures, and trust

Features covered:

- 27, 28, 31, 132, 133

### Fingerprint verification

Show the public key fingerprint before sending and ask the user to confirm.

### Digital signatures

Add sender authenticity by signing the encrypted payload digest with sender private key.

### Step-by-step

1. Compute SHA-256 digest of payload.
2. Sign digest with sender private key.
3. Attach signature to payload header.
4. Recipient verifies with sender public key.
5. Show verified badge in frontend.

### Starter code: signature utility

```java
package com.stegacrypt.util;

import java.security.PrivateKey;
import java.security.PublicKey;
import java.security.Signature;

public class SignatureUtil {

    public static byte[] sign(byte[] data, PrivateKey privateKey) throws Exception {
        Signature signature = Signature.getInstance("SHA256withRSA");
        signature.initSign(privateKey);
        signature.update(data);
        return signature.sign();
    }

    public static boolean verify(byte[] data, byte[] sig, PublicKey publicKey) throws Exception {
        Signature signature = Signature.getInstance("SHA256withRSA");
        signature.initVerify(publicKey);
        signature.update(data);
        return signature.verify(sig);
    }
}
```

## 22. Private key encryption and key management

Features covered:

- 29, 30, 127, 128, 129, 130, 131

### Strategy

Do not store raw private keys in plaintext if moving to production.

### Step-by-step

1. Encrypt private key PEM using a passphrase-derived key.
2. Store encrypted private key blob.
3. Ask user for passphrase when needed.
4. Add import/export options:
   - PEM
   - armored text
   - JSON key bundle
   - QR for public key only
5. Add key revocation flags and trust score.

### Frontend pages

- `frontend/src/pages/KeysPage.jsx`
- `frontend/src/features/keys/PublicKeyQrCard.jsx`
- `frontend/src/features/keys/KeyImportDialog.jsx`

## 23. Advanced steganography modes

Features covered:

- 34, 35, 36, 37, 38, 39, 40, 41, 42, 43, 125, 126

### Recommended order

1. Add selectable compression mode
2. Add selectable embedding mode
3. Add integrity verification
4. Add metadata stripping
5. Add adaptive LSB
6. Then research JPEG/audio/video support

### Why this order

JPEG, audio, and video steganography are much harder than PNG/BMP LSB embedding. Build internal abstraction first.

### New interface to add

```java
package com.stegacrypt.service;

import java.awt.image.BufferedImage;

public interface EmbeddingStrategy {
    String getMode();
    void embed(BufferedImage image, byte[] payload, String seedMaterial) throws Exception;
    byte[] extract(BufferedImage image, String seedMaterial) throws Exception;
    int capacityBytes(BufferedImage image);
}
```

### Strategy implementations

- `SequentialLsbEmbeddingStrategy`
- `RandomizedLsbEmbeddingStrategy`
- `AdaptiveLsbEmbeddingStrategy`
- future: `JpegDctEmbeddingStrategy`
- future: `AudioLsbEmbeddingStrategy`

## 24. Metadata stripping and integrity verification

Features covered:

- 34, 43

### Step-by-step

1. Strip EXIF/metadata from source file before final export.
2. Add checksum/hash to encrypted payload.
3. Verify checksum before decrypt.
4. Return integrity status in extraction response.

### Suggested payload metadata additions

- `payloadHash`
- `chunkHash`
- `senderSignature`
- `createdAt`
- `mode`

## 25. Admin dashboard and moderation

Features covered:

- 44, 45, 64, 65, 93, 94, 139, 145, 146

### Backend APIs to add

- `GET /api/admin/users`
- `PATCH /api/admin/users/{id}/suspend`
- `GET /api/admin/messages`
- `GET /api/admin/audit-logs`
- `GET /api/admin/stats`
- `POST /api/admin/bulk-import`

### Frontend pages

- `frontend/src/pages/AdminDashboardPage.jsx`
- `frontend/src/pages/AdminUsersPage.jsx`
- `frontend/src/pages/AdminAuditPage.jsx`

### Audit log entity starter

```java
@Entity
@Table(name = "audit_logs")
public class AuditLogEntity {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String actorUsername;
    private String actionType;
    private String resourceType;

    @Column(columnDefinition = "TEXT")
    private String detailsJson;

    private String ipAddress;
    private Instant createdAt = Instant.now();
}
```

## 26. Rate limiting and abuse prevention

Features covered:

- 46, 53, 61, 62, 76, 77, 98

### Step-by-step

1. Add IP-based bucket rate limiter.
2. Apply limits to:
   - login
   - register
   - embed
   - extract
   - forgot password
3. Add failed-attempt tracking.
4. Lock accounts temporarily after repeated abuse.
5. Add blocked-user relation table.

### Example limiter service

```java
package com.stegacrypt.security;

import io.github.bucket4j.Bandwidth;
import io.github.bucket4j.Bucket;
import io.github.bucket4j.Refill;
import org.springframework.stereotype.Service;

import java.time.Duration;
import java.util.Map;
import java.util.concurrent.ConcurrentHashMap;

@Service
public class RateLimitService {

    private final Map<String, Bucket> buckets = new ConcurrentHashMap<>();

    public boolean tryConsume(String key) {
        Bucket bucket = buckets.computeIfAbsent(key, k ->
            Bucket.builder()
                .addLimit(Bandwidth.classic(20, Refill.greedy(20, Duration.ofMinutes(1))))
                .build()
        );
        return bucket.tryConsume(1);
    }
}
```

## 27. Notifications system

Features covered:

- 47, 91, 92

### Notification types

- new secure share
- share decrypted
- scheduled message delivered
- password reset requested
- suspicious login detected

### Backend pieces

- `NotificationEntity`
- `NotificationService`
- WebSocket event publishing
- optional email fallback

## 28. Profile, contacts, and settings

Features covered:

- 48, 49, 57, 86, 87, 88, 100, 148, 150

### New frontend pages

- `ProfilePage.jsx`
- `ContactsPage.jsx`
- `SettingsPage.jsx`
- `OnboardingPage.jsx`

### Settings to support

- theme
- language
- auto-download
- privacy level
- notification preferences
- default compression mode
- preferred embedding mode

## 29. Object storage and file delivery

Features covered:

- 103, 104, 105

### Why this matters

Base64 in DB works only for small demos. For production, store stego files in object storage.

### Step-by-step

1. Add `StorageService` abstraction.
2. Create local and cloud implementations.
3. Store only object key in DB.
4. Generate signed URLs for downloads.

### Interface

```java
package com.stegacrypt.storage;

public interface StorageService {
    String put(byte[] data, String contentType, String path) throws Exception;
    byte[] get(String objectKey) throws Exception;
    String signedDownloadUrl(String objectKey) throws Exception;
    void delete(String objectKey) throws Exception;
}
```

## 30. API versioning and OpenAPI docs

Features covered:

- 118, 119, 120

### Implementation

1. Put new endpoints under `/api/v1/...`
2. Add OpenAPI generator library
3. Publish Swagger UI
4. Export Postman collection

## 31. Testing strategy for all add-ons

Features covered:

- 121, 122, 123, 124

### Backend tests

- repository tests
- controller integration tests
- crypto utility tests
- stego capacity tests
- lifecycle rules tests

### Frontend tests

- form validation tests
- auth flow tests
- chat UI tests
- upload workflow tests

### End-to-end tests

- register
- verify email
- login
- send secure share
- receive socket event
- decrypt message
- export conversation

## 32. Database schema you should eventually have

Recommended tables:

- `users`
- `roles`
- `user_roles`
- `sessions`
- `password_reset_otps`
- `email_verification_tokens`
- `conversations`
- `messages`
- `message_receipts`
- `groups`
- `group_members`
- `notifications`
- `audit_logs`
- `blocked_users`
- `contacts`
- `settings`
- `jobs`
- `job_chunks`
- `attachments`
- `api_keys`
- `workspace_members`

## 33. Frontend route plan

Add routes like:

```text
/
/login
/register
/forgot-password
/verify-email
/chat
/chat/:conversationId
/upload
/extract
/keys
/profile
/contacts
/settings
/admin
/admin/users
/admin/audit
/onboarding
```

## 34. Suggested frontend state split

Use a small store or context structure:

- `authStore`
- `chatStore`
- `settingsStore`
- `notificationStore`
- `uploadStore`

This keeps features modular as the app grows.

## 35. File-by-file modification map for your current repo

### Backend existing files to modify

- `backend/pom.xml`
- `backend/src/main/resources/application.properties`
- `backend/src/main/java/com/stegacrypt/StegaCryptApplication.java`
- `backend/src/main/java/com/stegacrypt/controller/AuthChatController.java`
- `backend/src/main/java/com/stegacrypt/controller/SteganographyController.java`
- `backend/src/main/java/com/stegacrypt/service/AuthChatService.java`
- `backend/src/main/java/com/stegacrypt/service/SteganographyService.java`
- `backend/src/main/java/com/stegacrypt/service/ImageProcessingService.java`
- `backend/src/main/java/com/stegacrypt/util/AESUtil.java`
- `backend/src/main/java/com/stegacrypt/util/RSAUtil.java`
- `backend/src/main/java/com/stegacrypt/util/ValidationUtil.java`

### Frontend existing files to modify

- `frontend/package.json`
- `frontend/src/services/api.js`
- `frontend/src/App.jsx`
- `frontend/src/App.css`
- current chat/embed/extract components to support richer flows

## 36. Migration path from the current code

### Phase 1

Keep current controllers, but add new `/api/v1/...` endpoints.

### Phase 2

Keep old in-memory auth only for local demo fallback while you test DB auth.

### Phase 3

Switch frontend gradually to new JWT APIs.

### Phase 4

Move share storage from Base64-in-memory to DB metadata plus object storage.

### Phase 5

Add WebSocket events and advanced chat features.

### Phase 6

Introduce advanced stego and key-management features.

This phased migration avoids breaking the existing working demo.

## 37. Priority roadmap: what to build first

If you want the most realistic and valuable roadmap, build in this order:

1. PostgreSQL + JPA + Flyway
2. BCrypt + JWT + Spring Security
3. Email verification + OTP reset
4. Role-based admin and profile/settings
5. Persistent conversations/messages
6. Search, pagination, read status, export
7. WebSocket notifications and presence
8. File payload support
9. Batch jobs and storage service
10. Signatures, fingerprint verification, key passphrase encryption
11. Advanced embedding modes
12. Admin analytics, rate limiting, observability

## 38. Practical warnings

Some features are medium difficulty:

- unread/read tracking
- pagination
- export
- profile and settings
- admin dashboard

Some features are high difficulty:

- group decryption keys
- multi-image payload splitting
- digital signatures integrated into payloads
- private key passphrase encryption
- object storage + signed URLs
- WebSocket chat synchronization

Some features are very high difficulty or research-heavy:

- JPEG steganography
- audio steganography
- video steganography
- adaptive LSB with strong visual heuristics
- ECC/ECDH migration
- hardware-backed key storage

## 39. Minimum production-ready addon set

If your goal is a realistic "v2", the minimum strong upgrade set is:

- database persistence
- JWT authentication
- BCrypt password hashing
- email verification
- forgot/reset password
- role-based access control
- conversation/message persistence
- unread/read status
- pagination and search
- audit logs
- rate limiting
- object storage
- admin dashboard
- notifications
- signature verification

## 40. Example final target architecture

Backend:

- Spring Boot REST + WebSocket
- PostgreSQL
- Flyway
- JWT auth
- BCrypt
- object storage
- job worker
- audit log service
- notification service
- modular stego strategy layer

Frontend:

- React + router
- feature-based folders
- auth context/store
- real-time chat pages
- upload/extract workflows
- admin and settings pages
- multilingual and accessible UI

## 41. How to actually use this guide

Do not treat this as one giant coding task. Use it as an implementation workbook.

Recommended working style:

1. Pick one track.
2. Create feature branch.
3. Add DB or API pieces first.
4. Add frontend pages next.
5. Add tests.
6. Update docs.
7. Deploy and validate.

## 42. Best next step for your exact project

For this existing repo, the smartest immediate next milestone is:

1. real database storage
2. BCrypt password hashing
3. JWT auth
4. email verification and password reset
5. persistent conversation/message model

That gives you the strongest foundation for almost every other enhancement in this guide.

## 43. Suggested document split later

This master file is the big roadmap. As you implement, split it into focused docs:

- `docs/PHASE_1_DATABASE_AND_AUTH.md`
- `docs/PHASE_2_CHAT_AND_REALTIME.md`
- `docs/PHASE_3_PAYLOAD_AND_MEDIA.md`
- `docs/PHASE_4_CRYPTO_AND_KEYS.md`
- `docs/PHASE_5_ADMIN_AND_OPERATIONS.md`

## 44. Final summary

You now have:

- your original 50 feature ideas
- 100 more feature ideas
- the exact package/folder growth plan
- dependency guidance
- migration order
- backend and frontend integration plan
- starter code for the highest-impact add-on topics

If you want the cleanest path forward, implement this roadmap in phases and keep your current cryptography and steganography core as the engine. Most of the work now is about adding persistence, authentication, messaging architecture, file handling, and production-grade platform features around that core.
