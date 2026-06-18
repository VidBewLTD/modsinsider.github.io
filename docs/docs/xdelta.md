# xDelta Patching Explained

## What is xDelta?

xDelta is a tool that stores only the *differences* between the original ROM and the modified version. This makes patch files incredibly small compared to distributing entire ROM files.

## Why xDelta?

- **Small File Size** - Patches are often <100MB vs 700MB+ for full ROMs
- **Legal** - Distributes changes, not copyrighted material
- **Easy to Use** - Simple graphical tools available

## How to Apply Patches

### Using DeltaPatcher (Recommended)

1. Download [DeltaPatcher](https://www.romhacking.net/utilities/704/)
2. Open DeltaPatcher
3. Click "Choose Source File..." and select your original ROM
4. Click "Choose Patch File..." and select the .xdelta file
5. Choose output location for your new ROM
6. Click "Apply Patch"
7. Wait for completion (can take a few minutes)

### Using Command Line (Windows/Mac/Linux)

```bash
xdelta3 -d -s original_rom.iso patched_rom.iso patch.xdelta
```

Replace:
- `original_rom.iso` with your original ROM filename
- `patched_rom.iso` with desired output filename
- `patch.xdelta` with the patch filename

## Troubleshooting

**"Checksum mismatch" error?**
- Your original ROM may be corrupted
- Download/extract the patch again
- Ensure ROM is unmodified

**Patch stuck or running slowly?**
- DeltaPatcher can take 5-15 minutes
- Do not close the program
- Ensure you have enough disk space (3-4GB free)

**Still having issues?**
- Check the original ROM is the correct version
- Try a different patching tool
- Ask for help in the community
