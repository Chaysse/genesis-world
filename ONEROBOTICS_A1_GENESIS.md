# OneRobotics A1 for Genesis World

**Community-maintained integration in a public Genesis World fork.** This project
is not part of the official Genesis curated asset library and is not an
endorsement by the Genesis maintainers.

The A1 integration lives on the `onerobotics_a1_assets` branch of
[`Chaysse/onerobotics-a1-genesis-world`](https://github.com/Chaysse/onerobotics-a1-genesis-world/tree/onerobotics_a1_assets).
The `onerobotics_a1_assets` branch is the default branch.

## Assets

- Right arm (7 DoF): `genesis/assets/urdf/onerobotics_a1/a1_r.urdf`
- Left arm (7 DoF): `genesis/assets/urdf/onerobotics_a1/a1_l.urdf`
- Bimanual stand (14 DoF): `genesis/assets/urdf/onerobotics_a1/bimanual_stand/a1_bimanual_stand.urdf`
- 33 referenced STL meshes and the CC BY 4.0 notice in `ATTRIBUTION.md`

The original A1 models are from
[`katazen/onerobot_h1`](https://github.com/katazen/onerobot_h1) at commit
`3db6f614c8443bd9396fcd9304cf64a519f98f2a`.

## Installation

Use Python 3.10–3.13 and follow the
[Genesis World installation instructions](https://github.com/Genesis-Embodied-AI/genesis-world#quick-installation),
including installing a compatible PyTorch build first. The example below uses
an existing Python environment; `uv` is **not** required.

```bash
git clone --branch onerobotics_a1_assets --single-branch https://github.com/Chaysse/onerobotics-a1-genesis-world.git
cd onerobotics-a1-genesis-world
python -m pip install -e .
```

## Run the demo

All commands are run from the `onerobotics-a1-genesis-world` directory.
The example only reads the URDF and STL assets; it does not modify the upstream
Franka examples or the OneRobotics robot descriptions.

```bash
# Check that the URDF and referenced mesh files exist; no Genesis simulation yet.
python examples/rigid/a1_genesis_demo.py --robot right --check-assets
python examples/rigid/a1_genesis_demo.py --robot left --check-assets
python examples/rigid/a1_genesis_demo.py --robot bimanual --check-assets

# Actual Genesis import, build and simulation with GUI.
python examples/rigid/a1_genesis_demo.py --robot right --demo view -v
python examples/rigid/a1_genesis_demo.py --robot left --demo view -v
python examples/rigid/a1_genesis_demo.py --robot bimanual --demo view -v

# The bimanual demo automatically aligns the stand support mesh with z=0.
# If you need to diagnose ground intersection, hide the plane temporarily:
python examples/rigid/a1_genesis_demo.py --robot bimanual --demo view -v --no-plane
# Or override the robot base height explicitly (meters, example only):
python examples/rigid/a1_genesis_demo.py --robot bimanual --demo view -v --robot-z 0.3

# Joint-control and IK demos validated for all three configurations.

# Right arm
python examples/rigid/a1_genesis_demo.py --robot right --demo control -v
python examples/rigid/a1_genesis_demo.py --robot right --demo ik -v

# Left arm
python examples/rigid/a1_genesis_demo.py --robot left --demo control -v
python examples/rigid/a1_genesis_demo.py --robot left --demo ik -v

# Bimanual stand
python examples/rigid/a1_genesis_demo.py --robot bimanual --demo control -v
python examples/rigid/a1_genesis_demo.py --robot bimanual --demo ik --arm both -v
```

`-v` opens the viewer; omit it to run headless. Use `--steps 800` for a longer
visual demonstration. Run `--help` to see the `--arm`, `--gpu`, and other options.

## Current validation status

Reported local execution (2026-10-15): Genesis 1.4.4, CPU backend, fp32.

- Right-arm rigid URDF import succeeded (7 DoFs and 8 referenced meshes).
- Right-arm position/velocity/force-control API examples executed. Position
  endpoint RMSE was about 0.0144 rad across seven joints, with a persistent
  J2 offset of approximately 0.038 rad. The velocity sample measured
  0.0991 rad/s for a target of 0.1 rad/s. The 0.5 Nm output is a reported
  controller command, not an independent torque-fidelity measurement.
- Right-arm IK visual demonstration succeeded for 300 internally reachable
  FK-derived targets, maximum observed end-link position error 0.000040 m.
- Left-arm URDF loading, joint control, and inverse kinematics
  have been successfully validated in Genesis.
- Bimanual stand URDF loading, joint control, and inverse
  kinematics have been successfully validated in Genesis.

File-existence checks (`--check-assets`) alone do not prove compatibility.
Complete and record Genesis import, control and IK results separately for
any configurations or capabilities described as validated.

**Known limitations:** independent-arm `Link7` temporary flange inertial
parameters; the bimanual source uses `effort="0"` and `velocity="0"`
placeholders, not hardware actuator specifications. The demo overrides runtime
force limits for control using published upstream motor metadata, but this is
not a validated physical controller or a claim of hardware fidelity.

## Licensing

The Genesis engine retains its upstream Apache-2.0 license. The OneRobotics A1
URDF/STL robot assets are CC BY 4.0; see
[`ATTRIBUTION.md`](genesis/assets/urdf/onerobotics_a1/ATTRIBUTION.md)
for copyright, source revision, and license terms.
