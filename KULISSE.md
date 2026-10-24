# The kulisse wire format, version 1

A kulisse model is one file. Writers (this package, any exporter) and readers (the server's
`KulisseStore`, the viewer) implement this document independently; none imports another.

## Container
- A **binary glTF 2.0** file: 12-byte header (`glTF`, version `2`, total length equal to the
  file's size), a JSON chunk first, then at most one BIN chunk. `asset.version` is `"2.0"`.
- **Self-contained**: no `uri` on any buffer or image. Everything is in the BIN chunk.
- Every `bufferView` lies inside its buffer, every accessor inside its view.

## Extensions
Required extensions must be among: `KHR_lights_punctual`, `KHR_materials_emissive_strength`,
`KHR_materials_variants`, `KHR_texture_transform`, `KHR_mesh_quantization`,
`EXT_meshopt_compression`, `KHR_draco_mesh_compression`. Others may be *used* (a reader warns
and ignores them) but never *required*.

## Nodes
Node names are how a blok addresses a part (`for_node`, a joint's `node`). They should be
unique and match `[a-z0-9_]+`; anything else is a warning, not a refusal.

## The joint table: `extras.arkitekt`, version 1
```json
{"extras": {"arkitekt": {"version": 1, "joints": [
  {"name": "shoulder", "node": "shoulder", "type": "revolute", "axis": [0, 0, 1],
   "limit": {"lower": -2.0, "upper": 2.0}},
  {"name": "grip_r", "node": "grip_r", "type": "prismatic", "axis": [1, 0, 0],
   "mimic": {"joint": "grip_l", "multiplier": -1, "offset": 0}}
], "source": "sha256:…"}}}
```
- `type` is `revolute`, `continuous` or `prismatic`; `axis` is a unit vector in the node's frame;
  `node` names an existing node; a `mimic` names another joint of the table.
- `source` (optional) is the reference of the file the model was made from.
- Any other `version` is refused.

## Reference
A model's reference is `sha256:` followed by the 64 lowercase hex digits of the SHA-256 of its
exact bytes. A blok decorating a model names it as `for_model`.

## Budgets (defaults; a server may configure its own)
| | |
|---|---|
| size | 50 MiB |
| JSON chunk | 4 MiB |
| nodes | 10 000 |
| triangles | 2 000 000 |
| animations | 64 |
| texture side / memory (client-checked) | 4096 px / 256 MiB |
