# wxPython Multi-Platform Wheel Builder

Automated CI/CD pipeline for building and distributing pre-compiled wxPython wheels across Windows and Linux distributions.

## What is this?

This repository provides **pre-built wxPython wheels** for platforms where official binaries aren't available, eliminating the need to compile wxPython from source (which can take 30+ minutes and requires complex dependency management).

**Who it helps:**
- Developers deploying wxPython apps on Linux servers
- CI/CD pipelines needing fast wxPython installation
- Teams working across multiple Linux distributions
- Anyone tired of waiting for wxPython to compile

## Quick Start

Install wxPython in seconds instead of minutes:

```bash
# Fedora (Python 3.14)
pip install https://github.com/Row0902/build-wx/releases/download/wxPython-v4.3.1/wxpython-4.3.1+fedora-cp314-cp314-linux_x86_64.whl

# Ubuntu (Python 3.14)
pip install https://github.com/Row0902/build-wx/releases/download/wxPython-v4.3.1/wxpython-4.3.1+ubuntu-cp314-cp314-linux_x86_64.whl

# Windows (Python 3.14)
pip install https://github.com/Row0902/build-wx/releases/download/wxPython-v4.3.1/wxpython-4.3.1-cp314-cp314-win_amd64.whl
```

> **Tip:** Replace `cp314` with your Python version (`cp312` for 3.12, `cp313` for 3.13)

## Supported Platforms

| Platform | Python Versions | Architecture | Wheel Size |
|----------|----------------|--------------|------------|
| **Windows** | 3.12, 3.13, 3.14 | x86_64 | ~18 MB |
| **Ubuntu** | 3.12, 3.13, 3.14 | x86_64 | ~166-170 MB |
| **Debian** | 3.12, 3.13, 3.14 | x86_64 | ~170 MB |
| **Fedora** | 3.12, 3.13, 3.14 | x86_64 | ~180 MB |
| **Arch Linux** | 3.12, 3.13, 3.14 | x86_64 | ~167-181 MB |

**Total: 15 wheel combinations**

## How It Works

### Build Pipeline

```
┌─────────────────┐
│  Check PyPI     │ ──→ Detect new wxPython version
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Matrix Build   │ ──→ Parallel compilation across 15 platforms
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Upload & Tag   │ ──→ Create GitHub Release with all wheels
└─────────────────┘
```

### Workflow Details

| Component | Technology | Purpose |
|-----------|-----------|---------|
| **CI/CD** | GitHub Actions | Automated builds on push/schedule |
| **Build Tool** | uv + pip | Fast dependency management |
| **Linux Containers** | Docker | Isolated builds per distribution |
| **Dependencies** | GTK3, WebKit2GTK | Native GUI toolkit requirements |
| **Release** | GitHub Releases | Distribution platform |

### Build Triggers

The workflow runs automatically:
- **Daily** at 06:00 UTC (checks for new wxPython versions)
- **Manually** via GitHub Actions UI (workflow_dispatch)
- **On push** to main branch (for testing changes)

## Installation Guide

### Using pip

```bash
# Direct installation
pip install <WHEEL_URL>

# In requirements.txt
# Download wheel and reference locally, or use direct URL
```

### Using uv (recommended)

```bash
# Faster installation with uv
uv add <WHEEL_URL>

# Example for Fedora
uv add https://github.com/Row0902/build-wx/releases/download/wxPython-v4.3.1/wxpython-4.3.1+fedora-cp314-cp314-linux_x86_64.whl
```

### Using Poetry

```bash
# Add wheel as dependency
poetry add <WHEEL_URL>
```

### Verification

After installation, verify wxPython works:

```bash
python -c "import wx; print(wx.__version__)"
# Output: 4.3.1
```

## Wheel Naming Convention

Linux wheels use **PEP 425 compliant** naming with local version identifiers:

```
wxpython-4.3.1+<distro>-cp<python>-cp<python>-linux_x86_64.whl
```

Examples:
- `wxpython-4.3.1+fedora-cp314-cp314-linux_x86_64.whl`
- `wxpython-4.3.1+ubuntu-cp313-cp313-linux_x86_64.whl`

This maintains compatibility with pip/uv while indicating the target distribution.

## Development

### Prerequisites

- Git
- GitHub CLI (`gh`)
- Understanding of GitHub Actions workflows

### Local Testing

```bash
# Clone repository
git clone https://github.com/Row0902/build-wx.git
cd build-wx

# Trigger workflow manually
gh workflow run "Build and Release wxPython Wheels (Multi-OS)"

# Monitor build progress
gh run list --workflow=build.yml
```

### Modifying the Workflow

The main workflow file is `.github/workflows/build.yml`. Key sections:

| Section | Lines | Purpose |
|---------|-------|---------|
| Version Check | 13-64 | Detect latest wxPython from PyPI |
| Build Matrix | 67-106 | Define platform/Python combinations |
| Dependencies | 118-145 | Install system packages per distro |
| Build Step | 169-183 | Compile wheel using pip |
| Release | 204-238 | Create GitHub release with assets |

### Adding a New Platform

1. Add new entry to `matrix.target` in `build.yml`
2. Add dependency installation step for the new distro
3. Test with manual workflow trigger
4. Update this README with new platform info

## Troubleshooting

### Installation fails with "incompatible with current platform"

**Cause:** Using an old wheel with incorrect platform tag

**Solution:** Ensure you're using wheels with `+<distro>` naming (e.g., `+fedora`, `+ubuntu`)

### Import error: libgtk-3.so.0 missing

**Cause:** Missing GTK3 runtime libraries

**Solution:**
```bash
# Ubuntu/Debian
sudo apt-get install libgtk-3-0

# Fedora
sudo dnf install gtk3

# Arch
sudo pacman -S gtk3
```

### Import error: libwebkit2gtk-4.0.so.37 missing

**Cause:** Missing WebKit2GTK runtime

**Solution:**
```bash
# Ubuntu/Debian
sudo apt-get install libwebkit2gtk-4.0-37

# Fedora
sudo dnf install webkit2gtk4.0

# Arch
sudo pacman -S webkit2gtk
```

## Contributing

Contributions are welcome! Areas for improvement:

- [ ] Add macOS support
- [ ] Support for additional Linux distributions (openSUSE, RHEL, CentOS)
- [ ] ARM64 architecture support
- [ ] Automated testing of built wheels
- [ ] Cache optimization for faster builds

## Resources

| Resource | Link |
|----------|------|
| **wxPython Official** | https://wxpython.org/ |
| **wxPython PyPI** | https://pypi.org/project/wxPython/ |
| **wxPython Docs** | https://docs.wxpython.org/ |
| **wxPython GitHub** | https://github.com/wxWidgets/Phoenix |
| **Migration Guide** | https://docs.wxpython.org/MigrationGuide.html |
| **Community Forum** | https://discuss.wxpython.org/ |

## License

This build infrastructure is provided as-is. wxPython itself is licensed under the [wxWindows Library License](https://opensource.org/licenses/wxwindows.php).

## Acknowledgments

- **wxPython Team** - For maintaining the wxPython project
- **GitHub Actions** - For providing CI/CD infrastructure
- **uv** - For fast Python package management

---

**Built with automation** 🚀 | [View Releases](https://github.com/Row0902/build-wx/releases) | [Report Issues](https://github.com/Row0902/build-wx/issues)
