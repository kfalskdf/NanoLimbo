# AGENTS.md - NanoLimbo Development Guide

## Project Overview

NanoLimbo is a Minecraft limbo server implementation built with Java and Netty. It acts as a proxy that accepts Minecraft client connections and sends them directly to the game state without an actual world.

**Main class:** `ua.nanit.limbo.NanoLimbo`
**Package:** `ua.nanit.limbo`
**Java target:** 1.8

---

## Build Commands

### Gradle Wrapper
```bash
./gradlew build
```

### Build JAR (Shadow)
```bash
./gradlew shadowJar
```
Output: `build/libs/NanoLimbo-*.jar`

### Clean Build
```bash
./gradlew clean build
```

### Run
```bash
java -jar build/libs/NanoLimbo-*.jar
```

---

## Test Commands

Tests use JUnit 5 (Jupiter).

### Run All Tests
```bash
./gradlew test
```

### Run Single Test Class
```bash
./gradlew test --tests "ua.nanit.limbo.MyTestClass"
```

### Run Single Test Method
```bash
./gradlew test --tests "ua.nanit.limbo.MyTestClass.myTestMethod"
```

### Run Tests with Verbose Output
```bash
./gradlew test --info
```

---

## Code Style Guidelines

### File Header
All source files must include the GPL v3 license header:
```java
/*
 * Copyright (C) 2020 Nan1t
 *
 * This program is free software: you can redistribute it and/or modify
 * it under the terms of the GNU General Public License as published by
 * the Free Software Foundation, either version 3 of the License, or
 * (at your option) any later version.
 *
 * This program is distributed in the hope that it will be useful,
 * but WITHOUT ANY WARRANTY; without even the implied warranty of
 * MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
 * GNU General Public License for more details.
 *
 * You should have received a copy of the GNU General Public License
 * along with this program.  If not, see <https://www.gnu.org/licenses/>.
 */

package ua.nanit.limbo;
```

### Indentation
- Use 4 spaces for indentation (no tabs)
- Align method chains when appropriate

### Naming Conventions
| Element | Convention | Example |
|---------|------------|---------|
| Classes | PascalCase | `ClientConnection`, `PacketDecoder` |
| Methods | camelCase | `sendPacket()`, `handlePacket()` |
| Variables | camelCase | `clientVersion`, `velocityLoginMessageId` |
| Constants | UPPER_SNAKE_CASE | `ANSI_GREEN`, `LIMBO_VERSION` |
| Packages | lowercase | `ua.nanit.limbo.protocol` |

### Import Organization
Use explicit imports (avoid wildcard `.*` except for:
- `java.util.*` for common collections when heavily used
- Group by: static, then java, then external (netty, etc.)

### Annotations
- Use JetBrains `@NotNull` and `@Nullable` for null analysis
- Place annotations before type, e.g., `@NotNull String name`

### Error Handling
- Use `Log.error()` for logging errors with exceptions
- Throw appropriate exceptions: `IllegalArgumentException` for bad input, `IllegalStateException` for invalid state
- Never silently catch exceptions without logging

### Method Design
- Keep methods focused and single-purpose
- Use meaningful parameter names
- Document complex logic with comments
- Prefer early returns over deeply nested conditionals

### Class Structure
```java
// 1. Package declaration
// 2. License header
// 3. Imports (grouped: static, java, external)
// 4. Class declaration with Javadoc
// 5. Static fields
// 6. Instance fields
// 7. Constructor(s)
// 8. Public methods
// 9. Private methods
```

### Protocol Code Special Guidelines
- `ByteMessage` wraps Netty's `ByteBuf` - use it for all packet read/write
- Use `readVarInt()` / `writeVarInt()` for Minecraft protocol integers
- Always check `isConnected()` before sending packets
- Packet classes should extend `PacketIn` or `PacketOut`

---

## Key Dependencies

| Library | Purpose |
|---------|---------|
| Netty 4.1.101 | Async networking |
| Configurate YAML | Configuration parsing |
| Adventure NBT | NBT tag handling |
| NanoJSON | JSON parsing |
| Gson | JSON serialization |
| Logback | Logging implementation |

---

## Important Classes

### Core
- `ua.nanit.limbo.NanoLimbo` - Entry point
- `ua.nanit.limbo.server.LimboServer` - Server initialization
- `ua.nanit.limbo.connection.ClientConnection` - Client handler

### Protocol
- `ua.nanit.limbo.protocol.ByteMessage` - Buffer wrapper
- `ua.nanit.limbo.protocol.Packet` - Base packet interface
- `ua.nanit.limbo.protocol.PacketSnapshot` - Pre-built packet cache

### Configuration
- `ua.nanit.limbo.configuration.LimboConfig` - Main config class

---

## Testing Strategy

- Place tests in `src/test/java/` following package structure
- Test naming: `*Test.java` or `*Tests.java`
- Use `@Test` annotation from JUnit Jupiter
- Use `@BeforeEach` / `@AfterEach` for setup/teardown

---

## Debugging

### Enable Debug Logging
```java
Log.setLevel(1);
```

### Common Issues
- Check `Version` enum for Minecraft version compatibility
- Verify port availability in config
- Ensure Java 8+ is used (check with `java -version`)

---

## Notes for Agents

1. Before modifying protocol packets, check `PacketSnapshots` for pre-built versions
2. When adding new packets, consider version-specific behavior using `Version.moreOrEqual()` / `less()`
3. Test against multiple Minecraft versions if possible
4. The project uses shadow JAR for distribution - ensure all dependencies are included