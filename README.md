# Dusklight Mod Template

A standalone template for [Dusklight](https://github.com/TwilitRealm/dusklight) mods.

See the [Dusklight modding documentation](https://github.com/TwilitRealm/dusklight/blob/main/docs/modding.md)
for the full mod API: services, hooking game functions, asset overlays, and more.

## Replacing Dawnlight's Glider icon

The editable example is
[`textures/tex1_128x128_6e4659042d5d2d18_6.png`](textures/tex1_128x128_6e4659042d5d2d18_6.png).
It replaces Dawnlight's **2D Glider acquisition icon**, shown when Link receives
and holds up the Glider. The 3D model and its canvas/wood/leather textures are
supplied separately by `overlay/res/Object/DawnlightGlider.bmd`.

1. Edit or replace the example PNG in `textures/`. Keep its exact filename.
   Use a square RGBA PNG with a transparent background; the example is 256×256,
   and a higher-resolution square image works too.
2. Build this project as usual. `TEXTURES_DIR textures` in `CMakeLists.txt`
   automatically includes the PNG under `textures/` inside the `.dusk` archive.
   The combined GitHub Actions bundle also includes it.
3. Install the rebuilt `.dusk` alongside Dawnlight, enable both mods, and restart
   the game. View the Glider acquisition message to see your replacement.
   Existing saves do not automatically replay that message.

The example deliberately starts with the original Dawnlight icon, so its design
stays familiar until you edit the PNG. This is a mod-bundled texture replacement;
you do not need to copy it into the user's `texture_replacements/` folder.
`res/icon.png` would change this mod's manager icon instead. Putting a copy of
`glider-item-icon.rgba8` in this mod's `res/` cannot override Dawnlight's private
resource, and replacing the BMD alone does not replace the 2D icon.

### Source and texture filename

The example PNG is copied unchanged from
[Dawnlight's Glider artwork](https://github.com/BeZide93/dawnlight/blob/16b693be6de8fb1389afafc27f4ba093e1c253e2/art/glider/glider-item-icon.png).
The key matches `res/glider-item-icon.rgba8` at that same Dawnlight revision:

- Original runtime size: **128×128**, format **GX RGBA8 (6)**, one mip level.
- XXH64 (seed 0) of the 65,536 raw GX-tiled pixel bytes: **`6e4659042d5d2d18`**.
- Replacement filename: **`tex1_128x128_6e4659042d5d2d18_6.png`**.

The filename identifies the original runtime texture, not the replacement PNG's
size or file hash. Keep it unchanged when editing the example. If Dawnlight
changes its built-in icon pixels or format, the matching filename must be updated.
If multiple enabled mods replace this same texture, Dusklight's mod priority
selects the winner.

## Quick start

1. Click "Use this template" to create a new repository for your mod.
2. Edit `mod.json.in`: set your mod's `id` (reverse-DNS style, e.g. `com.example.my_mod`),
   `name`, `author`, and `description`.
3. Rename the target in `CMakeLists.txt` (`add_mod(my_mod ...)`) (this names the `.dusk` file).
4. Write your mod in `src/mod.cpp`.
5. Build locally:
   ```sh
   cmake -B build
   cmake --build build
   ```

The result is `build/mods/<name>.dusk`. Copy it into the game's mods folder to try it:

- Windows: `%APPDATA%\TwilitRealm\Dusklight\mods`
- Linux: `~/.local/share/TwilitRealm/Dusklight/mods`
- macOS: `~/Library/Application Support/TwilitRealm/Dusklight/mods`

During development, rebuild, copy and click **Reload** in the in-game mod manager to pick up changes.

> [!IMPORTANT]
> A mod built locally will only be valid for your own platform, and shouldn't be distributed.
> The repository will build a [cross-platform bundle](#github-actions) for distribution. See below.

## Updating to a new Dusklight version

Change the `DUSKLIGHT_VERSION` line in `CMakeLists.txt` to the new release tag (or commit hash) and reconfigure. The
pinned version is fetched into `dusklight/` automatically. Use the `dusklight/` checkout to browse game code, headers
and mod services.

> [!IMPORTANT]
> The Dusklight checkout is for **reference only**. Mods use
> [services](https://github.com/TwilitRealm/dusklight/blob/main/docs/modding.md#built-in-services) and
> [hooks](https://github.com/TwilitRealm/dusklight/blob/main/docs/modding.md#hooking-game-functions) to interact with
> game code.

## GitHub Actions

The included GitHub Actions workflow builds the mod for the following platforms:
- Windows (AMD64 & ARM64)
- macOS (Apple Silicon & Intel)
- iOS (Apple Silicon)
- Linux (x86_64 & aarch64)
- Android (aarch64)

It then merges the per-platform builds into a single `.dusk` supporting all platforms. (Artifact `mod-combined`) 

Pushing a tag to the repository creates a GitHub release with the combined bundle.

## For Dusklight developers

Point the build at an existing checkout instead of fetching one:

```sh
cmake -B build -DDUSKLIGHT_DIR=~/path/to/dusklight
```
