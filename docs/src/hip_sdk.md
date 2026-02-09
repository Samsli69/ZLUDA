# Installing HIP SDK

On Windows, in addition to installing the GPU driver, you need to install the HIP SDK. Choose one of the two options below:

<table width="100%">
<tr >
<th> Official HIP SDK </th>
<th> Unofficial HIP SDK builds </th>
</tr>
<tr/>
<tr>
<td style="width: 50%;border:none;vertical-align: top">

✅ Automatic installation\
✅ Stable and supported by AMD\
❌ Older code version\
❌ No Machine Learning support (PyTorch and TensorFlow will not work)

### Installation

1. Visit the [AMD HIP SDK for Windows](https://www.amd.com/en/developer/resources/rocm-hub/hip-sdk.html) website
2. Download the latest version for your OS
3. Run the installer and follow the on-screen instructions

</td>
<td style="width: 50%;border:none;vertical-align: top">

❌ Manual installation\
❌ Unstable and unsupported by AMD\
✅ Freshest possible code version\
✅ Machine Learning support (required for PyTorch, TensorFlow, etc.)

### Installation

1. Visit the [ROCm SDK nightly tarballs](https://therock-nightly-tarball.s3.amazonaws.com/index.html) website
2. Download a recent `therock-dist-windows-gfx<GPUARCH>...tar.gz` file:
   - If you don't know your `<GPUARCH>`, check the TechPowerUp GPU database. Example: for [AMD Radeon RX 9070](https://www.techpowerup.com/gpu-specs/radeon-rx-9070.c4250), the `<GPUARCH>` (Shader ISA) is 1201
   - Alternatively, download any `tar.gz` file, unpack it and run the included `hipInfo.exe` to determine your `<GPUARCH>` (gcnArchName)
3. Extract the `.tar.gz` file to a directory of your choice (you can use [7-Zip](https://www.7-zip.org/))
   - Note: You may need to extract twice (first the `.tar.gz`, then the `.tar` file)
4. Set the `HIP_PATH` environment variable to the extracted directory path ([instructions here](https://www.tenforums.com/tutorials/121855-edit-user-system-environment-variables-windows.html))
   - The directory must contain a `bin` subdirectory
   - The `bin` directory must contain various `.dll` files, including `rocblas.dll`

</td>
</tr>
</table>

## Verification

ZLUDA includes a small CUDA application that tests the loading and initialization of all performance libraries. Run it using:

```
zluda.exe -- cuda_check.exe
```

The output should look like this:

```
cufft11   : OK
cublas12  : OK
cublas13  : OK
nvcuda    : OK
nvml      : OK
cudnn8    : OK
cudnn9    : OK
cublaslt13: OK
cusparse11: OK
cusparse12: OK
cublaslt12: OK
cufft12   : OK
```

**Known Issues:**
- `cuda_check.exe` may sometimes hang and not close due to a bug in MIOpen
- When using the official HIP SDK, `cuda_check.exe` will fail to load `cudnn8` and `cudnn9` because the official SDK does not include MIOpen

