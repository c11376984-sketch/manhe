# Published source bundle

`sources.bundle` is generated from `app/src/main/assets/sources.json`.

The file is compressed, byte-scrambled, and reversed. The App accepts either a
plain JSON source list or this MRSB bundle when importing from a URL.

Pack:

```bash
python3 tools/pack_sources.py app/src/main/assets/sources.json sources/sources.bundle
```

Decode for verification:

```bash
python3 tools/pack_sources.py sources/sources.bundle sources/sources.json --decode
```

This format is obfuscation, not strong encryption. Anyone who can reverse the
App can recover the transform key and rewrite the bundle.
