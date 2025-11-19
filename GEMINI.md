# Miryoku ZMK

## Project Overview

**Miryoku ZMK** is the ZMK firmware implementation of the [Miryoku](https://github.com/manna-harbour/miryoku) keyboard layout. It provides a universal, ergonomic, and minimal keymap that can be applied to a wide variety of keyboards supported by ZMK.

The project uses a sophisticated system of C preprocessor macros to abstract the physical layout of the keyboard, allowing the same logical keymap to be deployed across different hardware with minimal configuration.

### Key Components

*   **`config/`**: Contains the entry points for each supported keyboard. Each board has a corresponding `.keymap` file (e.g., `corne.keymap`) that includes the board-specific mapping and the core Miryoku definitions.
*   **`miryoku/`**: Holds the core logic of the layout.
    *   `miryoku.dtsi`: The main devicetree source include file that generates the ZMK keymap.
    *   `miryoku.h`: Header file defining the macros.
    *   `mapping/`: Defines how the logical layout maps to physical keys for specific boards (e.g., 36-key, 42-key layouts).
    *   `custom_config.h`: A file for user-specific overrides and configuration options.
*   **`.github/workflows/`**: CI/CD pipelines for building the firmware automatically using GitHub Actions.

## Building and Running

This project relies on the [ZMK build environment](https://zmk.dev/docs/development/setup), which uses the `west` meta-tool and the Zephyr RTOS build system.

### Local Build

1.  **Setup ZMK:** Ensure you have a working ZMK development environment set up.
2.  **Clone:** Clone this repository.
3.  **Configure:** Set the `ZMK_CONFIG` environment variable to the absolute path of the `config` directory in this repository.
4.  **Build:** Use `west build` from your ZMK workspace (usually `zmk/app`).

```bash
# Example: Building for the Corne keyboard
west build -b nice_nano_v2 -- -DSHIELD=corne_left -DZMK_CONFIG="/path/to/miryoku_zmk/config"
```

### Workflow Builds

The project is designed to be built primarily via GitHub Actions. You can fork the repository and use the "Build Inputs" workflow to generate firmware for your specific board without installing a local toolchain.

## Configuration

Configuration is primarily handled through C preprocessor definitions.

*   **`miryoku/custom_config.h`**: Use this file to set options like `MIRYOKU_ALPHAS_QWERTY`, `MIRYOKU_NAV_INVERTEDT`, etc.
*   **Board Configs**: standard Kconfig options (e.g., `CONFIG_ZMK_DISPLAY=y`) can be added to `config/<board>.conf`.

## Development Conventions

*   **Macro Magic**: The codebase heavily relies on C macros (`#define`) to generate code. When modifying the core layout, ensure you understand how these macros expand.
*   **Keymap Structure**: Board keymaps in `config/` should be minimal, strictly including the config, the mapping, and `miryoku.dtsi`. Avoid defining key codes directly in board files unless necessary for hardware-specific reasons.
*   **Out-of-Tree Boards**: Supported via the `outboards` mechanism defined in `.github/workflows/outboards/`, which handles cloning external repositories during the build.
