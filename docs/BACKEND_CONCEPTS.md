# StegaCrypt Backend Concepts

This file explains the backend of StegaCrypt in simple, project-specific terms. It is meant to help you understand how the backend is organized, how data flows through it, and why each major component exists.

## 1. What the backend does

The backend is a Java 17 Spring Boot application that exposes REST APIs for:

- generating RSA key pairs
- embedding an encrypted message inside an image
- extracting a hidden message from a stego image
- checking how much data an image can hold
- supporting a demo secure chat/share flow with in-memory users

In short, the backend combines:

- web APIs
- image processing
- cryptography
- steganography
- input validation

## 2. Main backend stack

- Framework: Spring Boot
- Language: Java 17
- Build tool: Maven
- Web layer: `spring-boot-starter-web`
- Validation support: `spring-boot-starter-validation`
- Image handling: Java `ImageIO`
- Crypto: Java Cryptography APIs

The project entry point is:

- `backend/src/main/java/com/stegacrypt/StegaCryptApplication.java`

This class starts the app and also configures CORS for `/api/**`.

## 3. Backend architecture

The backend follows a simple layered structure:

- `controller`: receives HTTP requests and sends HTTP responses
- `service`: contains business logic
- `util`: contains reusable helper logic like RSA, AES, validation, bit conversion, PRNG
- `exception`: handles common errors centrally
- `resources`: application configuration

Think of it like this:

1. Client sends request
2. Controller reads request parameters
3. Service and util classes do the real work
4. Controller returns JSON or image bytes

## 4. Important folders

### `controller`

This is the API layer.

- `SteganographyController.java`
  Handles key generation, embed, extract, capacity, health, and demo-user endpoints.
- `AuthChatController.java`
  Handles registration, login, chat bootstrap, secure share send/decrypt, and shared-image extraction.

### `service`

This is the logic layer.

- `SteganographyService.java`
  Hides encrypted bytes in image pixels and extracts them back.
- `ImageProcessingService.java`
  Loads, validates, converts, and saves images.
- `CompressionService.java`
  Compresses text with GZIP before encryption when useful.
- `AuthChatService.java`
  Manages demo auth, user accounts, sessions, and in-memory chat shares.
- `DemoUserService.java`
  Creates demo project users with generated RSA keys.

### `util`

This is the helper/algorithm layer.

- `RSAUtil.java`
  Generates RSA keys, converts PEM, parses PEM, wraps AES keys, derives public key, creates fingerprint.
- `AESUtil.java`
  Encrypts/decrypts the actual message using AES-GCM and a hybrid payload format.
- `PRNGUtil.java`
  Generates deterministic randomized pixel positions from key-based seed material.
- `BitUtil.java`
  Converts bytes to bits and bits back to bytes.
- `ValidationUtil.java`
  Checks messages, keys, image size, image dimensions, and capacity.

### `exception`

- `GlobalExceptionHandler.java`
  Converts common exceptions into JSON error responses.

## 5. Why hybrid encryption is used

The backend does not encrypt the full message directly with RSA. Instead, it uses hybrid encryption:

1. Generate a random AES session key
2. Encrypt the message using AES-256-GCM
3. Encrypt the AES session key using RSA public key
4. Store both inside one payload

Why this is better:

- RSA is good for small data like keys, not large messages
- AES is fast and efficient for real message data
- AES-GCM provides both confidentiality and integrity
- RSA lets only the matching private key holder recover the AES key

So the project gets both speed and security.

## 6. Crypto flow in this project

### During embedding

1. The user uploads an image and plaintext message
2. The backend reads the recipient public key
3. The message may be compressed with GZIP
4. `AESUtil.encrypt(...)` creates a random AES key
5. The message bytes are encrypted using AES-GCM
6. The AES key is wrapped using RSA-OAEP with SHA-256
7. The final encrypted payload is sent to the steganography layer
8. The payload is hidden in the image

### During extraction

1. The user uploads the stego image and private key
2. The backend regenerates the same random pixel order using key-derived seed material
3. Hidden encrypted bytes are extracted from the image
4. `AESUtil.decrypt(...)` unwraps the AES key using RSA private key
5. AES-GCM decrypts the payload
6. If compression flag is set, the backend decompresses the result
7. The plaintext message is returned

## 7. AES payload format

The encrypted payload is not just raw ciphertext. It has structure.

`AESUtil` stores:

- magic bytes: identifies this as a StegaCrypt payload
- version: supports format evolution
- flags: currently used to mark compression
- wrapped key length
- IV length
- ciphertext length
- RSA-wrapped AES key
- AES-GCM IV
- AES-GCM ciphertext

This is important because extraction must know how to correctly split and decode the encrypted bytes.

## 8. How steganography works here

The project uses LSB steganography.

