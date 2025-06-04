# 󰏢 public docker image tags


> [!NOTE]
> 
> - [python](https://hub.docker.com/_/python)을 예시로 든다.


## Quick Decision Matrix

### For Development

- **Unknown requirements**: `python:3.13`
- **Complex dependencies**: `python:3.13-bookworm`

### For Production

- **Most cases**: `python:3.13-slim-bookworm`
- **Simple apps**: `python:3.13-alpine`
- **Enterprise/reproducible**: `python:3.13-bookworm`

### For Special Cases

- **Windows environments**: `python:3.13-windowsservercore`
- **Extreme size constraints**: `python:3.13-alpine`
- **Legacy compatibility**: `python:3.13-bullseye`



## Comparison


### Default Tags (`python:3.13`)

**What it is:** Full Debian-based images across platforms

| pros                                | cons                                 | use case                         |
|-------------------------------------|--------------------------------------|----------------------------------|
| - Cross-platform compatibility      | - Largest image size (~900MB+)       | - Development/prototyping        |
| - All common packages included      | - Implicit platform choice           | - Complex dependencies           |
| - Beginner-friendly                 | - May change underlying OS over time | - Cross-platform tutorials       |
| - Most pip installs work out-of-box |                                      | - When unsure about requirements |



### Explicit Platform Tags (`python:3.13-bookworm`)

**What it is:** Simple tag for specific Debian version, predictable and explicit

  > - **bullseye** = Debian 11 (released 2021)
  > - **bookworm** = Debian 12 (released 2023, current stable)

| pros                           | cons                    | use case                       |
|--------------------------------|-------------------------|--------------------------------|
| - Reproducible builds          | - Still large size      | - Production deployments       |
| - Clear OS version             | - More verbose tag name | - CI/CD pipelines              |
| - Won't change unexpectedly    | - Platform-specific     | - When reproducibility matters |
| - All common packages included |                         | - Team collaboration           |


### Slim Tags (`python:3.13-slim-bookworm`)

**What it is:** Minimal Debian with only essential packages for Python


| pros | cons | use case |
|------|------|----------|
| - Smaller size (~50% reduction) |       - Some pip installs may fail | - Production with known dependencies |
|           - Faster pulls/builds | - Need manual package installation | - Space-constrained environments     |
|        - Reduced attack surface | - Compilation dependencies missing | - Microservices                      |
|            - Still Debian-based |                                    | - Simple Python apps                 |
  

### Alpine Tags (`python:3.13-alpine`)

**What it is:** Based on Alpine Linux, extremely minimal (~5MB base)

| pros                     | cons                             | use case                         |
|--------------------------|----------------------------------|----------------------------------|
| - Smallest possible size | - musl libc compatibility issues | - Microservices with simple deps |
| - Very fast pulls        | - Many pip packages fail         | - Size is critical priority      |
| - Minimal attack surface | - Missing common tools           | - Pure Python apps               |
| - Good for containers    | - Debugging harder               | - Container orchestration        |


### Windows Tags (`python:3.13-windowsservercore`)

**What it is:** Windows Server Core based images

| pros                           | cons                       | use case                     |
|--------------------------------|----------------------------|------------------------------|
| - Windows-native execution     | - Very large size (GB+)    | - Windows-only environments  |
| - Windows-specific libraries   | - Windows host required    | - .NET integration needs     |
| - Familiar Windows environment | - Limited tooling          | - Windows-specific libraries |
|                                | - Licensing considerations | - Legacy Windows apps        |



