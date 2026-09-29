# Installation Guide

This guide provides detailed instructions for installing gitlogue on different platforms.

## Prerequisites

- Git must be installed on your system
- For building from source: Rust 1.70 or later, a C toolchain, and **Perl**
  (see [Build Dependencies](#build-dependencies-perl) below)

## Installation Methods

### Method 1: Using Install Script (Recommended)

The quickest way to install gitlogue is using the installation script:

```bash
curl -fsSL https://raw.githubusercontent.com/unhappychoice/gitlogue/main/install.sh | bash
```

This script will:
- Automatically detect your platform (macOS Intel/ARM, Linux x64/ARM64/LoongArch64)
- Download the latest release binary
- Install to `~/.local/bin` (customizable with `INSTALL_DIR` environment variable)

**Custom installation directory:**
```bash
INSTALL_DIR=/usr/local/bin curl -fsSL https://raw.githubusercontent.com/unhappychoice/gitlogue/main/install.sh | bash
```

### Method 2: Using Homebrew

For macOS and Linux users with Homebrew:

```bash
brew install unhappychoice/tap/gitlogue
```

**Updating via Homebrew:**
```bash
brew upgrade gitlogue
```

### Method 3: Using pacman (Arch Linux)

gitlogue is available in the [official Arch Linux repositories](https://archlinux.org/packages/extra/x86_64/gitlogue/):

```bash
sudo pacman -Syu gitlogue
```

This keeps you on the latest packaged release, integrates with system upgrades, and installs the binary into `/usr/bin`.

### Method 4: Using Cargo

Install from [crates.io](https://crates.io) using Cargo:

```bash
cargo install gitlogue
```

This will download, compile, and install the latest version.

### Method 5: Using Nix

Run directly without installation:

```bash
nix run github:unhappychoice/gitlogue
```

Or add to your flake inputs:

```nix
{
  inputs.gitlogue.url = "github:unhappychoice/gitlogue";
}
```

Then use `inputs.gitlogue.packages.${system}.default` in your configuration.

### Method 6: Download Pre-built Binaries

Pre-built binaries for multiple platforms are available in the [Releases](https://github.com/unhappychoice/gitlogue/releases) section.

**Supported platforms:**
- macOS (Intel: x86_64, Apple Silicon: aarch64)
- Linux (x64: x86_64, ARM64: aarch64)
- Linux (LoongArch64: loongarch64)
- Windows (x64: x86_64)

The downloadable archives are named `gitlogue-<version>-<target>.tar.gz`:

| Platform | Target |
| --- | --- |
| macOS Intel | `x86_64-apple-darwin` |
| macOS Apple Silicon | `aarch64-apple-darwin` |
| Linux x64 | `x86_64-unknown-linux-gnu` |
| Linux ARM64 | `aarch64-unknown-linux-gnu` |
| Linux LoongArch64 | `loongarch64-unknown-linux-gnu` |
| Windows x64 | `x86_64-pc-windows-msvc` |

**Manual installation:**
1. Download the appropriate archive for your platform
2. Extract the binary:
   ```bash
   # For Linux/macOS
   tar -xzf gitlogue-v*.tar.gz

   # For Windows
   # Use your preferred extraction tool for .zip files
   ```
3. Move to a directory in your PATH:
   ```bash
   sudo mv gitlogue /usr/local/bin/
   ```

### Method 7: From Source

If you want to build the latest development version or contribute to the project:

1. Clone the repository:
   ```bash
   git clone https://github.com/unhappychoice/gitlogue.git
   cd gitlogue
   ```

2. Build and install:
   ```bash
   cargo install --path .
   ```

   Or just build for development:
   ```bash
   cargo build --release
   ```

   The binary will be located at `target/release/gitlogue`.

## Platform-Specific Notes

### macOS

On macOS, you may need to install Xcode Command Line Tools if you haven't already:

```bash
xcode-select --install
```

### Linux

Make sure you have the build essentials installed:

**Ubuntu/Debian:**
```bash
sudo apt update
sudo apt install build-essential git
```

**Fedora/RHEL (incl. Anolis, openEuler, Kylin):**
```bash
sudo dnf install gcc gcc-c++ make cmake pkgconf-pkg-config perl git
```

**Arch Linux:**
```bash
sudo pacman -S base-devel perl git
```

### Build Dependencies (Perl)

gitlogue links libgit2 and OpenSSL **statically** (`vendored-openssl` +
`vendored-libgit2`), so those C libraries are compiled from source as part of the
build. OpenSSL's build system is driven by Perl scripts, which means `perl` must
be on `PATH` even when a system OpenSSL is already installed — otherwise the build
fails with `Command 'perl' not found. Is perl installed?`.

Minimal server installs (Anolis, openEuler, Kylin, RHEL, and slim containers) often
ship without Perl, so install it before building from source:
```bash
sudo dnf install -y perl          # Fedora/RHEL/Anolis/openEuler/Kylin
sudo apt install -y perl          # Debian/Ubuntu
perl -v                            # verify
```

If you would rather not install Perl at all, build against the system OpenSSL
instead of the vendored copy (requires the OpenSSL development headers, 3.x):
```bash
sudo dnf install -y openssl-devel
OPENSSL_NO_VENDOR=1 cargo install gitlogue
```
This trades the Perl requirement for a runtime dependency on the system
`libssl.so.3`/`libcrypto.so.3`.

### Linux (LoongArch64 / 龙芯)

The LoongArch64 release (`loongarch64-unknown-linux-gnu`) is cross-compiled
against a glibc sysroot, so it follows the same glibc requirement as the other
Linux builds (glibc 2.35 or newer) and runs on glibc based LoongArch64
distributions — Loongnix, Kylin, openEuler, Debian/Ubuntu `loong64`, Arch Linux
and others. `install.sh` picks it up automatically and performs the same glibc
version check it does for x86_64 and aarch64.

```bash
curl -fsSL https://raw.githubusercontent.com/unhappychoice/gitlogue/main/install.sh | bash
```

Rust publishes official host tools for LoongArch64, so building natively on a
LoongArch64 machine also works:

```bash
cargo install gitlogue
```

Prefer the **install script over `cargo install`** on LoongArch64: the pre-built
binary needs no compiler, no Perl, and no long native build (compiling the
vendored libgit2 + OpenSSL on LoongArch64 takes tens of minutes). If you do build
from source, install the [build dependencies](#build-dependencies-perl) first —
Anolis, Kylin, and openEuler images usually need `perl` installed explicitly.

### Windows

1. Install [Rust](https://www.rust-lang.org/tools/install) using rustup
2. Install [Git for Windows](https://git-scm.com/download/win)
3. Follow the Cargo installation method above

## Verifying Installation

After installation, verify that gitlogue is correctly installed:

```bash
gitlogue --help
```

You should see the help message with available commands and options.

## Updating

### Install Script

Re-run the installation script to update to the latest version:

```bash
curl -fsSL https://raw.githubusercontent.com/unhappychoice/gitlogue/main/install.sh | bash
```

### Homebrew

```bash
brew upgrade gitlogue
```

### Cargo Installation

To update to the latest version:

```bash
cargo install gitlogue
```

Cargo will automatically update to the newest version.

### Source Installation

```bash
cd gitlogue
git pull origin main
cargo install --path .
```

## Uninstalling

### Install Script

If installed via the install script, manually remove the binary:

```bash
rm ~/.local/bin/gitlogue
# Or if you used a custom INSTALL_DIR
rm /path/to/your/install/dir/gitlogue
```

### Homebrew

```bash
brew uninstall gitlogue
```

### Cargo

```bash
cargo uninstall gitlogue
```

## Configuration

gitlogue supports configuration via `~/.config/gitlogue/config.toml`. See the [Configuration Guide](configuration.md) for detailed options and the [Usage Guide](usage.md) for command-line options.

## Troubleshooting

### Command Not Found

If you get a "command not found" error after installation, make sure the installation directory is in your PATH:

**For Install Script (default: `~/.local/bin`):**
```bash
export PATH="$HOME/.local/bin:$PATH"
```

**For Cargo:**
```bash
export PATH="$HOME/.cargo/bin:$PATH"
```

Add the appropriate line to your shell configuration file (`~/.bashrc`, `~/.zshrc`, etc.) to make it permanent.

### Build Errors

If you encounter build errors:

1. Ensure you have the latest version of Rust:
   ```bash
   rustup update
   ```

2. Clean the build directory and try again:
   ```bash
   cargo clean
   cargo build --release
   ```

3. Install the [build dependencies](#build-dependencies-perl) — in particular
   `perl`, which is required for the vendored OpenSSL build.

### `perl` Not Found (openssl-sys) During Source Build

If `cargo install gitlogue` or `cargo build` fails with:

```
warning: openssl-sys@0.9.117: configuring OpenSSL build: Command 'perl' not found. Is perl installed?
warning: openssl-sys@0.9.117: openssl-src: failed to build OpenSSL from source
error: failed to run custom build command for `openssl-sys v0.9.117`
```

the compiler is fine — Perl is simply missing. Because gitlogue enables
`vendored-openssl`, OpenSSL is built from source, and OpenSSL's build system is
written in Perl.

**Solutions:**

1. **Install Perl** (recommended):
   ```bash
   sudo dnf install -y perl    # Fedora/RHEL/Anolis/openEuler/Kylin
   sudo apt install -y perl    # Debian/Ubuntu
   cargo install gitlogue
   ```

2. **Use the system OpenSSL instead of the vendored one** (no Perl needed):
   ```bash
   sudo dnf install -y openssl-devel
   OPENSSL_NO_VENDOR=1 cargo install gitlogue
   ```

3. **Skip the build entirely** by using the pre-built binary, which is linked
   statically and therefore needs no toolchain, no Perl, and no matching system
   OpenSSL:
   ```bash
   curl -fsSL https://raw.githubusercontent.com/unhappychoice/gitlogue/main/install.sh | bash
   ```

On slower CPUs (for example LoongArch64) the vendored build also takes a long
time because it compiles libgit2 and OpenSSL from source. `cargo install` builds
in a throwaway temporary directory by default, so an interrupted build starts
over. Point it at a persistent directory to make retries cheap:
```bash
CARGO_TARGET_DIR=$HOME/.cache/gitlogue-build cargo install gitlogue
```

### Permission Errors

On Linux/macOS, if you encounter permission errors during installation, you may need to use `sudo`:

```bash
sudo cargo install gitlogue
```

However, it's generally recommended to use cargo without sudo and ensure your user has proper permissions.

### GLIBC Version Errors (Linux)

If you see an error like:

```
gitlogue: /lib/x86_64-linux-gnu/libc.so.6: version `GLIBC_2.35' not found (required by gitlogue)
```

This means your system's glibc version is older than what the pre-built binary requires.

**Solutions:**

1. **Upgrade your OS** (Recommended)

   The safest way is to upgrade to a newer version of your Linux distribution:
   - Ubuntu 22.04+ includes glibc 2.35
   - Debian 12 (Bookworm)+ includes glibc 2.36
   - Fedora 36+ includes glibc 2.35+

2. **Build from source**

   Install via Cargo to compile for your system:
   ```bash
   cargo install gitlogue
   ```

3. **Use Nix**

   If you have Nix installed:
   ```bash
   nix run github:unhappychoice/gitlogue
   ```

All three Linux builds (`x86_64-unknown-linux-gnu`, `aarch64-unknown-linux-gnu`
and `loongarch64-unknown-linux-gnu`) are dynamically linked and affected.

## Next Steps

- Read the [Usage Guide](usage.md) to learn how to use gitlogue
- Explore [Theme Customization](themes.md) to personalize your experience
- Check out the [Contributing Guidelines](CONTRIBUTING.md) if you want to contribute

## Getting Help

If you encounter issues during installation:

- Check the [GitHub Issues](https://github.com/unhappychoice/gitlogue/issues) for known problems
- Open a new issue if your problem isn't already reported
- Include your OS, Rust version (`rustc --version`), and error messages
