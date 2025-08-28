# vendor-manifest-raspberrypi
Vendor layer manifest for RaspberryPi

## Build Steps
Note: use latest `TAG` for released versions or `develop` branch for develop HEAD.
```bash
$ repo init -u "https://github.com/rdkcentral/vendor-manifest-raspberrypi/" -b <Tag or branch Name> -m rdke-raspberrypi.xml
$ repo sync
 
Setup the IPK feed(s)
Please note for local IPK feed in local file system, the "file:/" prefix and the "/" suffix are important.
 
1, OSS IPK feed
 
   Modify this file:
   rdke/common/meta-oss-reference-release/conf/machine/include/oss.inc
 
   and set `OSS_IPK_SERVER_PATH` to the IPK feed location in the local file system,
   e.g:
   OSS_IPK_SERVER_PATH = "file:/${HOME}/community_shared/rdk-arm64-oss/<OSS IPK Version>/ipk/"
 
$ MACHINE=raspberrypi4-64-rdke source ./scripts/setup-environment
 
$ echo 'DEPLOY_IPK_FEED = "1"' >> conf/local.conf
DEPLOY_IPK_FEED to "1", which will generate local deploy ipk feed. This will generate the Packages.gz for all the arch’s present in the ${TMPDIR}/deploy/ipk/ folder.

$ bitbake lib32-packagegroup-vendor-layer (to build vendor layer IPK feed for other layers to consume)
OR
$ bitbake lib32-vendor-test-image (wrapper over packagegroup-vendor-layer to compile a bootable vendor layer test image)
```
At least either one of packagegroup or test-image is required to be successful to produce the IPK feed for the next layer. Ideally both should succeed.
 
The produced IPK objects that can be found here: ./build-raspberrypi4-64-rdke/tmp/deploy/ipk/raspberrypi4-64-rdke-vendor/
 
1, Copy or rsync the IPK feed into a location in the local file system, e.g.
   ->
   Rsyncing from ./build-raspberrypi4-64-rdke/tmp/deploy/ipk/raspberrypi4-64-rdke-vendor/* to ${HOME}/community_shared/raspberrypi4-64-rdke-vendor/${OSS IPK Version}/ipk/

Eg: Steps to build the reference RaspberryPi vendor stack from `develop` branch
```bash
$ repo init -u "https://github.com/rdkcentral/vendor-manifest-raspberrypi/" -b develop -m rdke-raspberrypi.xml
$ repo sync
$ MACHINE=raspberrypi4-64-rdke source ./scripts/setup-environment
$ echo 'DEPLOY_IPK_FEED = "1"' >> conf/local.conf
$ bitbake lib32-packagegroup-vendor-layer
OR
$ bitbake lib32-vendor-test-image
```
