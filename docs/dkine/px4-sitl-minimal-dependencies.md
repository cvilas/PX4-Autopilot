# PX4 SITL with Minimal External Dependencies

- Runs PX4 SITL natively on Linux X86_64 and AARCH64
- Uses PX4's built-in Software-in-the-Loop (SIH) simulator.
- SIH runs the vehicle physics inside the PX4 process, so it does not require an external simulator such as Gazebo.

## Host and Python dependencies

Install the normal native build tools for your distribution: `make`, CMake, Ninja, a C++ compiler, and Python 3. Then create a virtual environment and install PX4's Python requirements from the repository root:

```sh
python3 -m venv .venv
. .venv/bin/activate
python -m pip install --upgrade pip
python -m pip install -r Tools/setup/requirements.txt
```

> The requirements file constrains EmPy to `>=3.3,<4`. EmPy 4.x is incompatible with PX4's template generators.

## Build and run SIH

From the repository root, with the virtual environment active:

```sh
make px4_sitl_sih sihsim_quadx
```

This builds and starts PX4 SITL with SIH's quadrotor-X model. The simulator runs headless by default. QGroundControl can connect over UDP port 14550 and show the vehicle on its map.

Other SIH models and their support status are listed in the [SIH documentation](../en/sim_sih/index.md). The quadrotor is the stable model; the other listed vehicle types are experimental.

## Building NuttX board firmware

SITL does not need an embedded cross-compiler. Building firmware for a flight controller does: for example, the `px4_fmu-v6c_default` target requires the `arm-none-eabi` GCC toolchain. Fedora 44 provides ARM64-hosted packages for it:

```sh
sudo dnf install arm-none-eabi-gcc-cs arm-none-eabi-gcc-cs-c++ arm-none-eabi-binutils-cs arm-none-eabi-newlib
arm-none-eabi-gcc --version
arm-none-eabi-g++ --version
```

These tools run natively on aarch64 and generate code for the board's Cortex-M7 processor. The `arm-none-eabi-newlib` package also supplies `nosys.specs`, which the PX4 NuttX toolchain uses during compiler checks. After installing the toolchain, build the board target:

```sh
make px4_fmu-v6c_default
```

To open the board configurator instead, use `make px4_fmu-v6c_default boardconfig`.

