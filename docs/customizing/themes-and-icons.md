# Themes & Icons

## Theming

- **`DGT-CUS-140`**{ #dgt-cus-140 } — Brand **per app**, using the modern model-driven app theme (logo, colors set on the app),
  rather than the legacy organization-wide theme — org themes are a classic-era mechanism and
  don't belong in new work.
- Keep theming **inside the solution** so it deploys with everything else; a color set by hand
  in production is a value someone has to remember to reapply after every refresh.
- Respect accessibility: sufficient contrast, and don't rely on color alone to convey state.

## Icons

- **`DGT-CUS-150`**{ #dgt-cus-150 } — Use **SVG web resources** for table and command-bar icons — vector scales cleanly across the
  Unified Interface densities where fixed-size PNGs blur. Reserve raster formats for genuine
  images (photos, logos with gradients).
- Store icons as web resources in the solution and name them per the
  [icon conventions](naming-conventions.md#core-schema) (`prfx_/Icons/Entity/...` for table
  icons, `prfx_/Icons/Button/...` for command icons). Assign the table icon in the table's
  properties; assign button icons in the [Command Bar](command-bar.md) designer.
- Keep a **consistent visual style** across a project's icons (one icon set / stroke weight),
  and check them in both light and dark mode.

!!! tip "Icons are web resources — they deploy like code"
    Because icons are web resources, they go through the same packaging and
    [`dgtp push`](../alm/pre-post-deployment.md#web-resource-push) path as scripts. Keep the SVG
    sources in the repository alongside the [web resource project](../coding/clientside/typescript-webresources.md),
    not only uploaded ad hoc in the maker portal.
