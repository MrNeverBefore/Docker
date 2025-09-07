# [Home](READEME.md)
# Multi-stage Docker Builds

Multi-stage Docker builds are a powerful feature that allows you to create more efficient and secure Docker images by separating the build environment from the runtime environment. This technique significantly reduces the final image size and enhances security by eliminating unnecessary tools and dependencies required only during the build process.

### The Problem Multi-stage Builds Solve

Traditionally, when building a Docker image, all the dependencies and tools required to compile or package an application (e.g., compilers, SDKs, development libraries) would be included in the same image that runs the application. For example, a Python application might require `apt` and `pip` for installation, but once built, only the Python runtime and the application binary are needed to execute it. This leads to:

*   **Large Image Sizes**: The final Docker image contains many unnecessary components, such as build tools, temporary files, and development dependencies, making the image much larger than required.
*   **Increased Attack Surface**: More software and libraries in an image mean more potential vulnerabilities, as these components might have security flaws that are not relevant to the application's runtime.

### How Multi-stage Builds Work

Multi-stage builds address these issues by allowing you to define multiple `FROM` instructions in a single Dockerfile, where each `FROM` instruction starts a new build stage. Each stage can selectively copy artefacts from previous stages, ensuring that only the essential components make it into the final image.

*   **Build Stage**: This initial stage uses a "richer" base image (e.g., Ubuntu with a full Go SDK or Java JDK) that contains all the necessary tools and dependencies to build your application.
*   **Final Stage**: This stage typically uses a "minimalistic" or "destroyless" base image (e.g., `scratch`, `alpine`, or a specific runtime image like `python-slim`) and copies only the compiled application binary or essential runtime files from the build stage. This dramatically reduces the final image size and the attack surface.

### Code Example: Multi-stage Build for a Go Application

The following example demonstrates a multi-stage build for a Go calculator application. Go applications are particularly good for showcasing this because they can be statically compiled into a single binary that doesn't even require a Go runtime in the final image, allowing for a "destroyless" final stage using `scratch`.

**`Dockerfile` (Multi-stage for Go application)**

```dockerfile
# Stage 1: Build the application (Build Stage)
FROM ubuntu:latest AS builder #

# Install Go language (not explicitly mentioned in all sources as 'install go', but implied for Go app build)
# Note: In a real-world scenario, you might use a 'golang' specific base image for the builder stage
RUN apt-get update && apt-get install -y golang-go # This installs Go on the Ubuntu base image

WORKDIR /app #

COPY calculator.go . # Copies the Go source code into the build stage

RUN go build -o calculator # Builds the Go binary, naming it 'calculator'

# Stage 2: Create the final, minimal image (Final Stage)
FROM scratch # This is a "destroyless" base image, meaning it's an empty image.

WORKDIR /app #

COPY --from=builder /app/calculator . # Copies only the compiled binary from the 'builder' stage

ENTRYPOINT ["./calculator"] # Defines the command to run when the container starts.
```

To build and run this Dockerfile:

```bash
docker build -t simple-calculator-multi-stage .
docker run simple-calculator-multi-stage
```
When running the `simple-calculator-multi-stage` container, it will prompt for inputs, for example:
`Provide the inputs for the calculation (e.g., "5 + 3"): `

### Bullet Point Notes for the Code Example:

*   **`FROM ubuntu:latest AS builder`**:
    *   This is the **first stage**, named `builder`.
    *   It uses a `ubuntu:latest` base image, which is a relatively large but feature-rich image, suitable for installing build tools and compiling code. The `AS builder` assigns a name to this stage, allowing later stages to refer to it.
*   **`RUN apt-get update && apt-get install -y golang-go`**:
    *   Installs the Go compiler and tools within the `builder` stage. These tools are necessary for building the Go application but are not needed for running it.
*   **`WORKDIR /app`**:
    *   Sets the working directory inside the container for subsequent instructions in this stage.
*   **`COPY calculator.go .`**:
    *   Copies the application's source code (`calculator.go`) into the `/app` directory within the `builder` container.
*   **`RUN go build -o calculator`**:
    *   Compiles the Go application and creates an executable binary named `calculator` in the `/app` directory. This is the artefact we want to keep.
*   **`FROM scratch`**:
    *   This is the **second and final stage**.
    *   `scratch` is an **empty base image**, meaning it contains literally nothing. It's the most minimalistic "destroyless" image available. It's ideal for statically compiled binaries like Go, which don't require an operating system or runtime dependencies to execute.
*   **`WORKDIR /app`**:
    *   Sets the working directory for the final container.
*   **`COPY --from=builder /app/calculator .`**:
    *   This is the key instruction for multi-stage builds. It **copies only the compiled `calculator` binary** from the `builder` stage (specifically from `/app/calculator` within the `builder` stage) to the current working directory (`/app`) in the final `scratch` image.
    *   All the build tools and the `ubuntu:latest` base image from the `builder` stage are left behind, not included in the final image.
*   **`ENTRYPOINT ["./calculator"]`**:
    *   Specifies the command that will be executed when a container is started from this final image. In this case, it runs the compiled `calculator` binary.

**Benefits Demonstrated by this Example:**

*   **Significant Size Reduction**: As mentioned in the sources, a non-multi-stage Go application image could be around 861 MB, while a multi-stage build using `scratch` can reduce it drastically to merely **1.83 MB**. This is an approximate **800-fold reduction**.
*   **Enhanced Security**: By using `scratch` as the final image, the container has minimal software, practically eliminating operating system-level vulnerabilities. This makes the application less exposed to potential threats.

Multi-stage builds are considered the future of containers due to their ability to create smaller, faster, and more secure images, which is crucial for modern DevOps practices and cloud-native development.
