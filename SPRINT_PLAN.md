# Matrix Shader Improvement Sprint Plan

**Created:** January 8, 2026
**Execution Method:** Subagent-Driven Development
**Goal:** Authentic Matrix-style characters + Better controls

---

## Architecture Overview

### Character Rendering System (HLSL)

**Problem:** Current implementation uses random 3x5 bit patterns - looks nothing like The Matrix.

**Solution:** Bit-packed glyph bitmaps in pure HLSL

```
Current (random noise):          Target (Katakana-style):
  ░▒░                              ██░██
  ▒░▒                              ░░█░░
  ░▒░                              ░░█░░
  ▒░▒                              ░█░█░
  ░▒░                              █░░░█
```

**Data Structure:**
- 5x7 pixel grid per character = 35 bits
- Pack into uint32 constants
- Store 16 glyphs (0-15) covering: カタカナ-style + numbers + symbols
- Select glyph via: `glyph_index = floor(random(seed) * 16)`

**Glyph Encoding (5 columns × 7 rows, bottom-to-top, right-to-left):**
```hlsl
// Each uint encodes a 5x7 glyph as bitmask
// Bit 0 = bottom-right, Bit 34 = top-left
static const uint GLYPHS[16] = {
    0x1F8C63F,  // ア-style
    0x1F2109F,  // イ-style
    0x1E1E1E1,  // ウ-style
    // ... 13 more glyphs
};
```

### Control Panel System (PowerShell)

**Problem:** Basic key presses, no visual feedback, confusing UX.

**Solution:** Enhanced TUI with:
- ANSI color swatch showing current RGB
- Help screen overlay (H key)
- Named presets display
- Parameter value bars
- Reset to defaults (0 key)

---

## Task Breakdown

### Task 1: Design Katakana-Style Glyphs
**Type:** Research + Design
**Independent:** Yes
**Deliverable:** 16 glyph bitmaps as uint32 constants

Create 16 Matrix-authentic glyphs:
- 8 Katakana-inspired characters
- 4 numbers (stylized)
- 4 symbols (░, │, ─, etc.)

Each glyph: 5 wide × 7 tall pixel grid
Output: HLSL constant array with visual comments

**Verification:** Visual inspection of rendered glyphs

---

### Task 2: Implement Glyph Renderer in HLSL
**Type:** Implementation
**Depends on:** Task 1
**Deliverable:** Updated DrawLayer() function

Replace lines 31-35 in Matrix.hlsl:
```hlsl
// OLD: Random bits
float glyph = step(0.5, random(bit_pos + char_seed));

// NEW: Lookup from glyph table
int glyph_idx = int(random(cell_id + floor(Time * 4.0)) * 16.0) % 16;
uint glyph_bits = GLYPHS[glyph_idx];
int bit_index = int(bit_pos.x) + int(bit_pos.y) * 5;
float glyph = float((glyph_bits >> bit_index) & 1u);
```

Changes:
- Add GLYPHS constant array at top
- Change char_res from (3,5) to (5,7)
- Update glyph sampling to use bit lookup
- Adjust character cell sizing

**Verification:**
- Shader compiles without errors
- Characters display as designed glyphs (not random)
- Animation still works (glyphs change over time)

---

### Task 3: Sync Template in PowerShell
**Type:** Implementation
**Depends on:** Task 2
**Deliverable:** Updated $shaderTemplate in matrix_tool.ps1

Copy the new HLSL code into the PowerShell template string.
Ensure hot-reload still works.

**Verification:**
- Run matrix_tool.ps1
- Verify shader regenerates correctly
- Verify characters match standalone Matrix.hlsl

---

### Task 4: Add ANSI Color Swatch Display
**Type:** Implementation
**Independent:** Yes
**Deliverable:** Visual color preview in control panel

Add to the display section:
```powershell
# Convert RGB floats to 0-255 for ANSI
$r = [int]([float]$s.R * 255)
$g = [int]([float]$s.G * 255)
$b = [int]([float]$s.B * 255)
$swatch = "$([char]27)[48;2;${r};${g};${b}m    $([char]27)[0m"
Write-Host " COLOR: $swatch R:$($s.R) G:$($s.G) B:$($s.B)"
```

**Verification:**
- Color swatch displays correct color
- Updates in real-time when RGB changes

---

### Task 5: Add Help Screen (H Key)
**Type:** Implementation
**Independent:** Yes
**Deliverable:** Toggle-able help overlay

Add 'H'/'h' to switch statement:
- Toggle $showHelp boolean
- When true, display full help overlay instead of normal UI
- Include all key bindings in organized table
- Press H again or any key to dismiss

**Verification:**
- H key toggles help screen
- All controls documented
- Returns to normal view

---

### Task 6: Enhance Preset Display
**Type:** Implementation
**Independent:** Yes
**Deliverable:** Named presets with color indicators

Replace current preset display:
```powershell
Write-Host " PRESETS:" -ForegroundColor DarkGreen
Write-Host " [1] Classic Green  [2] Cyber Blue   [3] Blood Red"
Write-Host " [4] Neon Purple    [5] Solar Gold   [6] Teal Cyan"
```

Add ANSI color squares next to each name.

**Verification:**
- Preset names visible
- Color indicators match preset colors

---

### Task 7: Add Reset to Defaults (0 Key)
**Type:** Implementation
**Independent:** Yes
**Deliverable:** Quick reset functionality

Add '0' to switch statement:
```powershell
'0' {
    $s = $defaults.Clone()
    Save-Shader
    Write-Host " Reset to defaults!" -ForegroundColor Yellow
}
```

**Verification:**
- Pressing 0 resets all parameters
- Shader updates to default green Matrix look

---

### Task 8: Integration Testing
**Type:** Testing
**Depends on:** Tasks 1-7
**Deliverable:** Verified working system

Test all features together:
- Glyphs render correctly at all 3 layer depths
- All color presets work
- All parameter adjustments work
- Help screen works
- Reset works
- Hot-reload works

**Verification:**
- Full manual test pass
- No regressions from original functionality

---

## Execution Order

```
Phase 1 (Parallel):
  Task 1: Design Glyphs
  Task 4: ANSI Color Swatch
  Task 5: Help Screen
  Task 6: Preset Display
  Task 7: Reset Key

Phase 2 (Sequential):
  Task 2: Implement Glyph Renderer (needs Task 1)
  Task 3: Sync PowerShell Template (needs Task 2)

Phase 3:
  Task 8: Integration Testing (needs all)
```

---

## Files to Modify

| File | Tasks | Changes |
|------|-------|---------|
| Matrix.hlsl | 1, 2 | Add GLYPHS array, update DrawLayer() |
| matrix_tool.ps1 | 3, 4, 5, 6, 7 | Update template, add UI features |

---

## Success Criteria

1. **Characters look like The Matrix** - Recognizable Katakana-style glyphs
2. **Better UX** - Color swatch, help screen, named presets, reset key
3. **No regressions** - All existing features still work
4. **Formally verifiable** - Glyph lookup has no OOB access

---

## Risk Mitigation

| Risk | Mitigation |
|------|------------|
| HLSL array indexing issues | Use modulo to clamp index: `idx % 16` |
| Template sync errors | Diff Matrix.hlsl vs template after changes |
| Performance regression | Test on multiple GPUs before release |
| ANSI escape not supported | Fallback to plain text if $Host.UI doesn't support |

---

*Plan ready for Subagent-Driven Development execution*
