# TWRP 基座补丁

`twrp16-vold-empty-metadata-encryption.patch`

打给 `system/vold/MetadataCrypt.cpp` 的 `parse_options()`：Android 12 及更早的 vendor fstab
只用 `fileencryption=...:v2+inlinecrypt_optimized,keydirectory=...` 表示 metadata 加密，
不带 `metadata_encryption=` 字段。TWRP-16.0 的 vold 在 v2 路径下会强制解析该字段，
`Split("")` 得到空 cipher 后直接返回 false，日志表现为 `Unable to decrypt metadata encryption`。

补丁在空串时回退到默认 `aes-256-xts`（不启用 HW-wrapped key），匹配 A12 keymaster 4.1 的
legacy storage key。

应用（在 TWRP 源码树里）：

```bash
cd /cannon/twrp-a17/system/vold
git apply /path/to/patches/twrp16-vold-empty-metadata-encryption.patch
```

重新 sync TWRP 基座后需要重新打一次。
