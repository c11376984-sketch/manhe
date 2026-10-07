# Published source bundle

`sources.bundle` is generated from `app/src/main/assets/sources.json`.

Current format is **v2**: `MRSB` + version `0x02` + 12-byte random IV +
AES-256-GCM(JSON). The key lives in the App
(`app/src/main/java/com/manga/reader_native/data/SecretBox.kt`) and in
`tools/pack_sources.js` / `tools/sign_script.js`; change one, change all three.

The App still accepts:

- plain JSON (no `MRSB` header), and
- **v1** bundles: `MRSB` + `0x01` + reverse(XOR(zlib(JSON))), which is
  obfuscation only, kept so previously published links keep importing.

Pack / decode (Node):

```bash
node tools/pack_sources.js app/src/main/assets/sources.json sources/sources.bundle
node tools/pack_sources.js sources/sources.bundle sources/sources.json --decode
node tools/pack_sources.js sources/sources.json sources/sources.bundle --v1   # legacy
```

The Python twin (`tools/pack_sources.py`) still handles v1 only; v2 needs AES.

Scripts (`scripts/*.json`) are `{name, sourceId, version, scriptEnc, signature}`:
`scriptEnc` is base64(12-byte IV || AES-256-GCM(js || tag)) and `signature` is
HMAC-SHA256 over the canonical (pretty, 4-space, unescaped) shell minus the
signature. Re-encrypt / re-sign with:

```bash
node tools/sign_script.js scripts/xfy.json --encrypt
node tools/sign_script.js scripts/xfy.json --verify
```

Both layers stop casual inspection only: the client must decrypt the payload to
use it, so anyone who reverses the App can recover the key.