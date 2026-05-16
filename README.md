# ONNX Runtime Native ARM64 Builds

Native ARM64 builds of ONNX Runtime for BirdNET-Go, built on GitHub's ARM64 runners.

## Why native builds?

Cross-compiled ORT on ARM64 has a critical issue: CMake's FP16 capability detection tests can't execute on the x86 build host, so the FP16 MLAS runtime path gets disabled. The graph optimizer then inserts `Cast(fp16->fp32)` nodes instead of routing to native FP16 MatMul (`MlasHalfGemmBatch`), making FP16 models 15-42% *slower* than FP32.

Building natively on ARM64 fixes this by allowing the compile-time detection to run correctly.

Reference: [microsoft/onnxruntime#25824](https://github.com/microsoft/onnxruntime/issues/25824)

## Build variants

| Variant | EPs included | Use case |
|---------|-------------|----------|
| `native` | CPU | Standard FP16-capable build |
| `native-xnnpack` | CPU + XNNPACK | Additional XNNPACK EP for single-threaded workloads |

## Usage

Download the latest release, extract, and copy to your system:

```bash
tar xzf onnxruntime-1.25.1-linux-aarch64-native.tar.gz
sudo cp onnxruntime-*/lib/libonnxruntime.so.* /usr/lib/
sudo ldconfig
```

## Triggering a build

The build is triggered via `workflow_dispatch` with parameters:
- `ort_version`: ORT version tag (default: `v1.25.1`)
- `build_xnnpack`: Include XNNPACK EP (default: true)
