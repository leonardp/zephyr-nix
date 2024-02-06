# zephyr-nix

Develop Zephyr projects using Nix

## Features

- SDK packaging
- Host tools packaging

## Basic usage

- `shell.nix`

``` nix
{ mkShell
, zephyr
, callPackage
, cmake
, ninja
, lib
}:

mkShell {
  packages = [
    zephyr.pythonEnv
    # Use zephyr.hosttools-nix to use nixpkgs built tooling instead of official Zephyr binaries
    zephyr.hosttools
    cmake
    ninja
  ];

  env.ZEPHYR_SDK_INSTALL_DIR = zephyr.sdk.overrideAttrs(old: {
    targets = old.targets ++ [
      "arm-zephyr-eabi"
    ];
  });
}
```

## Flakes usage

- `flake.nix`
``` nix
{
  description = "A very basic Zephyr flake";

  inputs = {
    nixpkgs.url = "github:NixOS/nixpkgs/nixos-unstable";

    # Customize the version of Zephyr used by the flake here
    zephyr.url = "github:zephyrproject-rtos/zephyr/v3.5.0";
    zephyr.flake = false;

    zephyr-nix.url = "github:adisbladis/zephyr-nix";
    zephyr-nix.inputs.nixpkgs.follows = "nixpkgs";
    zephyr-nix.inputs.zephyr.follows = "zephyr";
  };

  outputs = { self, nixpkgs, zephyr-nix, ... }: let
    pkgs = nixpkgs.legacyPackages.x86_64-linux;
    zephyr = zephyr-nix.packages.x86_64-linux;
  in {
    devShell.x86_64-linux.default = pkgs.mkShell {
      # Use the same mkShell as documented above
    };
  };
}
```
