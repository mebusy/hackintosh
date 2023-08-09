

# 0D/6D 补丁

- SSDT-GPRW.aml
    - 补丁文件
    - 重写 `(GPRW, 2, NotSerialized)` 方法
- GPRW_UPRW-Renames.plist 
    - 应用 SSDT-GPRW.aml 补丁，需要先 [GU]PRW -> XPRW 命名
    - DELL 使用 GPRW -> XPRW
        ```
        // In config ACPI, GPRW to XPRW
        // Find:     47505257 02
        // Replace:  58505257 02
        ```


