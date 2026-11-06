# mike-odom/herdr-app

A personal fork of [herdrdev/herdr-app](https://github.com/herdrdev/herdr-app) for running a few not-yet-merged fixes
and local mods.

## Branches

- `master`: tracks `herdrdev/herdr-app` (the `upstream` remote). Do not commit here.
- `mike/main`: `master` plus the changes below. This is the branch to build and run.
- `fix/release-terminal-images`: the `src/terminal.rs` half of upstream PR
  [#25](https://github.com/herdrdev/herdr-app/pull/25) (fixes [#22](https://github.com/herdrdev/herdr-app/issues/22)):
  terminal images are dropped from the GPU sprite atlas when they are replaced or the pane closes. Without it
  `herdr-app` kept one GPU copy of every painted image (88 GB of GTT after a 2 h 22 min browser session).
- `fix/kitty-image-files`: upstream PR [#210](https://github.com/herdrdev/herdr-app/pull/210): programs on this
  machine can send Kitty images as files, which makes `terminal-browser` smooth.

Each commit keeps its original author. Drop a commit from `mike/main` once its PR is merged upstream.

## Build (Linux, x86-64)

Needs Rust 1.95.0, Zig 0.16.0 and these packages (the versions CI uses):

    sudo apt-get install -y --no-install-recommends pkg-config clang libclang-dev libxcb1-dev libxkbcommon-dev \
      libxkbcommon-x11-dev libwayland-dev libvulkan-dev mesa-vulkan-drivers libfontconfig-dev libfreetype-dev
    curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y --profile minimal --default-toolchain 1.95.0 --no-modify-path
    # Zig 0.16.0: https://ziglang.org/download/ (verify the sha256 from index.json), then put `zig` on PATH

    git clone --recurse-submodules -b mike/main git@github.com:mike-odom/herdr-app.git
    cd herdr-app && scripts/install-linux     # builds release, points ~/.local/bin/herdr-app at this checkout

A source build does not update itself. Quit and reopen Herdr after installing. To go back to the official release:
`ln -sfn ~/.local/share/herdr-app/herdr-app ~/.local/bin/herdr-app`.

## Taking upstream changes

    git fetch upstream && git checkout master && git merge --ff-only upstream/master
    git checkout mike/main && git rebase master      # fix conflicts, then rebuild and run cargo test --locked
