# How to Contribute to arpkg

## What is this?
This is the official package repository for **arpkg**, a package manager.

## The Golden Rule: No Binaries
**DO NOT submit `.tar.gz` files or pre-compiled binaries.** To maintain a zero-trust, zero-bloat ecosystem, ARK OS compiles all packages from source on our secure CI/CD servers. Any Pull Request containing a pre-compiled binary will be immediately closed.

## Requirements for Submission
To submit a package to arpkg, you must create a directory with your package name and include a single configuration file called `ARKBUILD`.

> Note: Make sure the `ARKBUILD` does not contain wget commands! Use curl instead!

### 1. Directory Structure
Fork this repository and create your package under the appropriate category:
```text
ARK-Packages/
├── core/
│   └── [package-name]/
│       └── ARKBUILD
└── community/
    └── [package-name]/
        └── ARKBUILD
```

### 2. The `ARKBUILD` File

The `ARKBUILD` is a strict blueprint that tells our build servers exactly where to fetch the official source code and how to compile it against the `x86_64-linux-musl` toolchain.

Create an `ARKBUILD` file using the following template:

```bash

# ARKBUILD Template
PKG_NAME="example"
PKG_VERSION="1.0.0"
SOURCE_URL="https://github.com/developer/example.git"
DEPENDENCIES=""

fetch() {
    git clone --depth 1 --branch v$PKG_VERSION $SOURCE_URL
}

compile() {
    cd example
    ./autogen.sh
    ./configure --prefix=/usr
    make -j$(nproc)
    make DESTDIR=$PKG_ROOT install
}

```

### 3. Info

You must provide accurate developer and support information. This data will be bundled into the final `.tar.gz` and displayed to users via the `arpkg info` command.

Create a `pkginfo.json` file strictly following this schema:
```json
{
  "pkg_name": "example-tool",
  "maintainer": "Your Name or Handle",
  "email": "your.email-noreply@github.com",
  "credits": ["Original Author: Name of origional author", "Ported by: Name of porter"],
  "support_url": "[https://github.com/your-repo/issues](https://github.com/your-repo/issues)"
}
```

### 3. The CI/CD Pipeline

Once you submit your PR:

 1. `Validation`: Our automated servers will verify your pkginfo.json format.

 2. `The Forge`: The server boots a clean ARK OS container and executes your ARKBUILD.

 3. `Packaging`: The compiled binaries and your pkginfo.json are cryptographically hashed and packaged together.

 4. `Merge`: Once approved by a maintainer, the package goes live on the arpkg network.
