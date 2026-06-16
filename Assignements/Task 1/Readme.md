# Task 1 build instructions

# Windows-first build guide

## Prerequisites

- Install "Build Tools for Visual Studio (community edition)" (select "C++ build tools"): https://visualstudio.microsoft.com/downloads/
- Install CMake: https://cmake.org/download/
- Install Git: https://git-scm.com/download/win

## Quick overview

- This repo includes OpenCV as a submodule in `opencv/`. You can build OpenCV locally (recommended) or point CMake to a prebuilt OpenCV.

Steps (copy-paste into an Administrator PowerShell or Developer PowerShell)

### 1. Get the code and submodules

```powershell
git clone --recursive https://github.com/BlackCatTB/opencv_c--/
cd opencv_c--/
# if you already cloned without --recursive
git submodule update --init --recursive
```

### 2. Build the OpenCV submodule (recommended)

```powershell
cd opencv
mkdir build
cmake -S . -B build -A x64 -D CMAKE_BUILD_TYPE=Release
cmake --build build --config Release -- /m
cmake --install build --config Release
cd ..
```

Notes: The project's top-level CMakeLists sets `OpenCV_DIR` to `${CMAKE_SOURCE_DIR}/opencv/build`, so building OpenCV into `opencv/build` is the expected setup.

### 3. Configure and build this project

From the repo root:

```powershell
cmake -S . -B build -A x64 -D CMAKE_BUILD_TYPE=Release
cmake --build build --config Release -- /m
```

### 4. Run the binaries

Binaries are in `build\Release` (e.g. `main.exe`, `list_cameras.exe`). If you see missing DLL errors, copy OpenCV's bin folder into the same directory, for example:

```powershell
Copy-Item -Recurse opencv\build\bin\Release\* build\Release\
```

Quick alternative: using Visual Studio GUI

- Open the generated solution at `build\OpenCV.sln` or the top-level solution created by CMake in `build` with Visual Studio, then build `Release` x64.

Provided helper script (Windows PowerShell)

- Use the included script [scripts/build_windows.ps1](scripts/build_windows.ps1) to automate steps 1–3. Example:

```powershell
.\scripts\build_windows.ps1 -Configuration Release -Arch x64
```

## Troubleshooting

- If `cmake` or `git` are not recognized, reopen your terminal after installing, or use the Visual Studio "Developer PowerShell" which sets paths for you.
- PowerShell blocked the script? In an Administrator PowerShell you may need to allow local scripts to run:

```powershell
Set-ExecutionPolicy RemoteSigned -Scope LocalMachine
```

Run that only in an elevated (Admin) PowerShell. This allows locally created scripts to run while still requiring signatures for remote scripts.

- If building OpenCV fails, check CMake output for missing dependencies; building OpenCV can take many minutes.

## macOS

- Install Xcode Command Line Tools:

```bash
xcode-select --install
```

- Install Homebrew (if needed): https://brew.sh/
- Install required tools:

```bash
brew install cmake git
```

- Option A — build the bundled OpenCV submodule (recommended):

```bash
cd opencv
mkdir build
cmake -S . -B build -D CMAKE_BUILD_TYPE=Release
cmake --build build --config Release
cmake --install build --config Release
cd ..
```

- Option B — use Homebrew OpenCV and tell CMake where it is:

```bash
brew install opencv
cmake -S . -B build -D OpenCV_DIR=$(brew --prefix opencv)/lib/cmake/opencv4
cmake --build build
```

## Linux (Debian/Ubuntu)

- Install build dependencies (example for Ubuntu/Debian):

```bash
sudo apt update
sudo apt install -y build-essential cmake git pkg-config libgtk-3-dev \
	libavcodec-dev libavformat-dev libswscale-dev libv4l-dev libjpeg-dev \
	libpng-dev libtiff-dev libopenexr-dev libtbb2 libtbb-dev libdc1394-22-dev \
	gfortran
```

- Build the OpenCV submodule (recommended):

```bash
cd opencv
mkdir build
cmake -S . -B build -D CMAKE_BUILD_TYPE=Release
cmake --build build -j$(nproc)
sudo cmake --install build
cd ..
```

- Then configure and build the top-level project as shown earlier:

```bash
cmake -S . -B build -D CMAKE_BUILD_TYPE=Release
cmake --build build -j$(nproc)
```

Want help? Ask Cas

When completing Task 1 you will need to calibrate your camera. You can do this like [this](https://youtu.be/3h7wgR5fYik). You will get a camera distortion offset matrix which you will need to use for every interaction with openCV and your Camera. These Offsets are individual for each camera, don’t mix them up! 