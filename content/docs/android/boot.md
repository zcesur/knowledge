## Get the tools
```
git clone https://github.com/zcesur/mkbootimg
cd mkbootimg
make
mv -t /usr/local/bin -- mkbootimg unpackbootimg
```

## Unpack bootimg
```bash
file boot.img
# boot.img: Android bootimg, kernel (0x10008000), ramdisk (0x11000000), page size: 2048

mkdir -p boot
unpackbootimg -i boot.img -o boot/
# BOARD_KERNEL_CMDLINE
# BOARD_KERNEL_BASE 10000000
# BOARD_PAGE_SIZE 2048
# BOARD_HASH_TYPE sha1
# BOARD_KERNEL_OFFSET 00008000
# BOARD_RAMDISK_OFFSET 01000000
# BOARD_SECOND_OFFSET 00f00000
# BOARD_TAGS_OFFSET 00000100
# BOARD_OS_VERSION 8.0.0
# BOARD_OS_PATCH_LEVEL 2019-02
# BOARD_DT_SIZE 387072

cd boot
mkdir -p ramdisk
gunzip -c boot.img-ramdisk.gz | cpio -i -D ramdisk/
# 20342 blocks
```

## Repack bootimg
```bash
cd ramdisk
find . | cpio -H newc -o | gzip > ../boot.img-ramdisk-new.gz
# 20342 blocks

cd ..
mkbootimg \
    --kernel boot.img-zImage \
    --ramdisk boot.img-ramdisk-new.gz \
    --dt boot.img-dtb \
    --base 10000000 \
    --pagesize 2048 \
    --hash sha1 \
    --kernel_offset 00008000 \
    --ramdisk_offset 01000000 \
    --second_offset 00f00000 \
    --tags_offset 00000100 \
    --os_version 8.0.0 \
    --os_patch_level 2019-02 \
    --output ../boot-new.img

cd ..
file boot-new.img
# boot-new.img: Android bootimg, kernel (0x10008000), ramdisk (0x11000000), page size: 2048
```
