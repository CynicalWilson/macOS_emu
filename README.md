# macOS_emu (Darling)

**Run macOS applications on Linux** using a translation layer (similar to Wine for Windows apps).

This is a fork of [Darling](https://www.darlinghq.org/) - a Darwin/macOS emulation layer for Linux.

---

## What is Darling?

Darling translates macOS system calls to Linux equivalents, allowing some macOS binaries to run natively on Linux without requiring a macOS installation or VM.

**Current Status:**
- ✅ **Command-line tools:** Good compatibility
- ⚠️ **GUI applications:** Limited/experimental support
- 🔧 **Active development:** Ongoing improvements

**Think of it as:** Wine for macOS apps instead of Windows apps.

---

## System Requirements

- **OS:** Ubuntu 22.04+ (or Debian-based distro)
- **Arch:** x86_64 (Intel/AMD)
- **RAM:** 4GB+ recommended
- **Disk:** ~6GB for source + build artifacts

---

## Build Instructions (Ubuntu 22.04)

### 1. Install Dependencies

```bash
sudo apt update
sudo apt install -y \
  cmake clang bison flex xz-utils libfuse-dev libudev-dev pkg-config \
  libc6-dev-i386 linux-headers-generic gcc-multilib libcap2-bin \
  libcairo2-dev libgl1-mesa-dev libglu1-mesa-dev libtiff5-dev \
  libfreetype6-dev git git-lfs libelf-dev libxml2-dev libegl1-mesa-dev \
  libfontconfig1-dev libbsd-dev
```

### 2. Configure Git LFS

```bash
git lfs install
```

### 3. Clone & Initialize Submodules

```bash
# Already done if you're working from this repo
cd ~/DevEnv/macOS_emu
git submodule update --init --recursive
```

### 4. Build Darling

```bash
# Create build directory
mkdir build && cd build

# Configure (this takes a while)
cmake .. -DCMAKE_BUILD_TYPE=Release

# Build (use all CPU cores, takes 30-60 min)
make -j$(nproc)

# Install system-wide (optional)
sudo make install
```

### 5. Load Kernel Module

```bash
# Load the darling kernel module
sudo modprobe darling-mach

# Make it load on boot (optional)
echo darling-mach | sudo tee /etc/modules-load.d/darling.conf
```

---

## Usage

### Start Darling Shell

```bash
darling shell
```

This drops you into a macOS-like environment where you can run macOS binaries.

### Run a macOS Binary

```bash
# Inside darling shell
./some-macos-app

# Or from Linux directly
darling exec /path/to/macos-binary
```

### Install macOS Software

```bash
# Inside darling shell
installer -pkg package.pkg -target /
```

### Example: Run macOS uname

```bash
darling shell
uname -a
# Output: Darwin ...
```

---

## Limitations

### What Works ✅
- Command-line utilities (ls, grep, bash, etc.)
- Simple CLI tools and scripts
- Some developer tools (compilers, build tools)
- Basic system services

### What Doesn't Work ❌
- Most GUI applications (Cocoa/AppKit apps)
- Apps requiring Apple Silicon (M1/M2) - this is x64 only
- Code-signed binaries with strict requirements
- Metal API (3D graphics)
- Apps with heavy macOS framework dependencies

---

## Troubleshooting

### "darling: command not found"
Either add to PATH or run from build directory:
```bash
export PATH="$HOME/DevEnv/macOS_emu/build/bin:$PATH"
```

### "Module darling-mach not found"
Build artifacts may need sudo:
```bash
cd ~/DevEnv/macOS_emu/build
sudo make install
sudo modprobe darling-mach
```

### App crashes immediately
Check if it's a GUI app (most don't work yet) or requires frameworks Darling doesn't implement.

---

## Uninstall

```bash
cd ~/DevEnv/macOS_emu/build
sudo make uninstall
sudo rmmod darling-mach
sudo rm /etc/modules-load.d/darling.conf
```

---

## Resources

- **Official Darling Site:** https://www.darlinghq.org/
- **Upstream Repo:** https://github.com/darlinghq/darling
- **Documentation:** https://docs.darlinghq.org/
- **Discord Community:** https://discord.gg/darling

---

## Development

This fork tracks the upstream Darling project. To sync with latest changes:

```bash
git remote add upstream https://github.com/darlinghq/darling.git
git fetch upstream
git merge upstream/master
git submodule update --init --recursive
```

---

## License

Darling is licensed under GPL v3. See [LICENSE](LICENSE) for details.

Individual submodules may have different licenses (BSD, MIT, etc.) - check their respective directories.

---

## Contributing

Contributions welcome! For major changes:
1. Fork this repo
2. Create a feature branch
3. Submit a pull request

For Darling core improvements, contribute to [upstream](https://github.com/darlinghq/darling).

---

**Built on:** Ubuntu 22.04 x86_64  
**Last Updated:** February 2026
