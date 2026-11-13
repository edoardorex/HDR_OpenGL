# HDR and Tone Mapping in OpenGL

An interactive computer-graphics coursework scene that compares standard
display-range rendering with **Reinhard** and **exposure-based** tone mapping.
A bright central object illuminates a textured room, making highlight clipping
and tone-mapping differences easy to inspect.

## Rendering pipeline

The scene is rendered to an `RGBA16F` floating-point framebuffer so lighting
values can exceed `1.0`. A fullscreen pass maps those values into display range:

```glsl
// Reinhard
result = hdrColor / (hdrColor + vec3(1.0));

// Exposure
result = vec3(1.0) - exp(-hdrColor * exposure);

// Gamma correction
result = pow(result, vec3(1.0 / 2.2));
```

See [hdr.cpp](src/OpenGL/HDR/hdr.cpp) for the scene and
[hdr.fs](src/OpenGL/HDR/hdr.fs) for the tone-mapping implementation.

## Requirements and build

- A graphics driver supporting **OpenGL 3.3 core**.
- A C++11-capable compiler and compatible CMake installation.
- GLFW, GLM, and Assimp discoverable by the supplied CMake modules.
- The additional libraries linked by your platform branch in
  [CMakeLists.txt](CMakeLists.txt), including FreeType and, on Windows, irrKlang.
- The included GLAD/stb_image sources and the repository's textures and shaders.

Use the official [GLM project](https://github.com/g-truc/glm) for missing GLM
headers and configure their include path. The repository contains
platform-specific libraries; these are not portable across operating systems,
architectures, or compiler ABIs.

The original build uses CMake 2.8 policy settings. For the commands below, use a
compatible **CMake 3.x version, 3.13 or newer**. CMake 4 may require updating the
legacy policy declarations before configuration succeeds.

```sh
cmake -S . -B build
cmake --build build --target OpenGL__HDR
```

Launch the executable from its generated `bin/OpenGL` directory, or the
configuration subdirectory used by your generator. Keep the copied shaders in
the working directory and the original texture tree available. These relative
shader paths are required at runtime.

## Controls

| Input | Action |
| --- | --- |
| `W`, `A`, `S`, `D` | Move the camera |
| Mouse | Look around |
| Scroll | Zoom |
| `Space` | Toggle HDR |
| `M` | Switch Reinhard/exposure tone mapping |
| `Q`, `E` | Decrease/increase exposure |
| `Esc` | Exit |

Exposure changes affect the exposure-based method, not the Reinhard formula.
The program prints current HDR, exposure, and method settings to the console.

## Original visual examples

![Animated scene demonstration](https://media.giphy.com/media/yp70JVbU0uIpFor3AU/giphy.gif)

| HDR off | Reinhard | Exposure 0.033 |
| --- | --- | --- |
| ![HDR disabled](screenshot/off.png) | ![Reinhard mapping](screenshot/reinhard.png) | ![Exposure 0.033](screenshot/exp033.png) |
| ![HDR disabled, second view](screenshot/off%20%282%29.png) | ![Reinhard, second view](screenshot/reinhard%20%282%29.png) | ![Exposure 0.033, second view](screenshot/exp033%20%282%29.png) |

![Exposure 0.008](screenshot/exp008.png)

## Project provenance

This repository is a fork of
[edoardore/HDR_OpenGL](https://github.com/edoardore/HDR_OpenGL).
The original scene, examples, and history are preserved. Retain the notices
and licenses of bundled third-party graphics components when redistributing.
