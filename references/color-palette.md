# Color Palette Reference

9 ramps × 7 levels. Load this file when generating SVG/HTML visuals to look up exact hex values.

## Level semantics

| Level | Meaning |
|-------|---------|
| 50 | Lightest fill (light theme container backgrounds) |
| 100-200 | Light fills (light theme inner regions, dark theme accents) |
| 400 | Midtone (rarely used directly) |
| 600 | Accent / stroke (light theme strokes, dark theme highlights) |
| 800 | Dark fill (dark theme container backgrounds) |
| 900 | Darkest (dark theme inner regions, rarely needed) |

## Full hex table

| Class | 50 | 100 | 200 | 400 | 600 | 800 | 900 |
|-------|----|-----|-----|-----|-----|-----|-----|
| c-purple | #EEEDFE | #CECBF6 | #AFA9EC | #7F77DD | #534AB7 | #3C3489 | #26215C |
| c-teal | #E1F5EE | #9FE1CB | #5DCAA5 | #1D9E75 | #0F6E56 | #085041 | #04342C |
| c-coral | #FAECE7 | #F5C4B3 | #F0997B | #D85A30 | #993C1D | #712B13 | #4A1B0C |
| c-pink | #FBEAF0 | #F4C0D1 | #ED93B1 | #D4537E | #993556 | #72243E | #4B1528 |
| c-gray | #F1EFE8 | #D3D1C7 | #B4B2A9 | #888780 | #5F5E5A | #444441 | #2C2C2A |
| c-blue | #E6F1FB | #B5D4F4 | #85B7EB | #378ADD | #185FA5 | #0C447C | #042C53 |
| c-green | #EAF3DE | #C0DD97 | #97C459 | #639922 | #3B6D11 | #27500A | #173404 |
| c-amber | #FAEEDA | #FAC775 | #EF9F27 | #BA7517 | #854F0B | #633806 | #412402 |
| c-red | #FCEBEB | #F7C1C1 | #F09595 | #E24B4A | #A32D2D | #791F1F | #501313 |

## Theme mapping (which stops to use)

| Theme | Fill | Stroke | Title text | Subtitle text |
|-------|------|--------|------------|---------------|
| Dark  | 800  | 200    | 100        | 200           |
| Light | 50   | 600    | 800        | 600           |

## Worked examples

### Dark theme, teal node
```
fill="#085041"  stroke="#5DCAA5"
title text:   fill="#E1F5EE"
subtitle:     fill="#5DCAA5"
```

### Dark theme, amber node
```
fill="#633806"  stroke="#FAC775"
title text:   fill="#FAEEDA"
subtitle:     fill="#FAC775"
```

### Dark theme, blue node
```
fill="#0C447C"  stroke="#85B7EB"
title text:   fill="#E6F1FB"
subtitle:     fill="#85B7EB"
```

### Light theme, teal node
```
fill="#E1F5EE"  stroke="#0F6E56"
title text:   fill="#085041"
subtitle:     fill="#0F6E56"
```

### Light theme, amber node
```
fill="#FAEEDA"  stroke="#854F0B"
title text:   fill="#633806"
subtitle:     fill="#854F0B"
```

## Ramp selection guidance

- **Default neutral**: c-gray (containers, borders, secondary text)
- **Primary action / focal**: c-blue or c-teal
- **Warning / caution**: c-amber or c-coral
- **Error / danger**: c-red
- **Success / positive**: c-green
- **Creative / distinct**: c-purple or c-pink

Hard limit: ≤2 ramps per diagram (excluding c-gray for neutrals).
