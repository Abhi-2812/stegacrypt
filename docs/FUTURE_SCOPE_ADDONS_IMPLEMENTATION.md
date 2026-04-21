# Future Scope Add-ons Implementation Guide

This guide focuses only on the future-scope items you highlighted:

- persistent user accounts with database-backed history
- cloud storage for generated stego images
- multiple recipients in one stego image
- steganalysis resistance scoring
- other media types such as audio or video
- mobile version of the application
- stronger automated test coverage
- stronger production security and session management
- better image suitability recommendations before embedding

This file gives:

- coverage check against the master guide
- exact files to add or change
- code blocks for the main add-on building blocks
- old-code change notes for your current repo

## 1. Coverage check

Everything in your list is now covered.

Already present in the master guide:

- persistent accounts and history
- cloud storage
- multiple recipients/group sharing
- audio/video roadmap
- automated testing
- production auth and session management
- image suitability recommendations

Newly made more explicit:

- steganalysis resistance scoring
- dedicated mobile app path using the same backend APIs

## 2. Important scope note

Implementing all of these fully inside the current codebase would be a very large multi-phase build, not a single safe edit. So this guide gives you production-shaped code building blocks and the exact old-file changes needed, while keeping the current app stable.

## 3. Add-on 1: persistent user accounts with database-backed history

### Goal

Replace in-memory maps in `AuthChatService` with a real database model for:

- users
- sessions
- conversations
- messages
- history

### New backend files

- `backend/src/main/java/com/stegacrypt/entity/UserEntity.java`
- `backend/src/main/java/com/stegacrypt/entity/ConversationEntity.java`
- `backend/src/main/java/com/stegacrypt/entity/MessageEntity.java`
- `backend/src/main/java/com/stegacrypt/repository/UserRepository.java`
- `backend/src/main/java/com/stegacrypt/repository/ConversationRepository.java`
- `backend/src/main/java/com/stegacrypt/repository/MessageRepository.java`
- `backend/src/main/java/com/stegacrypt/service/UserService.java`
- `backend/src/main/java/com/stegacrypt/service/ConversationService.java`
- `backend/src/main/java/com/stegacrypt/service/MessageService.java`

### Old files to change

- `backend/pom.xml`
- `backend/src/main/resources/application.properties`
- `backend/src/main/java/com/stegacrypt/service/AuthChatService.java`
- `backend/src/main/java/com/stegacrypt/controller/AuthChatController.java`

### Code: `ConversationEntity.java`

```java
package com.stegacrypt.entity;

import jakarta.persistence.*;
import java.time.Instant;

@Entity
@Table(name = "conversations")
public class ConversationEntity {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false)
    private boolean groupConversation = false;

    @Column(length = 180)
    private String title;

    @Column(nullable = false)
    private Instant createdAt = Instant.now();

    @Column(nullable = false)
    private Instant updatedAt = Instant.now();

    public Long getId() { return id; }
    public boolean isGroupConversation() { return groupConversation; }
    public void setGroupConversation(boolean groupConversation) { this.groupConversation = groupConversation; }
    public String getTitle() { return title; }
    public void setTitle(String title) { this.title = title; }
    public Instant getCreatedAt() { return createdAt; }
    public Instant getUpdatedAt() { return updatedAt; }
    public void setUpdatedAt(Instant updatedAt) { this.updatedAt = updatedAt; }
}
```

### Code: `MessageEntity.java`

