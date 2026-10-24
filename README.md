# kulisse

Device and scene models for arkitekt. A model is one **self-contained binary glTF** (GLB) with
an optional joint table: what a viewer draws as a `<Model>` or drives as a `<Robot>`, what an
app registers as an asset, and what a server keeps in a `KulisseStore`.

kulisse is strictly the authoring side, like fabriks for meshes: no network code, no storage
opinions. The **format** is the contract ([`KULISSE.md`](KULISSE.md)), and the server reads it
with its own implementation (`arkitekt_datalayer.kulisse`), never by importing this package.

```bash
pip install kulisse            # check, reference, snippet: standard library only
pip install "kulisse[urdf]"    # import URDF / Xacro (numpy, trimesh, pycollada, xacro)

kulisse import robot.urdf.xacro --out robot.glb --package my_robot=./share/my_robot
kulisse check robot.glb
kulisse joints robot.glb joints.json --out robot.glb
kulisse snippet robot.glb --name robot
```

```python
import kulisse

report = kulisse.check("robot.glb")      # ModelReport, or ModelRefused(code, message)
kulisse.reference("robot.glb")           # "sha256:…", what a blok names as for_model
kulisse.snippet("robot.glb")             # '<Robot src="@self.assets.robot" … />'
```
