# Software-in-the-loop (SITL) Simulation

This workflow sets up the development environment to run PX4 SITL natively on Linux, including
ARM64 hosts, using PX4's built-in Software-in-the-Loop (SIH) simulator. SIH runs the vehicle
physics inside the PX4 process, so it does not require an external simulator such as Gazebo.

## Setup developer environment

- Install developmemt toolchain
  - On [Ubuntu](https://docs.px4.io/main/en/dev_setup/dev_env_linux_ubuntu)
    ```sh
    sudo apt-get update
    sudo apt-get install -y software-properties-common lsb-release ca-certificates sudo wget gnupg \
    build-essential cmake make gcc g++ git curl ninja-build ccache python-is-python3 python3-dev \
    python3-pip python3-setuptools python3-wheel lcov libssl-dev libxml2-dev libxml2-utils \
    g++-multilib gcc-arm-none-eabi gcc-multilib
    ```
  - On Fedora
    ```sh
    sudo dnf install -y cmake git gcc g++ arm-none-eabi-gcc-cs arm-none-eabi-gcc-cs-c++ \
    arm-none-eabi-binutils-cs arm-none-eabi-newlib
    ```

## Software in the loop (SITL) simulation

- Download and install QGroundControl [from here](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/getting_started/download_and_install.html)
- Build and start PX4 SITL with SIH's quadrotor-X model:
  ```sh
  # clone the repo
  git clone https://github.com/PX4/PX4-Autopilot.git --recursive
  cd PX4-Autopilot

  # create virtual environment with python dependencies
  python3 -m venv .venv
  . .venv/bin/activate
  python -m pip install --upgrade pip
  python -m pip install -r Tools/setup/requirements.txt

  # build and start SITL sim
  make px4_sitl_sih sihsim_quadx
  ```
  The simulator runs headless by default.
- Build and start [Hawkeye](https://px4.github.io/Hawkeye/) visualiser
  ```sh
  # install additional graphics dependencies
  sudo apt-get install -y libgl1-mesa-dev libx11-dev libxrandr-dev libxinerama-dev libxcursor-dev \
  libxi-dev

  # clone the repo
  git clone --recursive https://github.com/PX4/Hawkeye.git
  cd Hawkeye

  # build visualiser
  cmake -S . -B build
  cmake --build build --parallel

  # start visualiser. It will connect with PX4 SITL over mavlink
  ./build/hawkeye
  ```
- Start `QGroundControl` to connect over UDP port 14550 and show the vehicle on its map.
- Refer to `QGroundControl` [user guide](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/getting_started/quick_start.html) to
  - Fly manually in `Position` mode
  - Run a simple mission

## Build board firmware

- Building firmware for a flight controller requires a cross-compiler
  ```sh
  make px4_fmu-v6c_default
  ```
- To open the board configurator instead
  ```sh
  make px4_fmu-v6c_default boardconfig
  ```

## References

- [Ubuntu setup](https://docs.px4.io/main/en/dev_setup/dev_env_linux_ubuntu)
- [SIH documentation](../en/sim_sih/index.md).
