# Darling Build Guide (Detailed)

Complete build instructions for macOS_emu (Darling) on Ubuntu/Debian systems.

---

## Prerequisites Check

Before building, verify your system:

```bash
# Check Ubuntu version (22.04+ recommended)
lsb_release -a

# Check architecture (must be x86_64)
uname -m

# Check available disk space (need ~10GB)
df -h ~

# Check RAM (4GB+ recommended)
free -h
```

---

## Step 1: Install Build Dependencies

### Essential Build Tools

```bash
sudo apt update
sudo apt install -y \
  build-essential \
  cmake \
  clang \
  bison \
  flex \
  git \
  git-lfs
```

### Development Libraries

```bash
sudo apt install -y \
  libc6-dev-i386 \
  gcc-multilib \
  linux-headers-generic \
  libcap2-bin \
  pkg-config \
  xz-utils
```

### Graphics & UI Libraries (for GUI support)

```bash
sudo apt install -y \
  libfuse-dev \
  libudev-dev \
  libcairo2-dev \
  libgl1-mesa-dev \
  libglu1-mesa-dev \
  libegl1-mesa-dev \
  libtiff5-dev \
  libfreetype6-dev \
  libfontconfig1-dev \
  libxml2-dev \
  libelf-dev \
  libbsd-dev
```

### Initialize Git LFS

```bash
git lfs install
```

---

## Step 2: Clone Repository

### If starting fresh:

```bash
mkdir -p ~/DevEnv
cd ~/DevEnv
git clone --recursive https://github.com/CynicalWilson/macOS_emu.git
cd macOS_emu
```

### If already cloned (update submodules):

```bash
cd ~/DevEnv/macOS_emu
git submodule update --init --recursive
```

**Note:** This downloads 149 submodules (~5GB). Takes 5-15 minutes depending on connection.

---

## Step 3: Configure Build

```bash
cd ~/DevEnv/macOS_emu

# Create build directory
mkdir -p build
cd build

# Configure with CMake (Release build)
cmake .. \
  -DCMAKE_BUILD_TYPE=Release \
  -DCMAKE_INSTALL_PREFIX=/usr/local

# Alternative: Debug build (for development)
# cmake .. -DCMAKE_BUILD_TYPE=Debug
```

**Configuration options:**
- `-DCMAKE_BUILD_TYPE=Release` - Optimized for performance
- `-DCMAKE_BUILD_TYPE=Debug` - Includes debug symbols
- `-DCMAKE_INSTALL_PREFIX=/usr/local` - Installation directory

---

## Step 4: Build Darling

```bash
# Build using all CPU cores
make -j$(nproc)
```

**Build time estimates:**
- 4-core CPU: ~45-60 minutes
- 8-core CPU: ~25-35 minutes
- 16-core CPU: ~15-20 minutes

**Monitor progress:**
```bash
# In another terminal
watch -n 5 'ps aux | grep make'
```

**Common build errors:**

### "Out of memory"
Reduce parallel jobs:
```bash
make -j2  # Use only 2 cores
```

### "Missing header files"
Reinstall dev packages:
```bash
sudo apt install --reinstall linux-headers-$(uname -r)
```

---

## Step 5: Install System-Wide

```bash
cd ~/DevEnv/macOS_emu/build
sudo make install
```

This installs:
- `/usr/local/bin/darling` - Main executable
- `/usr/local/libexec/darling/` - Runtime libraries
- Kernel modules for `/lib/modules/`

### Verify installation:

```bash
which darling
darling --version
```

---

## Step 6: Load Kernel Module

### Manual load (current session):

```bash
sudo modprobe darling-mach
```

### Verify module loaded:

```bash
lsmod | grep darling
```

### Auto-load on boot (optional):

```bash
echo "darling-mach" | sudo tee /etc/modules-load.d/darling.conf
```

---

## Step 7: Test Installation

### Start Darling shell:

```bash
darling shell
```

You should see a macOS-like prompt:
```
Bootstrapping the container, this will take a minute...
[...]
darling$
```

### Run basic commands:

```bash
# Inside Darling shell
uname -a        # Should show "Darwin"
sw_vers         # macOS version info
ls /System      # Browse macOS filesystem
```

### Exit Darling shell:

```bash
exit
```

---

## Post-Build Configuration

### Add to PATH (if not installed system-wide):

```bash
echo 'export PATH="$HOME/DevEnv/macOS_emu/build/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

### Set up Darling prefix (user environment):

```bash
# Darling uses ~/.darling by default
# You can customize:
export DARLING_PREFIX="$HOME/.darling-test"
```

---

## Updating Darling

### Pull latest changes:

```bash
cd ~/DevEnv/macOS_emu
git pull origin master
git submodule update --init --recursive
```

### Rebuild:

```bash
cd build
make clean
cmake .. -DCMAKE_BUILD_TYPE=Release
make -j$(nproc)
sudo make install
```

---

## Troubleshooting Build Issues

### CMake configuration fails

**Check dependencies:**
```bash
sudo apt install --reinstall cmake clang
```

### Submodule checkout fails

**Re-sync submodules:**
```bash
git submodule sync
git submodule update --init --recursive --force
```

### Kernel module won't load

**Check kernel headers match running kernel:**
```bash
uname -r
dpkg -l | grep linux-headers
```

**Reinstall matching headers:**
```bash
sudo apt install linux-headers-$(uname -r)
```

### Build runs out of disk space

**Check available space:**
```bash
df -h ~/DevEnv/macOS_emu
```

**Clean build artifacts:**
```bash
cd ~/DevEnv/macOS_emu/build
make clean
```

---

## Alternative: Local Build (No Install)

If you prefer not to install system-wide:

```bash
# Build only
make -j$(nproc)

# Run directly from build directory
./bin/darling shell
```

---

## Performance Tuning

### Optimize build for your CPU:

```bash
cmake .. \
  -DCMAKE_BUILD_TYPE=Release \
  -DCMAKE_C_FLAGS="-march=native -O3" \
  -DCMAKE_CXX_FLAGS="-march=native -O3"
```

### Link-Time Optimization (LTO):

```bash
cmake .. \
  -DCMAKE_BUILD_TYPE=Release \
  -DCMAKE_INTERPROCEDURAL_OPTIMIZATION=ON
```

**Warning:** LTO increases build time significantly.

---

## Clean Build

To start fresh:

```bash
cd ~/DevEnv/macOS_emu
rm -rf build
mkdir build && cd build
cmake .. -DCMAKE_BUILD_TYPE=Release
make -j$(nproc)
```

---

## Additional Resources

- **CMake Options:** `cmake -L ..` (list all options)
- **Build Logs:** Check `build/CMakeFiles/CMakeError.log` for errors
- **Darling Docs:** https://docs.darlinghq.org/build-instructions.html

---

**Questions?** Open an issue: https://github.com/CynicalWilson/macOS_emu/issues
