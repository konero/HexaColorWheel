# OTWheel

OpenToonz-style Color Wheel Library - A reusable HSV color picker with hexagonal hue wheel, saturation/value triangle, and color sliders.

## Features

- **Hexagonal Hue Wheel** - OpenToonz-style color selection
- **SV Triangle** - Saturation and Value picker
- **Color Sliders** - H, S, V, A, R, G, B channel sliders with gradient bars
- **OTColor Class** - Color representation with getters/setters for HSV and RGB
- **Event System** - Listen for color changes
- **Static Utilities** - Conversion functions (HSV↔RGB, Hex↔RGB)
- **No Dependencies** - Pure vanilla JavaScript

## Quick Start

```html
<script src="otwheel.js"></script>
<script>
  const wheel = new OTWheel({
    container: '#my-container'
  });
  
  // Listen for changes
  wheel.color.onChange((color) => {
    console.log(color.hex);
  });
</script>
```

## API Reference

### OTWheel

The main widget class that creates the color wheel.

#### Constructor Options

```javascript
const wheel = new OTWheel({
  container: '#selector' | element,  // Required: container element
  hexRadius: 115,    // Optional: hexagon radius
  triWidth: 130,     // Optional: triangle width
  triHeight: 200,    // Optional: triangle height
  gap: 20,           // Optional: gap between hex and triangle
  color: new OTColor() // Optional: initial color
});
```

#### Properties

| Property | Type | Description |
|----------|------|-------------|
| `color` | `OTColor` | The current color object |
| `canvas` | `HTMLCanvasElement` | The canvas element |
| `width` | `number` | Canvas width |
| `height` | `number` | Canvas height |

#### Methods

| Method | Description |
|--------|-------------|
| `render()` | Re-render the wheel |
| `destroy()` | Clean up and remove the wheel |

---

### OTColor

Represents a color with HSV and RGB access through getters/setters.

#### HSV Properties

| Property | Type | Range | Description |
|----------|------|-------|-------------|
| `h` / `hue` | `number` | 0-360 | Hue in degrees |
| `s` / `saturation` | `number` | 0-1 | Saturation |
| `v` / `value` / `brightness` | `number` | 0-1 | Value/Brightness |
| `hsv` | `object` | - | Get/set `{h, s, v}` object |

#### RGB Properties

| Property | Type | Range | Description |
|----------|------|-------|-------------|
| `r` / `red` | `number` | 0-255 | Red channel |
| `g` / `green` | `number` | 0-255 | Green channel |
| `b` / `blue` | `number` | 0-255 | Blue channel |
| `rgb` | `object` | - | Get/set `{r, g, b}` object |

#### Other Properties

| Property | Type | Description |
|----------|------|-------------|
| `hex` | `string` | Hex color (e.g., `"#ff0000"`) |
| `cssRgb` | `string` | CSS `rgb()` string |

#### Methods

```javascript
// Batch updates (efficient - single notification)
color.setHSV(h, s, v);
color.setRGB(r, g, b);

// Clone and copy
const clone = color.clone();
color.copyFrom(otherColor);

// Listen for changes
const unsubscribe = color.onChange((color) => {
  console.log('Changed:', color.hex);
});
unsubscribe(); // Stop listening
```

---

### Static Utilities

```javascript
// HSV ↔ RGB conversion
OTWheel.hsvToRgb(h, s, v);  // Returns {r, g, b}
OTWheel.rgbToHsv(r, g, b);  // Returns {h, s, v}

// Hex ↔ RGB conversion
OTWheel.rgbToHex(r, g, b);  // Returns "#rrggbb"
OTWheel.hexToRgb(hex);      // Returns {r, g, b} or null
```

---

### OTSlider

OpenToonz-style color slider with gradient bar and triangle indicator.

#### Constructor Options

```javascript
const slider = new OTSlider({
  container: '#selector' | element,  // Required: container element
  channel: 'h',      // Required: 'h', 's', 'v', 'a', 'r', 'g', 'b'
  color: myColor,    // Required: OTColor instance to bind to
  width: 200,        // Optional: slider bar width
  height: 20,        // Optional: slider bar height
  showLabel: true,   // Optional: show channel label (H, S, V, etc.)
  showInput: true,   // Optional: show numeric input field
  showArrows: true   // Optional: show increment/decrement arrows
});
```

#### Properties

| Property | Type | Description |
|----------|------|-------------|
| `color` | `OTColor` | The bound color object |
| `alpha` | `number` | Alpha value (0-255) for alpha slider |

#### Methods

| Method | Description |
|--------|-------------|
| `destroy()` | Clean up and remove the slider |

## Examples

### Basic Usage

```javascript
const wheel = new OTWheel({
  container: document.getElementById('picker')
});

wheel.color.onChange((color) => {
  document.body.style.background = color.cssRgb;
});
```

### Using OTColor Standalone

```javascript
const color = new OTColor();

// Set via HSV
color.setHSV(120, 0.8, 1);  // Bright green

// Read RGB
console.log(color.rgb);     // {r: 51, g: 255, b: 51}
console.log(color.hex);     // "#33ff33"

// Set via hex
color.hex = '#ff8800';

// Read HSV
console.log(color.h);       // 32
console.log(color.s);       // 1
console.log(color.v);       // 1
```

### Multiple Wheels with Shared Color

```javascript
const sharedColor = new OTColor();

const wheel1 = new OTWheel({
  container: '#picker1',
  color: sharedColor
});

const wheel2 = new OTWheel({
  container: '#picker2',
  color: sharedColor
});

// Both wheels now reflect the same color!
```

### Custom Sizes

```javascript
// Compact wheel
const compact = new OTWheel({
  container: '#compact',
  hexRadius: 60,
  triWidth: 70,
  triHeight: 100
});

// Large wheel
const large = new OTWheel({
  container: '#large',
  hexRadius: 200,
  triWidth: 220,
  triHeight: 340
});
```

### Using Sliders

```javascript
const color = new OTColor();

// Create sliders for all channels
const sliders = ['h', 's', 'v', 'a', 'r', 'g', 'b'].map(channel => 
  new OTSlider({
    container: `#slider-${channel}`,
    channel: channel,
    color: color,
    width: 200
  })
);

// All sliders automatically sync with the color
color.setHSV(180, 0.8, 1);
```

### Wheel + Sliders Combined

```javascript
const wheel = new OTWheel({
  container: '#picker'
});

// Create sliders bound to same color
new OTSlider({ container: '#h', channel: 'h', color: wheel.color });
new OTSlider({ container: '#s', channel: 's', color: wheel.color });
new OTSlider({ container: '#v', channel: 'v', color: wheel.color });

// Everything stays in sync automatically!
```

## Files

- `otwheel.js` - The main library
- `demo.html` - Interactive demo page
- `README.md` - This documentation

## License

MIT
