# MBP 14,2 (2017) Audio Fix for Fedora 43 (Kernel 6.19+)

This repository serves as a technical record and guide for enabling internal audio on MacBook Pro 14,2 (2017 Touch Bar) models using the Cirrus Logic CS8409 chip on Fedora 43.

## Credits & References

This fix is a compilation of community efforts. Special thanks to the original developers:

    Primary Driver Source: davidjo/snd_hda_macbookpro

    Cirrus Codec Support: egorenar/snd-hda-codec-cs8409

    Original Guide: Ranquetat's Medium Post - Baseline for DKMS installation.

---

## The Fix Procedure

### 1. Environment Setup
```bash
sudo dnf groupinstall "Development Tools" -y
sudo dnf install dkms alsa-tools git kernel-devel kernel-headers -y
```

### 2. DKMS Tree Preparation
```bash
cd ~/Downloads
git clone https://github.com/danilovcorrea/snd_hda_macbookpro.git
cd snd_hda_macbookpro
sudo mkdir -p /usr/src/snd_hda_macbookpro-1.0
sudo cp -r ./* /usr/src/snd_hda_macbookpro-1.0/
sudo chmod +x /usr/src/snd_hda_macbookpro-1.0/install.cirrus.driver.sh
```

### 3. Apply GCC 14+ Compatibility Flags
```bash
sudo bash -c 'cat > /usr/src/snd_hda_macbookpro-1.0/dkms.conf << EOF
PACKAGE_NAME="snd_hda_macbookpro"
PACKAGE_VERSION="1.0"
PRE_BUILD="install.cirrus.driver.sh -k $kernelver"
MAKE="make KERNELRELEASE=${kernelver} KDIR=/lib/modules/${kernelver}/build M=${dkms_tree}/${PACKAGE_NAME}/${PACKAGE_VERSION}/build/build/hda CFLAGS_MODULE="-DAPPLE_PINSENSE_FIXUP -DAPPLE_CODECS -DCONFIG_SND_HDA_RECONFIG=1 -Wno-unused-variable -Wno-unused-function -Wno-error -Wno-incompatible-pointer-types -Wno-implicit-function-declaration""
BUILT_MODULE_NAME[0]="snd-hda-codec-cs8409"
BUILT_MODULE_LOCATION[0]="build/hda/codecs/cirrus"
DEST_MODULE_LOCATION[0]="/updates/codecs/cirrus"
AUTOINSTALL="yes"
EOF'
```

### 4. Code Patch for Kernel 6.19+
```bash
sudo sed -i "s/make -C/sed -i 's\/\.free = cs8409_free,\/\/\/ \.free = cs8409_free,\/g' build\/hda\/codecs\/cirrus\/patch_cs8409.c && sed -i 's\/codec->patch_ops =\/\/\/ codec->patch_ops =\/g' build\/hda\/codecs\/cirrus\/patch_cs8409.c && sed -i 's\/HDA_CODEC_ENTRY(0x10138409, \"CS8409\", patch_cs8409),\/HDA_CODEC_QUIRK(0x10138409, 0, \"CS8409\", patch_cs8409),\/g' build\/hda\/codecs\/cirrus\/patch_cs8409.c && make -C/" /usr/src/snd_hda_macbookpro-1.0/install.cirrus.driver.sh
```

### 5. Build and Install via DKMS
```bash
sudo dkms add -m snd_hda_macbookpro -v 1.0
sudo dkms install -m snd_hda_macbookpro -v 1.0 --force
```

---
## Verification
```bash
modinfo snd-hda-codec-cs8409 | grep filename
```

---
*Technical record for MBP 14,2 2017 users.*