LSB means Least Significant Bit. In each selected pixel, the backend changes only the last bit of the blue channel. That tiny change is usually invisible to the human eye.

### Image embedding strategy

- The first part of the image stores the hidden payload length
- The actual encrypted data is stored in randomized pixel locations
- Only one bit is stored per selected pixel

### Header design

`SteganographyService` reserves `64` header pixels.

In practice:

- the first `32` bits store the payload length
- the rest of the reserved header space is left unused for now

This means the design has room for extension later, even though only part of the reserved header is currently used.

### Randomized pixel selection

Instead of embedding data sequentially through the whole image, the backend:

1. creates a seed from the RSA public key fingerprint
2. generates a deterministic shuffled pixel order
3. uses that order for both embedding and extraction

Why this matters:

- makes the hidden data less predictable
- ties extraction to the correct key material
- adds another layer of protection beyond raw encryption

Important note:

This is not the same as cryptographic security by itself. The main security still comes from RSA + AES-GCM. The randomized pixel order mostly improves concealment and makes extraction dependent on the correct key path.

## 9. Why PNG output is mandatory

After embedding, the backend always returns PNG.

Reason:

- PNG is lossless
- JPG/JPEG is lossy
- lossy compression changes pixel values
- changing pixel values destroys LSB hidden data

So even if the input is JPG, the output must be PNG to preserve the secret data.

## 10. Image handling concepts

`ImageProcessingService` does several important jobs:

- checks that the uploaded file is really an allowed image type
- validates file size and dimensions before full decoding
- loads the image into memory
- converts it into `TYPE_INT_ARGB`
- saves the final result as PNG

The ARGB conversion is useful because the backend wants a consistent pixel format before modifying color channels.

## 11. Compression logic

`CompressionService` uses GZIP.

Compression is optional and only used when it seems beneficial. The current logic recommends compression mainly for larger messages because very short strings can actually become larger after GZIP overhead.

Benefits of compression:

- reduces payload size
- increases effective image capacity
- slightly changes byte patterns before encryption

Flow:

1. plain text is compressed
2. compressed bytes are encrypted
3. a flag is stored in encrypted payload metadata
4. extraction checks the flag and decompresses only if needed

## 12. Validation logic

`ValidationUtil` protects the backend from bad input and unsupported conditions.

It validates:

- message is not empty
- message is within allowed size
- keys are present
- image file is not too large
- image dimensions are within limits
- image capacity is enough for payload

This is very important because steganography and crypto both fail badly if invalid inputs are allowed too far into the pipeline.

## 13. API flow for normal steganography

### `POST /api/generate-keys`

Purpose:

- generate an RSA-2048 key pair

Returns:

- public key PEM
- private key PEM
- metadata like algorithm and timestamp

### `POST /api/embed`

Purpose:

- hide an encrypted message inside an uploaded image

Inputs:

- `image`
- `message`
- `publicKey`
- `useCompression`

Returns:

- PNG image bytes containing the hidden payload

### `POST /api/extract`

Purpose:

- recover and decrypt the hidden message

Inputs:

- `image`
- `privateKey`

Returns:

- plaintext message
- encrypted size
- wrapped key length
- compression status

### `POST /api/capacity`

Purpose:

- estimate how much hidden data an image can hold

### `GET /api/health`

Purpose:

- confirm the service is running

## 14. Auth and chat backend concepts

This project also contains a small secure-sharing demo backend under `/api/auth`.

This is not a full production auth system. It is mainly a lightweight demo module.

### What it does

- registers users
- logs users in
- creates a simple session token
- stores users in memory
- stores secure image shares in memory
- lets a recipient decrypt a shared hidden message

### How user storage works

`AuthChatService` stores data in memory using `ConcurrentHashMap`.

That means:

- data is not stored in a database
- if the backend restarts, users, tokens, and shares are reset
- this is good for demo/testing
- this is not production persistence

### Password handling

Passwords are hashed with SHA-256 before storage.

That is better than plain text, but for a production app you would usually use a stronger password hashing approach like bcrypt, scrypt, or Argon2 with salt.

### Session handling

When a user logs in:

1. backend validates credentials
2. backend creates a random UUID token
3. token is stored in memory
4. later requests use `X-Auth-Token`

So this is token-based session management, but again only in memory.

## 15. Secure chat share flow

The chat module reuses the same crypto + steganography engine.

### Sending a secure share

`POST /api/auth/chat/send`

Flow:

1. sender is identified from `X-Auth-Token`
2. recipient public key is fetched from in-memory account data
3. backend builds a structured chat payload
4. payload includes sender, recipient, and message
5. payload may be compressed
6. payload is encrypted with recipient public key
7. encrypted data is hidden in uploaded image
8. resulting PNG is stored in memory as Base64

