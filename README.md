# rbxui

A browser-based Roblox UI builder. Design your interface visually on a canvas and get valid Luau code generated in real time. No installation, no account, runs entirely in the browser.

Live at: [heeditz.github.io/rbxui](https://heeditz.github.io/rbxui)

---

## How it works

You place UI elements onto a canvas that represents a Roblox ScreenGui. Every change you make, whether moving, resizing, recoloring, or adding modifiers, instantly updates the Luau code on the right panel. When you are done, you copy the code or download it as a `.lua` file and paste it directly into a LocalScript, a Script, or your executor.

All sizes and positions are stored as full UDim2 values with both scale and offset components. Scale is the default so layouts work across different screen sizes. You can override any value manually for pixel-perfect control or to express things like full screen (`1, 0, 1, 0`) or centered with a fixed height (`0.5, -80, 0, 36`).

---

## Elements

### Containers

| Element | Description |
|---|---|
| Frame | Basic container. The most commonly used element for building panels, windows, and backgrounds. |
| ScrollingFrame | A container that supports vertical or horizontal scrolling. Has configurable scroll bar thickness and canvas size. |
| CanvasGroup | A container that renders its children as a single composited layer. Useful for applying group transparency. |
| ViewportFrame | Renders 3D objects inside a 2D UI element. |

### Text

| Element | Description |
|---|---|
| TextLabel | Displays text. Non-interactive. |
| TextButton | A clickable button with text. Fires events when clicked. |
| TextBox | An input field. Supports placeholder text. |

### Image and Media

| Element | Description |
|---|---|
| ImageLabel | Displays a Roblox image asset using an rbxassetid. |
| ImageButton | A clickable image. Fires events when clicked. |
| VideoFrame | Plays a Roblox video asset using an rbxassetid. |

---

## Modifiers

Modifiers are child instances that change the appearance or behavior of their parent element. Select an element on the canvas, then click a modifier in the left panel to add it. Each modifier has its own configurable properties in the Mods tab.

| Modifier | What it does |
|---|---|
| UICorner | Rounds the corners of an element. Configurable by pixel offset and scale. |
| UIStroke | Adds an outline or inner border. Configurable color, thickness, transparency, mode, and join mode. |
| UIPadding | Adds internal padding on all four sides independently. |
| UIGradient | Applies a color or transparency gradient. Configurable start/end color, rotation, and per-side transparency. |
| UIScale | Scales an element up or down without changing its Size property. |
| UIFlexItem | Controls how an element grows or shrinks inside a flex layout. |

---

## Layouts

Layouts are modifier-style instances that automatically arrange sibling elements inside a container. Add them to a Frame or ScrollingFrame.

| Layout | What it does |
|---|---|
| UIListLayout | Arranges children in a single row or column. Configurable direction, alignment, and padding. |
| UIGridLayout | Arranges children in a grid. Configurable cell size and cell padding. |
| UITableLayout | Arranges children in a table structure. |
| UIPageLayout | Arranges children as pages that can be navigated. Supports tween animation between pages. |

---

## Constraints

Constraints enforce rules on an element's size or text size regardless of its parent or layout.

| Constraint | What it does |
|---|---|
| UISizeConstraint | Sets a minimum and maximum pixel size for an element. |
| UITextSizeConstraint | Sets a minimum and maximum text size. Useful with scaled text. |
| UIAspectRatioConstraint | Forces an element to maintain a fixed width-to-height ratio. |

---

## Properties

When an element is selected, its properties appear in the right panel under four tabs.

**Basic** -- Name, parent, Size (XScale, XOffset, YScale, YOffset), Position (XScale, XOffset, YScale, YOffset), background color, background transparency, and any media asset IDs.

**Text** -- Text content, text color, font size, font family, and alignment. Only visible for TextLabel, TextButton, and TextBox.

**Mods** -- All modifiers attached to the selected element with their individual settings. Each modifier can be removed from here.

**Adv** -- ZIndex, AnchorPoint, and Visible toggle. ScrollingFrame also exposes scroll bar thickness and canvas size here.

---

## Size and position

Size and Position both use full UDim2 format with four fields each.

```
XScale, XOffset, YScale, YOffset
```

Scale values are relative to the parent size. Offset values are in pixels. They combine at runtime so you can mix both in the same value.

Common patterns:

| Intent | Size | Position |
|---|---|---|
| Full screen | 1, 0, 1, 0 | 0, 0, 0, 0 |
| Centered element | 0.4, 0, 0.3, 0 | 0.5, 0, 0.5, 0 (with AnchorPoint 0.5, 0.5) |
| Full width, fixed height | 1, 0, 0, 48 | 0, 0, 0, 0 |
| Full width minus padding | 1, -20, 0, 40 | 0, 10, 0, 8 |
| Right-aligned fixed size | 0, 120, 0, 36 | 1, -130, 0, 8 (with AnchorPoint 0, 0) |

When you drag or resize on the canvas, the scale fields update automatically with offset staying at 0. If you type into the fields directly, the canvas preview repositions to match using `scale * canvasSize + offset`.

---

## Parenting

By default all elements are parented to ScreenGui in the generated code. You can change the parent of any element from the Parent dropdown in the Basic tab. The dropdown lists ScreenGui and any container elements (Frame, ScrollingFrame, CanvasGroup, ViewportFrame) that currently exist in your scene. The generated code uses the correct variable name automatically.

---

## Layers

The Layers section at the bottom of the left panel shows all elements ordered from top to bottom by their z-index. Higher entries render in front.

You can reorder layers in two ways:

1. Hover a layer row and click the up/down chevrons that appear on the right side.
2. Grab the grip handle on the left of a row and drag it onto another row to swap their positions.

---

## Canvas size

The canvas toolbar at the bottom has width and height inputs. You can type any resolution or use the preset buttons.

| Preset | Size |
|---|---|
| 16:9 | 480 x 320 |
| Mobile | 360 x 640 |
| 720p | 1280 x 720 |
| 1080p | 1920 x 1080 |

Changing the canvas size does not affect UDim2 scale values. Scale is always relative to the parent at runtime. The canvas is just a preview surface.

---

## ScreenGui settings

Click the ScreenGui Settings section at the bottom of the code panel to expand it. From here you can configure:

- **Name** -- The variable name used throughout the generated code.
- **Parent** -- Where the ScreenGui is parented. Options are PlayerGui, StarterGui, CoreGui (exploit), gethui() (exploit), or a fully custom string.
- **ResetOnSpawn** -- Whether the GUI is destroyed and recreated when the player respawns.
- **ZIndexBehavior** -- Sibling or Global. Controls how ZIndex values are evaluated across nested elements.
- **Enabled** -- Whether the ScreenGui is visible on load.
- **IgnoreGuiInset** -- When true, the GUI covers the full screen including the top inset area.

---

## Save and load

Your project can be saved to the browser at any time. The save stores all elements, all properties, all modifier values, ScreenGui settings, and the canvas size.

- **Save** -- Writes to browser storage. Shortcut: Ctrl+S.
- **Load** -- Reads the last manual save back into the editor.
- **Autosave** -- Every change is written to a separate autosave slot automatically. If you close or refresh the tab without saving, a restore prompt appears the next time you open the tool.

The dot next to the element counter in the header turns grey when you have unsaved changes and green after a save.

---

## Code output

The generated Luau is valid and self-contained. It creates the ScreenGui, all elements, and all modifiers in one block. Each element is declared as a local variable. Modifiers are declared as locals prefixed with an underscore and the modifier type, for example `_UICorner_MainFrame`.

Use the Copy button to copy to clipboard or the Download button to save directly as a `.lua` file named after your ScreenGui.

The output is ready to paste into a LocalScript under StarterGui, directly into an executor, or into any script context depending on the parent you selected.

---

## Keyboard shortcuts

| Key | Action |
|---|---|
| F | Add Frame |
| T | Add TextLabel |
| B | Add TextButton |
| I | Add ImageLabel |
| Del | Delete selected element |
| Ctrl+D | Duplicate selected element |
| Ctrl+Z | Undo |
| Ctrl+S | Save project |

Shortcuts are disabled while any input field is focused so they do not interfere with typing.

---

## License

MIT
