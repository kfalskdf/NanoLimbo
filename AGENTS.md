# AGENTS.md - NanoLimbo

## Build
```bash
./gradlew build          # Compile and test
./gradlew shadowJar      # Produces build/libs/NanoLimbo-*.jar
./gradlew clean build    # Clean rebuild
```
Output JAR: `build/libs/NanoLimbo-*.jar`

## Test
JUnit 5 (Jupiter). Currently no tests exist in `src/test/`.
```bash
./gradlew test
./gradlew test --tests "ua.nanit.limbo.MyTestClass"         # Single class
./gradlew test --tests "ua.nanit.limbo.MyTestClass.method"   # Single method
./gradlew test --info    # Verbose output
```

## Key Classes
- `ua.nanit.limbo.NanoLimbo` - Entry point
- `ua.nanit.limbo.server.LimboServer` - Server initialization
- `ua.nanit.limbo.connection.ClientConnection` - Client handler
- `ua.nanit.limbo.protocol.ByteMessage` - Wraps Netty ByteBuf; use for all packet read/write
- `ua.nanit.limbo.protocol.PacketSnapshot` - Pre-built packet cache; check before creating new packets
- `ua.nanit.limbo.configuration.LimboConfig` - Main config (settings.yml)
- `ua.nanit.limbo.protocol.registry.Version` - Minecraft versions; use `Version.moreOrEqual()` / `Version.less()` for version-specific behavior

## Protocol Rules
- Use `readVarInt()` / `writeVarInt()` for Minecraft protocol integers
- Always check `isConnected()` before sending packets
- Packets extend `PacketIn` or `PacketOut`

## Dependencies
Netty 4.1.101, Configurate YAML, Adventure NBT, NanoJSON, Gson, Logback

## CI Build
- CI uses **Java 17** to build even though source targets Java 8
- Build command: `./gradlew clean shadowJar -P...` with many `-P` project properties
- JAR is uploaded to GitHub Releases automatically on push to main

## License Header
Required for all source files:
```java
/*
 * Copyright (C) 2020 Nan1t
 * [GPL v3 license text]
 */
```

## Notes
- `ByteMessage` extends Netty's `ByteBuf` - delegate pattern, not inheritance
- `shadowJar` minimization explicitly excludes logback; don't change this
- The project has extensive environment variable support for cloud deployment (see NanoLimbo.java lines 38-44)