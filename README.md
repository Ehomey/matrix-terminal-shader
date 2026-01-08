# Matrix Terminal Shader

**Real-time controllable Matrix rain effect for Windows Terminal**

Transform your terminal into the iconic Matrix digital rain with live parameter controls. Perfect for multi-AI-agent workflows, streaming setups, or just looking cool.

## Features

- **Real-time controls** - Adjust colors, speed, density, and more without restarting
- **6 color presets** - Classic green, cyber blue, blood red, neon purple, solar gold, teal cyan
- **3 depth layers** - Toggle far, mid, and near rain layers independently
- **Hot-reload** - Changes apply instantly via Windows Terminal's shader system
- **Zero dependencies** - Just 2 files, pure PowerShell + HLSL
- **Low overhead** - GPU-accelerated, minimal CPU impact

## Requirements

- Windows Terminal 1.12+ (with pixel shader support enabled)
- Windows 10/11
- PowerShell 5.1+
- GPU with DirectX 11 support

## Quick Start

1. Copy `Matrix.hlsl` to your Documents folder
2. Enable shader in Windows Terminal settings (see Installation below)
3. Run `.\matrix_tool.ps1` to launch the control panel

## Installation

### Step 1: Copy Shader File

Copy `Matrix.hlsl` to:
```
C:\Users\<YourUsername>\Documents\Matrix.hlsl
```

### Step 2: Enable Shaders in Windows Terminal

1. Open Windows Terminal Settings (`Ctrl+,`)
2. Click **Open JSON file** (bottom left)
3. Find your profile and add the shader path:

```json
{
    "profiles": {
        "defaults": {},
        "list": [
            {
                "name": "PowerShell",
                "experimental.pixelShaderPath": "C:\\Users\\<YourUsername>\\Documents\\Matrix.hlsl"
            }
        ]
    }
}
```

4. Save and restart Windows Terminal

### Step 3: Run Control Panel

```powershell
.\matrix_tool.ps1
```

## Usage

### Key Bindings

| Key | Action | Range |
|-----|--------|-------|
| `1-6` | Color presets | See presets below |
| `7/8/9` | Toggle layers | Far/Mid/Near |
| `r/R` | Red channel | -/+ (0.0-1.0) |
| `g/G` | Green channel | -/+ (0.0-1.0) |
| `b/B` | Blue channel | -/+ (0.0-1.0) |
| `s/S` | Speed | -/+ (0.1-5.0) |
| `l/L` | Glow/Brightness | -/+ (0.2-10.0) |
| `w/W` | Character width | -/+ (4.0-20.0) |
| `t/T` | Trail length | -/+ (2.0-20.0) |
| `d/D` | Rain density | -/+ (0.1-1.0) |
| `q` | Quit | - |

### Color Presets

| Key | Name | RGB |
|-----|------|-----|
| `1` | Classic Green | 0.0, 1.0, 0.2 |
| `2` | Cyber Blue | 0.0, 0.5, 1.0 |
| `3` | Blood Red | 1.0, 0.0, 0.0 |
| `4` | Neon Purple | 0.8, 0.0, 1.0 |
| `5` | Solar Gold | 1.0, 0.8, 0.0 |
| `6` | Teal Cyan | 0.0, 1.0, 1.0 |

## Use Cases

### Multi-Agent Monitoring
Use different colors to identify different AI coding assistants:
- **Green** - Primary agent
- **Blue** - Code reviewer
- **Red** - Testing agent
- **Purple** - Documentation agent

### Streaming & Demos
Create eye-catching terminal visuals for:
- Live coding streams
- Technical presentations
- YouTube tutorials

## Troubleshooting

### Shader not appearing
1. Verify Windows Terminal version is 1.12+
2. Check the shader path in settings.json is correct
3. Ensure shader file exists at the specified location
4. Restart Windows Terminal after enabling

### Controls not working
1. Make sure `matrix_tool.ps1` is running
2. Check that the shader path in the script matches your file location
3. Verify you have write permissions to the shader file

### Performance issues
1. Try disabling one or two layers (keys 7, 8, 9)
2. Reduce rain density (key d)
3. Lower glow strength (key l)

### PowerShell execution policy
If the script won't run, try:
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```

## File Structure

```
Matrix.hlsl       - HLSL pixel shader (runs in Windows Terminal)
matrix_tool.ps1   - PowerShell control panel (real-time parameter adjustment)
```

## How It Works

The system uses Windows Terminal's pixel shader feature with a file-watching hot-reload mechanism:

1. `matrix_tool.ps1` captures keyboard input
2. User adjusts parameters via key presses
3. Script regenerates `Matrix.hlsl` with new values
4. Windows Terminal detects file change and reloads shader
5. Effect updates in real-time

## Support

If you find this useful, consider buying me a coffee!

<!-- Add your Buy Me a Coffee link here -->

## License

MIT License - See [LICENSE](LICENSE) file

## Contributing

Contributions welcome! Please open an issue or PR on GitHub.

---

*Inspired by The Matrix (1999) - "There is no spoon."*
