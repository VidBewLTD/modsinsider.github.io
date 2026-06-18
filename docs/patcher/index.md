# Smash Remix Patcher Tool

Easily patch your ROMs with your favorite mods directly in your browser!

<div id="patcher-container" style="background: #2a2a2a; padding: 20px; border-radius: 8px; margin: 20px 0;">

<form id="patcher-form">
  <div style="margin-bottom: 15px;">
    <label for="mod-select" style="display: block; margin-bottom: 5px; font-weight: bold;">Select Mod:</label>
    <select id="mod-select" style="width: 100%; padding: 10px; border-radius: 4px; border: 1px solid #666; background: #1a1a1a; color: #fff;">
      <option value="">Choose a mod...</option>
      <option value="redux">Smash Remix Redux</option>
      <option value="slippy">Slippy Mod (0.9.7)</option>
      <option value="projectx">Project X (2022-2023)</option>
      <option value="spiderman">Spiderman Mod</option>
      <option value="shino">Shino Mod</option>
      <option value="knuckles">Smash Remix and Knuckles</option>
      <option value="galleon">Project Galleon</option>
    </select>
  </div>

  <div style="margin-bottom: 15px;">
    <label for="version-select" style="display: block; margin-bottom: 5px; font-weight: bold;">Version:</label>
    <select id="version-select" style="width: 100%; padding: 10px; border-radius: 4px; border: 1px solid #666; background: #1a1a1a; color: #fff;" disabled>
      <option value="">Select a mod first</option>
    </select>
  </div>

  <div style="margin-bottom: 15px;">
    <label for="rom-input" style="display: block; margin-bottom: 5px; font-weight: bold;">Upload Your ROM:</label>
    <input type="file" id="rom-input" accept=".iso,.gcm,.bin" style="width: 100%; padding: 10px; border-radius: 4px; border: 1px solid #666; background: #1a1a1a; color: #fff;">
    <small style="display: block; margin-top: 5px; color: #aaa;">Supported: .iso, .gcm, .bin</small>
  </div>

  <div id="status" style="margin-bottom: 15px; padding: 10px; border-radius: 4px; display: none; background: #333;">
    <span id="status-text"></span>
  </div>

  <button type="submit" style="width: 100%; padding: 12px; border-radius: 4px; border: none; background: #ff006e; color: white; font-weight: bold; font-size: 16px; cursor: pointer;">
    🔧 Patch ROM
  </button>
</form>

</div>

<script src="https://unpkg.com/xdelta3-wasm@0.4.1/dist/xdelta3.js"></script>
<script>
const modData = {
  redux: {
    name: 'Smash Remix Redux',
    versions: [
      { name: 'Latest', patch: 'redux-latest.xdelta' }
    ]
  },
  slippy: {
    name: 'Slippy Mod',
    versions: [
      { name: 'v1.0', patch: 'slippy-v1.0.xdelta' }
    ]
  },
  projectx: {
    name: 'Project X',
    versions: [
      { name: '2023', patch: 'projectx-2023.xdelta' },
      { name: '2022', patch: 'projectx-2022.xdelta' }
    ]
  },
  spiderman: {
    name: 'Spiderman Mod',
    versions: [
      { name: 'Latest', patch: 'spiderman-latest.xdelta' }
    ]
  },
  shino: {
    name: 'Shino Mod',
    versions: [
      { name: 'Latest', patch: 'shino-latest.xdelta' }
    ]
  },
  knuckles: {
    name: 'Smash Remix and Knuckles',
    versions: [
      { name: 'Latest', patch: 'knuckles-latest.xdelta' }
    ]
  },
  galleon: {
    name: 'Project Galleon',
    versions: [
      { name: 'Latest', patch: 'galleon-latest.xdelta' }
    ]
  }
};

const modSelect = document.getElementById('mod-select');
const versionSelect = document.getElementById('version-select');
const romInput = document.getElementById('rom-input');
const form = document.getElementById('patcher-form');
const statusDiv = document.getElementById('status');
const statusText = document.getElementById('status-text');

modSelect.addEventListener('change', () => {
  const selectedMod = modSelect.value;
  versionSelect.innerHTML = '<option value="">Select version</option>';

  if (selectedMod && modData[selectedMod]) {
    const versions = modData[selectedMod].versions;
    versions.forEach(v => {
      const option = document.createElement('option');
      option.value = v.patch;
      option.textContent = v.name;
      versionSelect.appendChild(option);
    });
    versionSelect.disabled = false;
  } else {
    versionSelect.disabled = true;
  }
});

form.addEventListener('submit', async (e) => {
  e.preventDefault();

  if (!modSelect.value) {
    showStatus('Please select a mod', 'error');
    return;
  }

  if (!versionSelect.value) {
    showStatus('Please select a version', 'error');
    return;
  }

  if (!romInput.files.length) {
    showStatus('Please select a ROM file', 'error');
    return;
  }

  const rom = romInput.files[0];
  const patchFile = versionSelect.value;
  const modName = modSelect.options[modSelect.selectedIndex].text;

  showStatus('Downloading patch file...', 'info');

  try {
    // Fetch the patch file
    const patchResponse = await fetch(`/patches/${patchFile}`);
    if (!patchResponse.ok) {
      showStatus('Patch file not found. Please check the mod page for manual patching.', 'error');
      return;
    }
    const patchBuffer = await patchResponse.arrayBuffer();

    // Read ROM file
    const romBuffer = await rom.arrayBuffer();

    showStatus('Patching ROM... This may take a minute or two.', 'info');

    // Initialize xdelta3
    const xdelta = await Xdelta3();

    // Apply patch
    const patchedRom = xdelta.decode(romBuffer, patchBuffer);

    // Download patched ROM
    const blob = new Blob([patchedRom], { type: 'application/octet-stream' });
    const url = URL.createObjectURL(blob);
    const a = document.createElement('a');
    a.href = url;
    a.download = `${modName.replace(/\s+/g, '_')}_patched.iso`;
    document.body.appendChild(a);
    a.click();
    document.body.removeChild(a);
    URL.revokeObjectURL(url);

    showStatus('✓ ROM patched successfully! Download started. Ready to play!', 'success');
  } catch (err) {
    console.error(err);
    showStatus('Error: ' + err.message + '. Patch files may not be available yet. Check the mod pages.', 'error');
  }
});

function showStatus(message, type) {
  statusText.textContent = message;
  statusDiv.style.display = 'block';

  if (type === 'error') {
    statusDiv.style.background = '#6b2a2a';
    statusDiv.style.color = '#ff6b6b';
  } else if (type === 'success') {
    statusDiv.style.background = '#2a6b2a';
    statusDiv.style.color = '#6bff6b';
  } else {
    statusDiv.style.background = '#2a4a6b';
    statusDiv.style.color = '#6bddff';
  }
}
</script>

---

## How It Works

1. **Select a Mod** - Choose from our collection of Smash Remix mods
2. **Pick a Version** - Select the version you want
3. **Upload Your ROM** - Provide your clean Super Smash Bros. Melee ROM
4. **Patch** - Click the patch button and wait for completion

The patched ROM will download automatically when complete!

## Need Help?

- [Installation Guide](../docs/installation.md)
- [xDelta Patching Tutorial](../docs/xdelta.md)
- [Troubleshooting](../docs/troubleshooting.md)