```java
package com.stegacrypt.entity;

import jakarta.persistence.*;
import java.time.Instant;

@Entity
@Table(name = "messages")
public class MessageEntity {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @ManyToOne(optional = false)
    private ConversationEntity conversation;

    @ManyToOne(optional = false)
    private UserEntity sender;

    @Column(nullable = false)
    private String messageType = "STEGO_IMAGE";

    @Column(nullable = false)
    private String deliveryStatus = "SENT";

    @Column(nullable = false)
    private boolean readFlag = false;

    private Instant readAt;

    @Column(nullable = false)
    private Instant createdAt = Instant.now();

    @Column(nullable = false, length = 260)
    private String stegoStorageKey;

    @Lob
    @Column(columnDefinition = "TEXT")
    private String previewText;

    public Long getId() { return id; }
    public ConversationEntity getConversation() { return conversation; }
    public void setConversation(ConversationEntity conversation) { this.conversation = conversation; }
    public UserEntity getSender() { return sender; }
    public void setSender(UserEntity sender) { this.sender = sender; }
    public String getMessageType() { return messageType; }
    public void setMessageType(String messageType) { this.messageType = messageType; }
    public String getDeliveryStatus() { return deliveryStatus; }
    public void setDeliveryStatus(String deliveryStatus) { this.deliveryStatus = deliveryStatus; }
    public boolean isReadFlag() { return readFlag; }
    public void setReadFlag(boolean readFlag) { this.readFlag = readFlag; }
    public Instant getReadAt() { return readAt; }
    public void setReadAt(Instant readAt) { this.readAt = readAt; }
    public Instant getCreatedAt() { return createdAt; }
    public String getStegoStorageKey() { return stegoStorageKey; }
    public void setStegoStorageKey(String stegoStorageKey) { this.stegoStorageKey = stegoStorageKey; }
    public String getPreviewText() { return previewText; }
    public void setPreviewText(String previewText) { this.previewText = previewText; }
}
```

### Code: `ConversationRepository.java`

```java
package com.stegacrypt.repository;

import com.stegacrypt.entity.ConversationEntity;
import org.springframework.data.jpa.repository.JpaRepository;

public interface ConversationRepository extends JpaRepository<ConversationEntity, Long> {
}
```

### Code: `MessageRepository.java`

```java
package com.stegacrypt.repository;

import com.stegacrypt.entity.MessageEntity;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.Pageable;
import org.springframework.data.jpa.repository.JpaRepository;

public interface MessageRepository extends JpaRepository<MessageEntity, Long> {
    Page<MessageEntity> findByConversationIdOrderByCreatedAtDesc(Long conversationId, Pageable pageable);
}
```

### Old code change summary

In `AuthChatService.java`:

- remove `ConcurrentHashMap` storage over time
- replace `sendShare(...)` with DB-backed message creation
- replace `getVisibleShares(...)` with repository query
- replace seeded runtime users with DB seed logic

In `AuthChatController.java`:

- stop returning all chat data in one bootstrap blob
- add paginated conversation and message APIs

## 4. Add-on 2: cloud storage for generated stego images

### Goal

Stop keeping stego images only in memory or inline Base64. Store them using local disk first, and later cloud storage such as S3.

### New backend files

- `backend/src/main/java/com/stegacrypt/storage/StorageService.java`
- `backend/src/main/java/com/stegacrypt/storage/LocalStorageService.java`
- `backend/src/main/java/com/stegacrypt/storage/S3StorageService.java`

### Old files to change

- `backend/src/main/resources/application.properties`
- `backend/src/main/java/com/stegacrypt/service/AuthChatService.java`

### Code: `StorageService.java`

```java
package com.stegacrypt.storage;

public interface StorageService {
    String put(byte[] data, String contentType, String path) throws Exception;
    byte[] get(String objectKey) throws Exception;
    void delete(String objectKey) throws Exception;
}
```

### Code: `LocalStorageService.java`

