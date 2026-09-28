# PS3 Save Manager

Native C# / WPF (.NET 8) PS3 save manager.

## Main workflow

1. Browse a USB drive, `PS3\\SAVEDATA` folder, or individual save.
2. Select a user profile `PARAM.SFO` once. The path is remembered per Windows user.
3. Use **Decrypt Save** to remove the PS3 secure-file layer.
4. Inspect or decode payloads with **Save Crypto Lab** when needed.
5. Use **Encrypt + Resign** to apply the remembered profile, encrypt protected payloads, rebuild `PARAM.PFD`, and verify the result.
6. Use **Undo Last Change** to restore the latest automatic backup.

## Included

- Native WPF interface and application icon/logo.
- `pfdtool.exe` integration for decrypt, encrypt, update/resign, and verify.
- `Config/games.conf` and `Config/global.conf` as external runtime configuration.
- `global.conf` has the requested all-zero 32-digit `console_id`.
- Automatic ZIP backups before destructive operations.
- Undo for save operations and in-session Undo Delete.
- Safe copy/paste and replacement rollback.
- Safe `ICON0.PNG` loading: unsupported/corrupt artwork falls back to no thumbnail instead of crashing while scrolling.
- Save Crypto Lab entropy/header analysis and reversible GZip, zlib, and Base64 decoding.
- Decoded Crypto Lab output is stored outside the live save folder so it cannot accidentally become a PS3 payload.
- Multiple `secure_file_id` rules per title are parsed and cached from `games.conf`.

## Build

Install the .NET 8 SDK or newer, then run:

```bat
BUILD_X64.cmd
```

Self-contained output:

```text
publish\\win-x64
```

For a smaller framework-dependent build:

```bat
BUILD_FRAMEWORK_DEPENDENT.cmd
```

You can also open `PS3 Save Manager.sln` in Visual Studio 2022.

## Runtime files

The published directory must contain:

```text
PS3SaveManager.exe
pfdtool.exe
Config\\games.conf
Config\\global.conf
Config\\second_layer_rules.json
Config\\checksum_rules.json
```

At runtime, the manager stages `pfdtool.exe`, `games.conf`, and `global.conf` into a per-user LocalAppData runtime directory. This avoids failures when the application is installed in a read-only folder.

## Validation

Run:

```text
python validate_source.py
```

The validator checks XAML/XML, event-handler bindings, C# lexical structure, required runtime files, JSON config, the BLES01897 secure-file ID, the all-zero console ID, publish rules, and renamed build paths.

The current execution environment does not contain the .NET SDK, so the authoritative C# compile remains `BUILD_X64.cmd` on Windows.
