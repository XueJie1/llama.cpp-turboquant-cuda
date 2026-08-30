# llama.cpp-turboquant CUDA Builds

This repository automatically builds [llama.cpp-turboquant](https://github.com/TheTom/llama-cpp-turboquant) (the TurboQuant fork of llama.cpp) with CUDA support for Linux x86_64, and publishes ready-to-use binaries via GitHub Releases.

## Why This Repository?

The llama.cpp-turboquant project only ships macOS (Metal) and Windows (CUDA) binaries in its own releases - there is no Linux CUDA build. This repository fills that gap by:

- Building llama.cpp-turboquant with CUDA support for a wide range of NVIDIA GPU architectures (compute capability 7.5+)
- Automatically tracking the `feature/turboquant-kv-cache` branch of the upstream fork
- Providing ready-to-use Linux x86_64 binaries via GitHub Releases

## Supported Configurations

### CUDA Version
- CUDA 12.8.1

### Host CPU Architecture

Each release publishes a single Linux x86_64 tarball (suffix `-amd64`).

### GPU Architectures

The build targets the following GPU compute capabilities (same on the host):

| Compute Capability | GPU Examples |
|-------------------|--------------|
| 7.5 | Tesla T4, RTX 2000 series, Quadro RTX |
| 8.0 | A100 |
| 8.6 | RTX 3000 series |
| 8.9 | RTX 4000 series, L4, L40 |
| 9.0 | H100, H200, GH200 |
| 10.0 | B200, GB200 |
| 12.0 | RTX Pro series, RTX 5000 series |

## Usage

### Download

1. Go to the [Releases](../../releases) page
2. Download the `-amd64` tarball. Filename format: `llama.cpp-turboquant-tqp-cuda-YYYYMMDD-<shortsha>-cuda-12.8-amd64.tar.gz`
3. Extract the archive:

```bash
tar -xzf llama.cpp-turboquant-tqp-cuda-20260620-4595fff-cuda-12.8-amd64.tar.gz
cd cuda-12.8
```

### Run

The extracted directory contains all llama.cpp binaries:

```bash
# Run the main CLI
./llama-cli --help

# Run the server
./llama-server --help

# Other utilities
./llama-bench
./llama-quantize
./llama-embedding
```

### Check Version

Each release includes a `VERSION.txt` file recording the exact source commit, branch, and build date:

```bash
cat VERSION.txt
```

## System Requirements

- NVIDIA GPU with compute capability 7.5 or higher
- NVIDIA driver supporting CUDA 12.8 (>= 570.15)
- Linux x86_64 (Ubuntu 22.04 compatible)

## Build Process

Builds are triggered automatically:
- Weekly (every Monday at 03:17 UTC), but only if a new commit has landed on the tracked branch
- Identical commits are not rebuilt (the tag carries the short commit SHA for dedup)
- Can be manually triggered via GitHub Actions, with a **Force build** option to override dedup

Each build:
1. Resolves the tip commit of `feature/turboquant-kv-cache` via the GitHub API
2. Shallow-clones llama.cpp-turboquant at that exact commit
3. Builds with CMake + Ninja inside the `nvidia/cuda:12.8.1-cudnn-devel-ubuntu22.04` Docker image
4. Packages the binaries into a single `-amd64` tarball
5. Creates a GitHub release tagged `tqp-cuda-<YYYYMMDD>-<shortsha>`

Release tag uses the compile date plus the short source commit SHA, so each release is both sortable by date and traceable to an exact upstream commit.

## Manual Building / Customization

To customize (e.g. CUDA version, compute capabilities, add other host architectures), edit `.github/workflows/build-cuda.yml`, then trigger a manual workflow run from the Actions tab. The source repo, branch, and CUDA settings are all defined in the `env` block at the top of the workflow for easy editing.

## License

This repository contains build scripts only. The llama.cpp-turboquant binaries are subject to the [llama.cpp MIT License](https://github.com/TheTom/llama-cpp-turboquant/blob/feature/turboquant-kv-cache/LICENSE).

## Links

- **Upstream llama.cpp-turboquant**: https://github.com/TheTom/llama-cpp-turboquant
- **CUDA Toolkit**: https://developer.nvidia.com/cuda-toolkit
- **NVIDIA Driver Downloads**: https://www.nvidia.com/download/index.aspx

## Support

For issues with:
- **Build process or binaries**: Open an issue in this repository
- **llama.cpp-turboquant functionality**: Open an issue in the [upstream fork](https://github.com/TheTom/llama-cpp-turboquant/issues)

## Credits

- [llama.cpp](https://github.com/ggml-org/llama.cpp) by Georgi Gerganov and contributors
- [llama.cpp-turboquant](https://github.com/TheTom/llama-cpp-turboquant) TurboQuant fork
- Build workflow adapted from [ai-dock/llama.cpp-cuda](https://github.com/ai-dock/llama.cpp-cuda)
