# vendor-manifest-raspberrypi
Vendor layer manifest for RaspberryPi

## Build Steps
Note: use latest `TAG` for released versions or `develop` branch for develop HEAD.
```bash
# Initialize the repository
repo init -u https://github.com/rdkcentral/vendor-manifest-raspberrypi.git -m rdke-raspberrypi.xml -b develop

# Synchronize the repository
repo sync --no-clone-bundle --no-tags

# Build the project
MACHINE=raspberrypi4-64-rdke source ./scripts/setup-environment
bitbake lib32-vendor-test-image
```