```java
package com.stegacrypt.storage;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Service;

import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.Paths;
import java.util.UUID;

@Service
public class LocalStorageService implements StorageService {

    private final Path rootDir;

    public LocalStorageService(@Value("${app.storage.local-dir:./uploads}") String dir) throws Exception {
        this.rootDir = Paths.get(dir).toAbsolutePath().normalize();
        Files.createDirectories(rootDir);
    }

    @Override
    public String put(byte[] data, String contentType, String path) throws Exception {
        String filename = path + "-" + UUID.randomUUID() + ".png";
        Path target = rootDir.resolve(filename).normalize();
        Files.createDirectories(target.getParent());
        Files.write(target, data);
        return filename.replace("\\", "/");
    }

    @Override
    public byte[] get(String objectKey) throws Exception {
        return Files.readAllBytes(rootDir.resolve(objectKey).normalize());
    }

    @Override
    public void delete(String objectKey) throws Exception {
        Files.deleteIfExists(rootDir.resolve(objectKey).normalize());
    }
}
```

### Application config

```properties
app.storage.mode=${STORAGE_MODE:local}
app.storage.local-dir=${STORAGE_LOCAL_DIR:./uploads}
```

### Old code change summary

In `AuthChatService.java`:

- replace `Base64.getEncoder().encodeToString(stegoImageBytes)` storage with:
  `String storageKey = storageService.put(stegoImageBytes, "image/png", "stego/chat");`
- save only `storageKey` in DB
- load image bytes later using `storageService.get(storageKey)`

## 5. Add-on 3: multiple recipients in one stego image

### Goal

Allow one image to carry one encrypted content payload, but with multiple wrapped keys so several intended recipients can decrypt it.

### New backend files

- `backend/src/main/java/com/stegacrypt/dto/MultiRecipientPayloadDto.java`
- `backend/src/main/java/com/stegacrypt/service/MultiRecipientPayloadService.java`

### Old files to change

- `backend/src/main/java/com/stegacrypt/util/AESUtil.java`
- `backend/src/main/java/com/stegacrypt/controller/SteganographyController.java`
- `backend/src/main/java/com/stegacrypt/controller/AuthChatController.java`

### Code: `MultiRecipientPayloadDto.java`

```java
package com.stegacrypt.dto;

import java.util.List;

public record MultiRecipientPayloadDto(
    String payloadVersion,
    String encryptionMode,
    byte[] cipherText,
    byte[] iv,
    List<RecipientWrappedKeyDto> recipients
) {
    public record RecipientWrappedKeyDto(String recipientId, byte[] wrappedKey) {}
}
```

### Code: `MultiRecipientPayloadService.java`

```java
package com.stegacrypt.service;

import com.stegacrypt.dto.MultiRecipientPayloadDto;
import com.stegacrypt.util.RSAUtil;
import org.springframework.stereotype.Service;

import javax.crypto.KeyGenerator;
import javax.crypto.SecretKey;
import javax.crypto.Cipher;
import javax.crypto.spec.GCMParameterSpec;
import java.security.PublicKey;
import java.security.SecureRandom;
import java.util.ArrayList;
import java.util.List;

@Service
public class MultiRecipientPayloadService {

    public MultiRecipientPayloadDto encryptForRecipients(byte[] plainData, List<String> recipientIds, List<PublicKey> keys) throws Exception {
        KeyGenerator generator = KeyGenerator.getInstance("AES");
        generator.init(256);
        SecretKey sessionKey = generator.generateKey();

        byte[] iv = new byte[12];
        new SecureRandom().nextBytes(iv);

        Cipher cipher = Cipher.getInstance("AES/GCM/NoPadding");
        cipher.init(Cipher.ENCRYPT_MODE, sessionKey, new GCMParameterSpec(128, iv));
        byte[] cipherText = cipher.doFinal(plainData);

        List<MultiRecipientPayloadDto.RecipientWrappedKeyDto> recipients = new ArrayList<>();
        for (int i = 0; i < keys.size(); i++) {
            recipients.add(new MultiRecipientPayloadDto.RecipientWrappedKeyDto(
                recipientIds.get(i),
                RSAUtil.encryptSessionKey(sessionKey.getEncoded(), keys.get(i))
            ));
        }

        return new MultiRecipientPayloadDto("MRP_V1", "AES-256-GCM + RSA-OAEP", cipherText, iv, recipients);
    }
}
```