### Why the chat payload has metadata

The backend wraps the message in a custom text format like:

- version marker
- sender username
- recipient username
- blank line
- actual message

This allows the backend to confirm that:

- the payload belongs to the expected recipient
- the extraction mode is correct
- the wrong account is not being used silently

### Decrypting a secure share

`POST /api/auth/chat/decrypt/{shareId}`

Flow:

1. current user is validated from token
2. stored stego image bytes are loaded
3. recipient-specific seed is regenerated
4. encrypted payload is extracted
5. private key decrypts the message
6. parsed metadata is checked
7. decrypted message is returned and marked in memory

### Extracting from a shared image manually

`POST /api/auth/extract-shared-image`

This endpoint allows extraction using usernames from the seeded in-memory user accounts, instead of manually pasting PEM keys.

## 16. Why deterministic PRNG matters

One of the smartest backend concepts in this project is deterministic randomness.

The pixel order is random-looking, but not truly random in the uncontrolled sense.

It is:

- shuffled
- repeatable
- derived from key-linked seed material

That means embedding and extraction both generate the same pixel sequence without having to save that sequence anywhere.

This avoids storing extra mapping metadata in the image or server.

## 17. Error handling

There are two layers of error handling:

- controller-level try/catch blocks
- `GlobalExceptionHandler`

The backend usually returns JSON with:

- `success: false`
- a human-readable message
- timestamp

For extraction, some crypto and parsing errors are normalized into more user-friendly messages like:

- wrong keys
- wrong extraction mode
- corrupted image

This is useful because raw crypto exceptions are often confusing.

## 18. Configuration concepts

Configuration lives in:

- `backend/src/main/resources/application.properties`

Important settings include:

- server port
- multipart upload limits
- logging level
- exposed health/info endpoints
- allowed frontend origins for CORS

The port uses:

- `server.port=${PORT:8080}`

So it uses environment variable `PORT` if present, otherwise `8080`.

## 19. Design strengths

This backend has several good architectural ideas:

- clear controller/service/util separation
- hybrid encryption instead of RSA-only encryption
- AES-GCM for confidentiality plus integrity
- deterministic randomized LSB embedding
- validation before expensive processing
- automatic PNG output for data safety
- reusable crypto and image services
- chat module reuses the same core steganography pipeline

## 20. Current limitations

These are important to understand honestly.

- auth data is in memory only
- chat shares are in memory only
- password hashing is basic SHA-256, not production-grade password storage
- no database
- no real authorization roles
- no refresh-token/session-expiry design beyond current token map
- no rate limiting
- no audit logging or secure secret management layer
- header reserves 64 pixels but only 32 bits are actively used for payload length
- `GlobalExceptionHandler` says max upload is `50MB`, while app config and validation are centered around `12MB`, so the messaging is not fully aligned

## 21. End-to-end mental model

If you want one simple summary, think of the backend like this:

- Spring Boot exposes APIs
- controllers accept image + text + key input
- services prepare data and images
- crypto utilities encrypt the message safely
- steganography service hides encrypted bytes in image pixels
- extraction performs the exact reverse process
- auth/chat module is a demo layer built on the same engine

## 22. One-line summary of each core class

- `StegaCryptApplication`: starts the backend and configures CORS
- `SteganographyController`: general crypto-stego API endpoints
- `AuthChatController`: demo auth/chat endpoints
- `SteganographyService`: hides and extracts bits from pixels
- `ImageProcessingService`: reads, validates, converts, and writes images
- `CompressionService`: GZIP compress/decompress support
- `AuthChatService`: in-memory users, sessions, and secure shares
- `DemoUserService`: seeded demo identities with RSA keys
- `RSAUtil`: RSA key and key-wrapping utilities
- `AESUtil`: AES-GCM hybrid payload encryption/decryption
- `PRNGUtil`: deterministic randomized pixel order generation
- `BitUtil`: byte/bit/int conversion helpers
- `ValidationUtil`: message, key, image, and capacity checks
- `GlobalExceptionHandler`: shared JSON error responses

## 23. Best way to explain the backend in viva/interview style

You can describe it like this:

"The backend is a Spring Boot REST API that securely hides messages inside images. It first encrypts the message using hybrid cryptography, where AES encrypts the actual content and RSA protects the AES key. Then it uses LSB steganography with deterministic randomized pixel selection based on key-derived seed material to embed the encrypted payload into the image. The backend also includes validation, image processing, and a demo in-memory auth/chat module that reuses the same secure-sharing pipeline."

## 24. Final understanding

The most important concept is this:

The backend does not just hide plain text inside an image. It first turns the message into a strongly encrypted structured payload, and only then hides that payload inside carefully chosen pixel positions. That combination of encryption + steganography is the central idea of the whole backend.
