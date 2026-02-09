# Installing HIP SDK

On Windows, on top of installing GPU driver you need to install HIP SDK. You need to choose one of the two options:

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
❌ Older code\
❌ No support for Machine Learning (PyTorch, Tensorflow won't work)

### Installation

Go to [AMD HIP SDK for Windows](https://www.amd.com/en/developer/resources/rocm-hub/hip-sdk.html) website, download the newest version for your OS, run it and follow instructions

</td>
<td style="width: 50%;border:none;vertical-align: top">

❌ Manual installation\
❌ Unstable and unsupported by AMD\
✅ More recent code\
✅ Supports Machine Learning (required for PyTorch, Tensorflow, etc.)

### Installation

* Go to [ROCm SDK nightly tarballs](https://therock-nightly-tarball.s3.amazonaws.com/index.html) website, then download a recent `therock-dist-windows-gfx<GPUARCH>...tar.gz`. If you don't know what your `<GPUARCH>` is, you can check in techpowerup database: e.g. for [AMD Radeon RX 9070](https://www.techpowerup.com/gpu-specs/radeon-rx-9070.c4250) `<GPUARCH>` (Shader ISA) is 1201. Alternatively you can download any `tar.gz` and run contained `hipInfo.exe` to get the your `<GPUARCH>` (gcnArchName)
* Unpack the content of `.tar.gz` to a directory of your choice. You can use [7-Zip](https://www.7-zip.org/) for that. You might have to unpack twice, first `.tar.gz` file and then `.tar` file
* Set environment variable `HIP_PATH` to the path to the newly unpacked directory (See instructions [here](https://www.tenforums.com/tutorials/121855-edit-user-system-environment-variables-windows.html)). The directory must contain directory "bin". "bin" directory must contain various .dll files including "rocblas.dll". 

</td>
</tr>
</table>

## Verification

ZLUDA ships with a small CUDA application that tries to load and initialize all performance libraries.
You use it like this:
```
zluda.exe -- cuda_check.exe
```

Output should look like this:
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

`cuda_check.exe` sometimes hangs and does not close due to a bug in MIOpen. `cuda_check.exe` will fail when loading  `cudnn8` and `cudnn9` with official HIP SDK, because official HIP SDK does not ship MIOpen

