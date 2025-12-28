# dittoTones 🟣

Generate color palettes by blending the L/C curves of design system ramps (Tailwind, Radix, etc.) with your target hue.

## Install

```bash
npm install dittotones culori
```

## Usage

```typescript
import { DittoTones } from 'dittotones';
import { tailwindRamps } from 'dittotones/ramps/tailwind';
// or
import { radixRamps } from 'dittotones/ramps/radix';
import { formatCss, formatHex } from 'culori';

// Use Tailwind ramps (shades: 50-950)
const ditto = new DittoTones({ ramps: tailwindRamps });

// Or Radix ramps (shades: 1-12)
const dittoRadix = new DittoTones({ ramps: radixRamps });

const result = ditto.generate('#F97316');

// result.scale contains Oklch color objects
// Use culori's formatCss or formatHex to convert:

for (const [shade, color] of Object.entries(result.scale)) {
  console.log(`${shade}: ${formatCss(color)}`);
  // 50: oklch(0.98 0.016 49)
  // 100: oklch(0.954 0.038 49)
  // ...
}

// Or as hex:
for (const [shade, color] of Object.entries(result.scale)) {
  console.log(`${shade}: ${formatHex(color)}`);
}
```

## Result

```typescript
interface GenerateResult {
  inputColor: Oklch;           // Parsed input color
  matchedShade: string;        // e.g. "500"
  method: 'exact' | 'single' | 'blend';
  sources: {                   // Which ramps were used
    name: string;
    deltaE: number;
    weight: number;
  }[];
  scale: Record<string, Oklch>; // The generated palette
}
```

## How it works

1. **Find closest ramp** — matches input to nearest color in all ramps using deltaE
2. **Find blend partner** — finds second ramp with closest hue at same shade
3. **Blend L/C curves** — interpolates lightness/chroma between the two ramps
4. **Apply target hue** — replaces hue across entire scale
5. **Correct to input** — adjusts L/C so your input color fits naturally in the scale

## Custom ramps

```typescript
import { DittoTones } from 'dittotones';
import { parse, type Oklch } from 'culori';

const customRamps = new Map([
  ['brand', {
    '50': parse('oklch(98% 0.01 250)') as Oklch,
    '500': parse('#3B82F6') as Oklch,
    '950': parse('oklch(25% 0.05 250)') as Oklch,
  }],
]);

const ditto = new DittoTones({ ramps: customRamps });
```

## Dev

```bash
npm install
npm run dev     # Start dev server with demo
npm run build   # Build library
```

## License

MIT
