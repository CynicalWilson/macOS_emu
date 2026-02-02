# Pick Up Here: macOS_emu (Darling) Project

**Status:** ✅ Successfully cloned, documented, and pushed to GitHub  
**Last Updated:** 2026-02-02 01:23 GMT+1  
**Next Step:** Build and test Darling

---

## Project Context

### Why This Exists
User asked: *"Is it generally possible to run macOS/Mac applications on an x64 Intel running Ubuntu 22?"*

**Answer:** Yes, with limitations. **Darling** is the macOS equivalent of Wine (for Windows apps) — it translates Darwin/macOS system calls to Linux.

**Current Reality:**
- ✅ CLI tools work well
- ❌ GUI apps mostly don't work (yet)
- 🔧 Active development, improving over time

---

## What We Accomplished

### 1. Cloned Darling Source
```bash
Location: ~/DevEnv/macOS_emu
Size: 5.4 GB
Submodules: 149 (all initialized)
```

**Challenge:** Initial clone failed at 95% because `git-lfs` (Git Large File Storage) wasn't installed.

**Resolution:**
1. User installed git-lfs: `sudo apt install git-lfs`
2. Ran: `git lfs install`
3. Completed submodules: `git submodule update --init --recursive`
4. ✅ All 149 submodules successfully cloned

### 2. Created GitHub Repository
**Repo:** https://github.com/CynicalWilson/macOS_emu  
**Visibility:** Public  
**Description:** Darling - macOS translation layer for Linux (fork)

**Actions:**
- Authenticated gh CLI: `gh auth login`
- Created repo: `gh repo create CynicalWilson/macOS_emu --public`
- Pushed master branch to origin
- Committed documentation

### 3. Added Comprehensive Documentation

**README.md** - Main documentation covering:
- What Darling is (macOS translation layer)
- System requirements (Ubuntu 22.04+, x86_64, 4GB RAM)
- Quick build instructions
- Usage examples (`darling shell`, `darling exec`)
- What works vs. what doesn't
- Troubleshooting common issues
- Resources & community links

**BUILD.md** - Detailed build guide covering:
- Complete dependency installation (cmake, clang, libraries)
- Step-by-step configure → build → install process
- Build time estimates (15-60 min depending on CPU)
- Kernel module loading (`darling-mach`)
- Performance tuning options (LTO, CPU-specific optimizations)
- Troubleshooting build failures
- Update/rebuild procedures

---

## Technical Details

### Repository Structure
```
~/DevEnv/macOS_emu/
├── README.md              # Main documentation (created by us)
├── BUILD.md               # Detailed build guide (created by us)
├── PICK-UP-HERE.md        # This file (created by us)
├── CMakeLists.txt         # Build configuration
├── src/                   # Darling source code
│   ├── external/          # 149 submodules (Swift, WebCore, etc.)
│   └── ...
└── build/                 # Not created yet (build artifacts go here)
```

### Git Configuration
```bash
Origin: https://github.com/CynicalWilson/macOS_emu.git
Branch: master
Upstream: https://github.com/darlinghq/darling.git (original)
Submodules: All initialized via .gitmodules
```

### Key Dependencies Installed
- git-lfs (for Swift submodule and large binary files)
- All build dependencies listed in BUILD.md

---

## Current State

### ✅ Completed
- [x] Cloned full Darling source with all 149 submodules
- [x] Resolved git-lfs issue
- [x] Pushed to GitHub (CynicalWilson/macOS_emu)
- [x] Created comprehensive README.md
- [x] Created detailed BUILD.md
- [x] Repository is public and accessible

### ⏸️ Not Started Yet
- [ ] Install build dependencies (see BUILD.md Step 1)
- [ ] Configure CMake build
- [ ] Compile Darling (~30-60 min build time)
- [ ] Install system-wide
- [ ] Load kernel module (darling-mach)
- [ ] Test with macOS binaries

---

## Next Steps (When Ready to Build)

### 1. Install Build Dependencies (~5 min)
```bash
sudo apt update
sudo apt install -y \
  cmake clang bison flex xz-utils libfuse-dev libudev-dev pkg-config \
  libc6-dev-i386 linux-headers-generic gcc-multilib libcap2-bin \
  libcairo2-dev libgl1-mesa-dev libglu1-mesa-dev libtiff5-dev \
  libfreetype6-dev git git-lfs libelf-dev libxml2-dev libegl1-mesa-dev \
  libfontconfig1-dev libbsd-dev
```

### 2. Configure Build (~2 min)
```bash
cd ~/DevEnv/macOS_emu
mkdir build && cd build
cmake .. -DCMAKE_BUILD_TYPE=Release
```

### 3. Compile (~30-60 min)
```bash
make -j$(nproc)
```

### 4. Install & Test (~5 min)
```bash
sudo make install
sudo modprobe darling-mach
darling shell
```

---

## Useful Commands

### Check build progress:
```bash
cd ~/DevEnv/macOS_emu/build
make -j$(nproc) 2>&1 | tee build.log
```

### Verify installation:
```bash
which darling
darling --version
lsmod | grep darling
```

### Start Darling shell:
```bash
darling shell
# Inside: uname -a (should show Darwin)
```

### Sync with upstream Darling updates:
```bash
cd ~/DevEnv/macOS_emu
git remote add upstream https://github.com/darlinghq/darling.git
git fetch upstream
git merge upstream/master
git submodule update --init --recursive
```

---

## Known Limitations

### What Works ✅
- macOS CLI tools (bash, ls, grep, etc.)
- Developer tools (compilers, build systems)
- Simple console applications
- System utilities

### What Doesn't Work ❌
- Most GUI apps (Cocoa/AppKit)
- Apps requiring Metal API (3D graphics)
- Code-signed binaries with strict checks
- Apple Silicon (M1/M2) apps on x64

### Compatibility
Think of Darling today like Wine in 2005: CLI tools work, GUI is experimental. It's improving but not ready for mainstream macOS app usage yet.

---

## Resources

- **Official Site:** https://www.darlinghq.org/
- **Documentation:** https://docs.darlinghq.org/
- **Upstream Repo:** https://github.com/darlinghq/darling
- **Our Fork:** https://github.com/CynicalWilson/macOS_emu
- **Discord:** https://discord.gg/darling

---

## Context for Future Sessions

**User's Question:** Can I run macOS apps on Ubuntu x64?  
**Answer:** Yes with Darling, but only CLI tools work reliably today.  
**User's Action:** Wanted to clone source and push to personal GitHub for experimentation.  
**Current State:** Source cloned, documented, and ready to build.  
**User hasn't decided yet:** Whether to build now or later.

**No decisions pending.** User can build when ready following BUILD.md steps.

---

**File Purpose:** This file allows anyone (human or AI) to understand exactly what was done, why, and what comes next — with zero context loss.