### Old code change summary

In `SteganographyController.java`:

- add new endpoint such as `POST /api/v1/embed/multi-recipient`
- accept recipient list instead of one public key

In `AESUtil.java`:

- do not overload the current single-recipient raw format too aggressively
- prefer a higher-level payload envelope for multi-recipient payloads

## 6. Add-on 4: steganalysis resistance scoring

### Goal

Score how safe or suitable an image is before embedding.

The score should estimate:

- texture richness
- flat-area risk
- payload-to-capacity ratio
- image format quality
- likely detectability risk

### New backend files

- `backend/src/main/java/com/stegacrypt/service/SteganalysisScoringService.java`
- `backend/src/main/java/com/stegacrypt/dto/SteganalysisScoreDto.java`

### Old files to change

- `backend/src/main/java/com/stegacrypt/controller/SteganographyController.java`
- `frontend/src/components/EmbedSection.jsx`
- `frontend/src/services/api.js`

### Code: `SteganalysisScoreDto.java`

```java
package com.stegacrypt.dto;

public record SteganalysisScoreDto(
    int overallScore,
    int textureScore,
    int capacityScore,
    int payloadPressureScore,
    String riskLevel,
    String recommendation
) {}
```

### Code: `SteganalysisScoringService.java`

```java
package com.stegacrypt.service;

import com.stegacrypt.dto.SteganalysisScoreDto;
import org.springframework.stereotype.Service;

import java.awt.image.BufferedImage;

@Service
public class SteganalysisScoringService {

    public SteganalysisScoreDto score(BufferedImage image, int payloadBytes) {
        int totalPixels = image.getWidth() * image.getHeight();
        int capacityBytes = Math.max(1, (totalPixels - 64) / 8);
        int payloadPressure = Math.min(100, (int) ((payloadBytes * 100.0) / capacityBytes));

        int textureScore = estimateTextureScore(image);
        int capacityScore = payloadPressure < 40 ? 90 : payloadPressure < 65 ? 70 : 45;
        int payloadPressureScore = 100 - payloadPressure;
        int overall = Math.max(0, Math.min(100, (textureScore + capacityScore + payloadPressureScore) / 3));

        String riskLevel = overall >= 80 ? "LOW" : overall >= 60 ? "MEDIUM" : "HIGH";
        String recommendation = overall >= 80
            ? "Good carrier image for embedding."
            : overall >= 60
                ? "Usable, but prefer a larger or more textured image."
                : "Risky image. Choose a more detailed image or reduce payload.";

        return new SteganalysisScoreDto(overall, textureScore, capacityScore, payloadPressureScore, riskLevel, recommendation);
    }

    private int estimateTextureScore(BufferedImage image) {
        long totalDiff = 0;
        int samples = 0;

        for (int y = 1; y < image.getHeight(); y += 4) {
            for (int x = 1; x < image.getWidth(); x += 4) {
                int current = image.getRGB(x, y) & 0xFF;
                int left = image.getRGB(x - 1, y) & 0xFF;
                int top = image.getRGB(x, y - 1) & 0xFF;
                totalDiff += Math.abs(current - left) + Math.abs(current - top);
                samples += 2;
            }
        }

        if (samples == 0) return 50;
        int avg = (int) (totalDiff / samples);
        return Math.max(20, Math.min(95, avg * 2));
    }
}
```

### New endpoint suggestion

Add to `SteganographyController.java`:

```java
@PostMapping("/v1/stego/score")
public ResponseEntity<?> scoreCarrier(
    @RequestParam("image") MultipartFile image,
    @RequestParam("payloadBytes") int payloadBytes
) {
    try {
        BufferedImage carrier = imageService.loadImage(image);
        return ResponseEntity.ok(steganalysisScoringService.score(carrier, payloadBytes));
    } catch (Exception e) {
        return buildErrorResponse("Scoring failed: " + e.getMessage(), HttpStatus.BAD_REQUEST);
    }
}
```

