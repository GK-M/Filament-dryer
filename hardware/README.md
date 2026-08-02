# Hardware

Full documentation: **[English](../docs/en/hardware.md)** · **[Polski](../docs/pl/sprzet.md)**

```
v1/cad/   Autodesk Inventor parts (.ipt) and assemblies (.iam),
          plus neutral STEP (.stp) exports and sliced 3MF files.
v2/stl/   Printable STL files from version 2.
```

## Print settings

| Part | Material | Notes |
|---|---|---|
| Frame, corners, brackets | PETG | ~1 kg total for the full set |
| Display mount (`screen_mount.stl`, `screen_v2.stl`) | **ABS** | sits near the warm chamber wall; PETG softens there |
| Base (`base.stl`) | PETG | v2 perforation pattern — improves airflow and warm-up time |

## A note on filenames

The Inventor files keep their original Polish names. Inventor assemblies reference their component
parts *by filename*, so renaming any `.ipt` would break every `.iam` that uses it. An English
translation table is in the [hardware documentation](../docs/en/hardware.md#cad-sources--hardwarev1cad).

If you only want to print, use the `.stp` and `.3mf` files — they stand alone.
