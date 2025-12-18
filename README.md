# Vendor Manifest for Raspberry Pi

[![Release](https://img.shields.io/github/v/tag/rdkcentral/vendor-manifest-raspberrypi?label=latest%20tag)](https://github.com/rdkcentral/vendor-manifest-raspberrypi/tags)
[![License](https://img.shields.io/github/license/rdkcentral/vendor-manifest-raspberrypi)](LICENSE)
[![Yocto](https://img.shields.io/badge/Yocto-kirkstone-blue?logo=yoctoproject)](https://docs.yoctoproject.org/)
[![Platform](https://img.shields.io/badge/Platform-RaspberryPi4--64-green?logo=raspberrypi)](https://www.raspberrypi.com/)

Vendor layer manifest for Raspberry Pi — this repo provides the manifest and instructions to build and produce IPK feeds and vendor test images for RDK-based stacks.

---

## Table of Contents

- [Vendor Manifest for Raspberry Pi](#vendor-manifest-for-raspberry-pi)
  - [Table of Contents](#table-of-contents)
  - [Quick Start](#quick-start)
  - [Setup IPK Feeds](#setup-ipk-feeds)
    - [Configure OSS IPK Feed](#configure-oss-ipk-feed)
  - [Build Environment](#build-environment)
  - [Building Targets](#building-targets)
    - [Build Targets Using Multiconfig (Debug/Prod/ProdLog)](#build-targets-using-multiconfig-debugprodprodlog)
  - [Deploying IPK Feed](#deploying-ipk-feed)
    - [Notes](#notes)
  - [Example: Building from `develop` Branch or Release tag `V4.9.1`](#example-building-from-develop-branch-or-release-tag-v491)
  - [License](#license)

---

## Quick Start

> Use a release **tag** for stable builds or `develop` for the latest development branch.

```bash
repo init -u "https://github.com/rdkcentral/vendor-manifest-raspberrypi/" -b <tag-or-branch> -m rdke-raspberrypi.xml
repo sync
```

---

## Setup IPK Feeds

IPK feeds are required for package installation and dependency resolution in downstream layers. You can configure local (file-based) feeds or remote feeds.

> ⚠️ **Important:** When using a local file feed, include the `file:/` prefix and make sure the path ends with a trailing `/`.

### Configure OSS IPK Feed

Edit the file:
```
rdke/common/meta-oss-reference-release/conf/machine/include/oss.inc
```

Set the feed path (example):
```bitbake
OSS_IPK_SERVER_PATH = "file:/${HOME}/community_shared/rdk-arm64-oss/<OSS-IPK-Version>/ipk/"
```

---

## Build Environment

Initialize your build environment for the Raspberry Pi 4 (64-bit) server:

```bash
MACHINE=raspberrypi4-64-rdke source ./scripts/setup-environment
```

Enable generation of a deployable IPK feed during the build:

```bash
echo 'DEPLOY_IPK_FEED = "1"' >> conf/local.conf
```

When enabled, the build will produce `Packages.gz` for each architecture in:
```
./build-raspberrypi4-64-rdke/tmp/deploy/ipk/
```

---

## Building Targets

Choose one (or both) of the following targets depending on your goal:

- **Vendor layer IPK feed** (produces IPKs consumed by downstream layers):
```bash
bitbake lib32-packagegroup-vendor-layer
```

- **Bootable vendor test image** (wrapper that builds a bootable image including vendor packages):
```bash
bitbake lib32-vendor-test-image
```

✅ At least one of the above must succeed to produce a usable IPK feed. Ideally both should succeed.

### Build Targets Using Multiconfig (Debug/Prod/ProdLog)

To build different variants of the vendor layer image using multiconfig:

```bash
# Build debug (dev) variant of vendor layer image (NOTE - Default is debug)
bitbake lib32-vendor-test-image

# Build prod variant of vendor layer image
bitbake mc:prod:lib32-vendor-test-image

# Build prodlog variant of vendor layer image
bitbake mc:prodlog:lib32-vendor-test-image

# Build all variants of vendor layer image
bitbake lib32-vendor-test-image mc:prod:lib32-vendor-test-image mc:prodlog:lib32-vendor-test-image
```

The generated IPKs are located at:
```
# Debug variant (default)
./build-raspberrypi4-64-rdke/tmp/deploy/ipk/raspberrypi4-64-rdke-vendor/

# Prod variant
./build-raspberrypi4-64-rdke/tmp-prod/deploy/ipk/raspberrypi4-64-rdke-vendor/

# ProdLog variant
./build-raspberrypi4-64-rdke/tmp-prodlog/deploy/ipk/raspberrypi4-64-rdke-vendor/
```

---

## Deploying IPK Feed

Copy or sync the generated IPK feed to your shared/local repository path:

```bash
# Debug variant (default)
rsync -av ./build-raspberrypi4-64-rdke/tmp/deploy/ipk/raspberrypi4-64-rdke-vendor/* ~/community_shared/raspberrypi4-64-rdke-vendor/<OSS-IPK-Version>/ipk/

# Prod variant
rsync -av ./build-raspberrypi4-64-rdke/tmp-prod/deploy/ipk/raspberrypi4-64-rdke-vendor/* ~/community_shared/raspberrypi4-64-rdke-vendor/<OSS-IPK-Version>/ipk/

# ProdLog variant
rsync -av ./build-raspberrypi4-64-rdke/tmp-prodlog/deploy/ipk/raspberrypi4-64-rdke-vendor/* ~/community_shared/raspberrypi4-64-rdke-vendor/<OSS-IPK-Version>/ipk/
```

After syncing, confirm the `Packages.gz` files and directory layout are correct for the consumers of the feed.

### Notes

- **IPK feed not found by consumers**  
  Verify `OSS_IPK_SERVER_PATH` has `file:/` prefix and a trailing `/`. Confirm `Packages.gz` exists for each arch.

- **Paths with spaces**  
  Avoid spaces in file-system paths for feeds. Use underscores `_` instead.
  
---

## Example: Building from `develop` Branch or Release tag `V4.9.1`

A fully copy-paste example to build from `develop` or Release tag `V4.9.1`:

```bash
# Option A: Building from release tag V4.9.1
repo init -u "https://github.com/rdkcentral/vendor-manifest-raspberrypi/" -b refs/tags/4.9.1 -m rdke-raspberrypi.xml

# Option A: Building from develop Branch
repo init -u "https://github.com/rdkcentral/vendor-manifest-raspberrypi/" -b develop -m rdke-raspberrypi.xml

repo sync
MACHINE=raspberrypi4-64-rdke source ./scripts/setup-environment
echo 'DEPLOY_IPK_FEED = "1"' >> conf/local.conf

# Option A: Build vendor IPK feed
bitbake lib32-packagegroup-vendor-layer

# Option B: Build bootable vendor test image (debug/dev variant - default)
bitbake lib32-vendor-test-image

# Option C: Build prod variant of vendor layer image
bitbake mc:prod:lib32-vendor-test-image

# Option D: Build prodlog variant of vendor layer image
bitbake mc:prodlog:lib32-vendor-test-image

# Option E: Build all variants of vendor layer image
bitbake lib32-vendor-test-image mc:prod:lib32-vendor-test-image mc:prodlog:lib32-vendor-test-image
```

---

## License

This project is licensed under **Apache-2.0**. See the [LICENSE](LICENSE) file for details.