## 7. Add-on 5: image suitability recommendations before embedding

### Goal

Rank candidate images and tell the user which one is best before embedding starts.

### New backend file

- `backend/src/main/java/com/stegacrypt/service/SuitabilityRecommendationService.java`

### Code

```java
package com.stegacrypt.service;

import com.stegacrypt.dto.SteganalysisScoreDto;
import org.springframework.stereotype.Service;

import java.awt.image.BufferedImage;

@Service
public class SuitabilityRecommendationService {

    private final SteganalysisScoringService scoringService;

    public SuitabilityRecommendationService(SteganalysisScoringService scoringService) {
        this.scoringService = scoringService;
    }

    public SteganalysisScoreDto recommend(BufferedImage image, int payloadBytes) {
        return scoringService.score(image, payloadBytes);
    }
}
```

### Frontend old file changes

In `frontend/src/services/api.js` add:

```javascript
async scoreCarrierImage(imageFile, payloadBytes) {
  const formData = new FormData();
  formData.append('image', imageFile);
  formData.append('payloadBytes', payloadBytes);
  const response = await axios.post(`${API_BASE_URL}/v1/stego/score`, formData);
  return response.data;
}
```

In `frontend/src/components/EmbedSection.jsx`:

- calculate estimated payload bytes
- call score API after user selects image and payload
- show:
  - overall score
  - risk level
  - recommendation

## 8. Add-on 6: supporting other media types such as audio or video

### Goal

Extend beyond images.

### Best implementation path

Do this in two separate tracks:

1. payload type support:
   hiding audio or video files inside images
2. carrier type support:
   using audio/video themselves as the carrier

The first is easier and should come first.

### New backend interface

```java
package com.stegacrypt.service;

public interface CarrierHandler {
    String carrierType();
    byte[] embed(byte[] carrierBytes, byte[] payloadBytes, String seedMaterial) throws Exception;
    byte[] extract(byte[] carrierBytes, String seedMaterial) throws Exception;
}
```

### Suggested implementations

- `ImageCarrierHandler`
- future `AudioCarrierHandler`
- future `VideoCarrierHandler`

### Old code change summary

In `SteganographyController.java`:

- move image-specific logic behind a handler abstraction before adding audio/video carrier support

In `ImageProcessingService.java`:

- keep only image-specific decode/encode logic

## 9. Add-on 7: building a mobile version of the application

### Goal

Reuse backend APIs for Android and iOS clients.

### Recommended approach

1. keep backend API-first and versioned
2. create a separate mobile client using React Native
3. reuse:
   - auth APIs
   - chat APIs
   - embed/extract APIs
   - scoring APIs

### Mobile project structure suggestion

```text
mobile/
  src/
    screens/
    components/
    services/
    hooks/
    navigation/
```

### Important mobile-specific work

- secure local token storage
- file-picker integration
- image compression and preview
- background upload handling
- share sheet support

## 10. Add-on 8: stronger automated test coverage

### Goal

Raise confidence before production-style changes.

### New backend test files

- `backend/src/test/java/com/stegacrypt/util/AESUtilTest.java`
- `backend/src/test/java/com/stegacrypt/service/SteganalysisScoringServiceTest.java`
- `backend/src/test/java/com/stegacrypt/controller/SteganographyControllerTest.java`

### Code: `SteganalysisScoringServiceTest.java`

```java
package com.stegacrypt.service;

import com.stegacrypt.dto.SteganalysisScoreDto;
import org.junit.jupiter.api.Test;

import java.awt.image.BufferedImage;

import static org.junit.jupiter.api.Assertions.*;

class SteganalysisScoringServiceTest {

    @Test
    void scoreReturnsValidRange() {
        BufferedImage image = new BufferedImage(300, 300, BufferedImage.TYPE_INT_ARGB);
        SteganalysisScoringService service = new SteganalysisScoringService();

        SteganalysisScoreDto dto = service.score(image, 1024);

        assertTrue(dto.overallScore() >= 0 && dto.overallScore() <= 100);
        assertNotNull(dto.riskLevel());
        assertNotNull(dto.recommendation());
    }
}
```

