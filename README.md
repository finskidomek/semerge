# semerge
Smart Gentoo Portage wrapper. Automatically fixes compilation errors by cycling through Clang, ThinLTO, and GCC 15 fallback profiles.
# semerge (Smart Emerge Wrapper)

**semerge** is an intelligent wrapper for Gentoo's `emerge`. It is designed for power users who want to push their system to the limit using **Clang 21**, **ThinLTO**, and **GCC 15**, but don't want to manually fix every compilation failure.

## 🚀 How it works
The script follows a "fallback ladder" strategy. If a package fails to compile with your aggressive default flags, `semerge` automatically downgrades the optimization level and switches compilers until it succeeds:

1. **Stage 0 (Standard):** Tries your default flags (e.g., Clang + ThinLTO).
2. **Stage 1 (No-LTO):** Disables LTO if Stage 0 fails.
3. **Stage 2 (GCC Native):** Switches to GCC if Clang fails.
4. **Stage 3 (Light-GCC):** Applies GCC 15 compatibility fixes (headers & permissive flags).
5. **Stage 4 (Local):** Disables `distcc` and builds locally to rule out network issues.

## 🛠️ Prerequisites & Example Config
`semerge` assumes you start with a modern LLVM/Clang setup. Here is an example of how your `/etc/portage/make.conf` might look (adjust versions and job counts to your CPU):

```bash
# Example for LLVM 21 / Clang
CC="x86_64-pc-linux-gnu-clang-21"
CXX="x86_64-pc-linux-gnu-clang++-21"
AR="llvm-ar"
NM="llvm-nm"
RANLIB="llvm-ranlib"

# Linker and LTO settings
LDFLAGS="-Wl,-O1 -Wl,--as-needed -flto=thin -fuse-ld=lld -Wl,--thinlto-jobs=6"