### Frontend tests to add

- embed form validation tests
- score-card rendering tests
- login flow tests
- chat conversation list tests

## 11. Add-on 9: stronger production security and session management

### Goal

Replace simple in-memory token behavior with production-style session controls.

### New backend files

- `backend/src/main/java/com/stegacrypt/entity/SessionEntity.java`
- `backend/src/main/java/com/stegacrypt/repository/SessionRepository.java`
- `backend/src/main/java/com/stegacrypt/service/SessionService.java`
- `backend/src/main/java/com/stegacrypt/security/JwtAuthenticationFilter.java`

### Code: `SessionEntity.java`

```java
package com.stegacrypt.entity;

import jakarta.persistence.*;
import java.time.Instant;

@Entity
@Table(name = "sessions")
public class SessionEntity {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @ManyToOne(optional = false)
    private UserEntity user;

    @Column(nullable = false, unique = true, length = 160)
    private String refreshTokenId;

    @Column(nullable = false)
    private Instant createdAt = Instant.now();

    @Column(nullable = false)
    private Instant expiresAt;

    @Column(nullable = false)
    private boolean revoked = false;

    public Long getId() { return id; }
    public UserEntity getUser() { return user; }
    public void setUser(UserEntity user) { this.user = user; }
    public String getRefreshTokenId() { return refreshTokenId; }
    public void setRefreshTokenId(String refreshTokenId) { this.refreshTokenId = refreshTokenId; }
    public Instant getCreatedAt() { return createdAt; }
    public Instant getExpiresAt() { return expiresAt; }
    public void setExpiresAt(Instant expiresAt) { this.expiresAt = expiresAt; }
    public boolean isRevoked() { return revoked; }
    public void setRevoked(boolean revoked) { this.revoked = revoked; }
}
```

### Security upgrades to implement

1. JWT access token
2. refresh token rotation
3. logout current session
4. logout all sessions
5. account lockout on repeated failures
6. role-based access control
7. audit logs
8. rate limiting

## 12. Old code changes you should apply for these add-ons

### `backend/pom.xml`

Add:

- security
- data-jpa
- postgresql
- flyway
- actuator
- websocket
- mail
- bucket4j

### `backend/src/main/resources/application.properties`

Add:

- datasource config
- JWT config
- storage config
- mail config
- optional profile config

### `backend/src/main/java/com/stegacrypt/service/AuthChatService.java`

Change direction:

- from in-memory demo service
- to orchestration layer that delegates to:
  - `AuthService`
  - `ConversationService`
  - `MessageService`
  - `StorageService`

### `backend/src/main/java/com/stegacrypt/controller/SteganographyController.java`

Add:

- carrier scoring endpoint
- multi-recipient endpoint
- versioned `/api/v1/stego/...` endpoints

### `frontend/src/services/api.js`

Add:

- bearer-token interceptor
- score-carrier API
- conversation list API
- paginated messages API

### `frontend/src/components/EmbedSection.jsx`

Add:

- pre-embed score UI
- image recommendation UI
- multi-recipient selection UI

### `frontend/src/components/ShareDemoSection.jsx`

Refactor into:

- conversation sidebar
- thread view
- message composer
- decrypt panel

## 13. What I recommend you build first from this future-scope list

Best order:

1. persistent accounts with DB history
2. stronger production auth and session management
3. cloud storage
4. suitability recommendation and steganalysis score
5. test coverage improvements
6. multi-recipient payloads
7. media expansion
8. mobile client

## 14. Final note

These add-ons are now:

- checked against the master guide
- explicitly documented
- mapped to old-file changes
- supported with starter code blocks for the most important backend pieces

This guide is the safest way to move forward without destabilizing the current working demo.
