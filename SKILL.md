name: pixel-alchemist
version: 1.2.0
description: Transmutes raw design elements into optimized, conversion-focused user experiences
author: SMOUJBOT Design Team
tags:
  - ui
  - ux
  - conversion
type: design-optimization
dependencies:
  - node: ">=18.0.0"
  - sharp: ">=0.32.0"
  - puppeteer: ">=21.0.0"
  - color: ">=4.0.0"
  - svgo: ">=3.0.0"
env_vars:
  - name: PIXEL_ALCHEMIST_INPUT_DIR
    required: true
    description: Path to raw design assets
  - name: PIXEL_ALCHEMIST_OUTPUT_DIR
    required: true
    description: Path for optimized outputs
  - name: CONVERSION_TARGETS
    required: false
    default: "clicks,form_submits,scroll_depth"
    description: Comma-separated conversion metrics to optimize for
  - name: A11Y_LEVEL
    required: false
    default: "WCAG_2.1_AA"
    description: Accessibility compliance level (WCAG_2.1_A, AA, AAA)
---

# Pixel Alchemist

## Purpose

Pixel Alchemist transforms raw design elements (Figma exports, Sketch files, image assets) into optimized, conversion-optimized UI components with automatic accessibility compliance and performance enhancements. It analyzes color contrast ratios, typography scales, spacing systems, and visual hierarchy to maximize engagement and conversion rates.

**Primary use cases:**
- Convert design system exports into production-ready components with accessibility baked in
- Optimize hero images and CTAs for maximum click-through rates (CTR)
- Automatically generate dark/light mode variants with proper contrast ratios
- Transform raw PNGs/SVGs into WebP/AVIF with optimal loading performance
- Enforce consistent UI patterns across product pages for brand coherence
- Convert static designs into interactive prototypes with state management

## Scope

### Core Commands

```
pixel-alchemist optimize <input> [options]
pixel-alchemist convert --format <target> --source <input>
pixel-alchemist analyze --type <metric>
pixel-alchemist apply --variant <theme>
pixel-alchemist validate --rules <accessibility|performance|conversion>
pixel-alchemist batch --manifest <json_file>
```

### Detailed Command Specifications

#### 1. `optimize`
Transforms raw assets into optimized UI components with conversion optimization.

**Syntax:**
```
pixel-alchemist optimize <input_path> \
  --output <dir> \
  --template <component|page|system> \
  --target-conversion <primary|secondary> \
  --sizes <responsive_breakpoints> \
  --format <webp|avif|png|svg> \
  --a11y-strictness <relaxed|standard|strict>
```

**Real example:**
```bash
pixel-alchemist optimize ./designs/hero-banner.png \
  --output ./src/components/optimized/ \
  --template component \
  --target-conversion primary_cta \
  --sizes "320,768,1024,1440" \
  --format webp \
  --a11y-strictness standard
```

**Flags:**
- `--compression-level 1-6` (default: 4)
- `--preserve-color-profile` (boolean)
- `--generate-srcset` (boolean, default: true)
- `--lazy-load` (boolean, default: true)
- `--critical-path` (extract above-the-fold assets)

#### 2. `convert`
Batch-converts between formats while maintaining quality thresholds.

**Syntax:**
```
pixel-alchemist convert \
  --source ./raw-assets/ \
  --format webp \
  --quality 85 \
  --strip-metadata \
  --rename-pattern "compressed_{filename}"
```

**Real example:**
```bash
pixel-alchemist convert \
  --source ./figma-exports/ \
  --format avif \
  --quality 75 \
  --lossless false \
  --chroma-subsampling 4:2:0 \
  --output ./public/images/optimized/
```

#### 3. `analyze`
Analyzes design assets for conversion potential and compliance issues.

**Syntax:**
```
pixel-alchemist analyze \
  --type contrast_ratio \
  --input ./components/button-primary.svg \
  --wcag-level AA \
  --report json
```

**Real example:**
```bash
pixel-alchemist analyze \
  --type hierarchy \
  --input ./pages/landing/ \
  --viewport mobile \
  --engagement-metrics bounce_rate,time_on_page \
  --output ./reports/hierarchy-analysis.json
```

**Analysis types:**
- `contrast_ratio` - Color accessibility
- `hierarchy` - Visual information architecture
- `color-harmony` - Palette cohesion score 0-100
- `conversion-funnel` - CTA placement effectiveness
- `responsive-behavior` - Breakpoint consistency

#### 4. `apply`
Applies optimized variants to existing codebases with safety checks.

**Syntax:**
```
pixel-alchemist apply \
  --variant dark_mode \
  --source ./optimized/dark/ \
  --target ./src/theme/ \
  --dry-run \
  --backup-originals
```

**Real example:**
```bash
pixel-alchemist apply \
  --variant high-contrast \
  --source ./variants/contrast/ \
  --target ./public/assets/ \
  --strategy replace-with-backup \
  --testing-mode a11y-suite \
  --coverage-threshold 95
```

#### 5. `validate`
Runs comprehensive validation against multiple rule sets.

**Syntax:**
```
pixel-alchemist validate \
  --rules accessibility,performance,conversion \
  --path ./deployed/site/ \
  --lighthouse-config ./config/lhci.json \
  --fail-on-warnings
```

**Real example:**
```bash
pixel-alchemist validate \
  --rules accessibility \
  --path ./build/production/ \
  --axe-level AA \
  --max-violations 0 \
  --ignore-rules color-contrast,image-alt
```

#### 6. `batch`
Processes multiple assets via manifest JSON.

**Example manifest (`batch-manifest.json`):**
```json
{
  "assets": [
    {
      "source": "designs/header/logo.svg",
      "output": "src/assets/logo.svg",
      "optimizations": ["a11y-tags", "path-simplify", "viewbox-fix"],
      "variants": ["dark", "light", "high-contrast"]
    },
    {
      "source": "designs/buttons/primary.png",
      "sprite": "buttons-sprite",
      "formats": ["webp", "png"],
      "responsive": true,
      "lazy": true
    }
  ],
  "global": {
    "compression": 5,
    "stripMetadata": true,
    "backupOriginal": true
  }
}
```

**Command:**
```bash
pixel-alchemist batch --manifest batch-manifest.json --parallelism 4
```

## Work Process

### Standard Transformation Pipeline

1. **Ingestion Phase**
   ```bash
   # Raw design intake
   pixel-alchemist ingest --source ./figma-exports/ --format figma
   ```
   - Validates file integrity
   - Extracts layer metadata
   - Identifies component boundaries
   - Generates asset manifest

2. **Analysis Phase**
   ```bash
   # Comprehensive analysis
   pixel-alchemist analyze --type all --input ./manifest.json
   ```
   - Runs color contrast analysis (CIE LAB delta-E)
   - Measures typography rhythm compliance
   - Calculates conversion potential score 0-100
   - Detects accessibility violations

3. **Optimization Phase**
   ```bash
   # Apply optimizations
   pixel-alchemist optimize --manifest analysis.json --target production
   ```
   - Applies preset-specific transformations
   - Generates responsive variants
   - Creates theme variants (light/dark/high-contrast)
   - Optimizes file sizes (lossy/lossless balance)

4. **Validation Phase**
   ```bash
   # Pre-deployment validation
   pixel-alchemist validate --rules all --fail-on-warnings --browser Puppeteer
   ```
   - Lighthouse CI scoring (target >95)
   - axe-core accessibility scan
   - Visual regression testing (pixel diff < 0.1%)
   - Performance budget validation

5. **Deployment Phase**
   ```bash
   # Safe application
   pixel-alchemist apply --variant optimized --strategy atomic-update
   ```
   - Creates codebase backup
   - Generates diff report
   - Performs sanity checks
   - Updates manifest/index

## Golden Rules

These rules are enforced by the system and cannot be bypassed:

### Rule 1: Contrast Preservation
- All color transformations must maintain WCAG 2.1 AA minimum contrast ratios
- Never reduce contrast below 4.5:1 for normal text, 3:1 for large text
- Auto-fallback to accessible alternatives if target fails

### Rule 2: Lossless Source Preservation
- Original source files always retained in `./.pixel-alchemist/backups/`
- All operations are atomic and reversible
- Zero destructive operations without explicit `--force-destructive`

### Rule 3: Conversion Integrity
- Optimized assets must maintain visual similarity >98% (SSIM metric)
- No color shifts >2 delta-E units
- Typography rendering must match original at device pixel ratio 1, 2, 3

### Rule 4: Performance Budget
- All PNG optimizations: <150KB (max), target <100KB
- All SVG optimizations: <50KB (max), target <30KB
- Sprite sheets: total <500KB
- WebP/AVIF must achieve 25-40% size reduction over source

### Rule 5: Component Isolation
- Transforms operate within component scope only
- No cross-component bleed of styles/variables
- Each component self-contained with its assets

### Rule 6: A11y Non-Degradation
- Never remove semantic markup or ARIA labels
- Alt text preserved or enhanced (never removed)
- Keyboard navigation paths maintained
- Focus states must remain visible in all variants

### Rule 7: Responsive Truthfulness
- Generated responsive variants must use exact same aspect ratios
- No content cropping without explicit `--allow-crop` flag
- Maintain semantic HTML structure across breakpoints

## Examples

### Example 1: Optimize Hero Banner for Conversion

**User input:**
```bash
pixel-alchemist optimize ./designs/marketing/hero-q1.png \
  --template component \
  --target-conversion primary_cta \
  --sizes "375,768,1024,1440" \
  --format webp \
  --quality 82 \
  --strip-metadata \
  --generate-srcset \
  --lazy-load \
  --critical-path \
  --output ./src/components/hero/
```

**System output:**
```
✓ Analyzed hero-q1.png (2480x1240)
✓ Conversion target: primary_cta optimized (CTR potential +23%)
✓ Generated variants: 4 responsive sizes
✓ WebP compression: 248KB → 67KB (73% reduction)
✓ Created srcset attribute for responsive loading
✓ Extracted above-the-fold critical image (375w)
✓ Lazy loading configured for below-fold variants
✓ Output: ./src/components/hero/HeroBanner.jsx
✓ Added <link rel="preload"> for critical variant
✓ Accessibility: contrast ratio 7.2:1 (passes AAA)
```

**Generated `HeroBanner.jsx`:**
```jsx
import React from 'react';
import heroMobile from './hero-q1-375.webp';
import heroTablet from './hero-q1-768.webp';
import heroDesktop from './hero-q1-1024.webp';
import heroWide from './hero-q1-1440.webp';

export default function HeroBanner() {
  return (
    <section className="hero" aria-label="Main promotional banner">
      <picture>
        <source media="(min-width: 1440px)" srcSet={heroWide} />
        <source media="(min-width: 1024px)" srcSet={heroDesktop} />
        <source media="(min-width: 768px)" srcSet={heroTablet} />
        <img
          src={heroMobile}
          alt="Special offer: 40% off all annual plans - Limited time"
          loading="eager"
          fetchPriority="high"
          className="hero__image"
        />
      </picture>
      <div className="hero__cta-container">
        <button className="cta-primary" aria-label="Claim your 40% discount">
          Get 40% Off Now
        </button>
      </div>
    </section>
  );
}
```

### Example 2: Batch Convert Figma Exports

**Manifest (`figma-manifest.json`):**
```json
{
  "sourceDir": "./figma-exports/v2.3/",
  "targetFormats": ["webp", "svg"],
  "rules": {
    "icons": {
      "format": "svg",
      "optimize": true,
      "removeMetadata": true
    },
    "photos": {
      "format": "webp",
      "quality": 85,
      "sizes": [320, 640, 1024, 1920]
    },
    "illustrations": {
      "format": "avif",
      "quality": 75,
      "lossless": false
    }
  }
}
```

**User command:**
```bash
pixel-alchemist batch --manifest figma-manifest.json --workers 8 --report ./reports/batch-conversion.html
```

**Output:**
```
Batch processing started with 8 workers
Processing 142 assets...

Icons (47 files):
  ✓ Optimized SVGs: 47/47
  ✓ Average size reduction: 34%
  ✗ Failed: 0

Photos (68 files):
  ✓ WebP conversion: 68/68
  ✓ Responsive variants generated: 204 files
  ✓ Average size reduction: 61%
  ✗ Failed: 0

Illustrations (27 files):
  ✓ AVIF conversion: 27/27
  ✓ Average size reduction: 48%
  ✗ Failed: 0

Total reduction: 12.4MB → 3.8MB (69% smaller)
Generated report: ./reports/batch-conversion.html
✓ All assets passed accessibility validation
```

### Example 3: Validate Dark Mode Implementation

**User command:**
```bash
pixel-alchemist validate \
  --rules accessibility \
  --path ./src/theme/dark/ \
  --wcag-level AAA \
  --color-samples 1000 \
  --axe-rules "color-contrast,image-alt,aria-valid-attr" \
  --output ./reports/dark-mode-a11y.json
```

**Output:**
```
Validating dark theme assets...
Scanned 234 elements in ./src/theme/dark/

Accessibility Report (AAA):
✗ color-contrast: 3 violations
  - Button .btn-primary (text: #9CA3AF, bg: #374151) ratio 3.2:1 (needs 7:1)
  - Card .card-title (text: #D1D5DB, bg: #1F2937) ratio 4.1:1 (needs 7:1)
  - Link footer-link (text: #6B7280, bg: #111827) ratio 3.8:1 (needs 7:1)

✓ image-alt: 0 violations (45 images)
✓ aria-valid-attr: 0 violations (23 ARIA attributes)

Fix suggestions:
  1. Replace #9CA3AF with #F3F4F6 (ratio 10.6:1)
  2. Replace #D1D5DB with #FFFFFF (ratio 21:1)
  3. Replace #6B7280 with #F9FAFB (ratio 16.3:1)

Report saved: ./reports/dark-mode-a11y.json
Exit code: 1 (3 violations found)
```

### Example 4: Apply Conversion Optimization Variants

**User command:**
```bash
pixel-alchemist apply \
  --variant cta-optimized \
  --source ./variants/cta-v2/ \
  --target ./src/components/marketing/ \
  --strategy staged-rollout \
  --rollout-percentage 25 \
  --dry-run \
  --report ./reports/apply-dryrun.txt
```

**Dry run output:**
```
DRY RUN - No changes will be written

Source variants detected:
  - ./variants/cta-v2/primary.webp
  - ./variants/cta-v2/secondary.webp
  - ./variants/cta-v2/tertiary.svg

Target files to update (3):
  ./src/components/marketing/PricingCTA.jsx
  ./src/components/marketing/NewsletterSignup.jsx
  ./src/components/marketing/WebinarBanner.jsx

Changes proposed:
  1. Replace 3 image references with optimized variants
  2. Update srcset attributes (12 new entries)
  3. Add lazy loading to 2 components
  4. Convert 1 PNG to WebP format

Estimated performance gain:
  - LCP improvement: ~0.8s
  - Total bytes saved: ~145KB
  - CLS improvement: ~0.05

Report: ./reports/apply-dryrun.txt
✓ Dry run successful. Use --execute to apply changes.
```

## Rollback Commands

### Full System Rollback

Restore all assets to pre-optimization state:

```bash
# List available backup points
pixel-alchemist rollback list --backup-dir ./.pixel-alchemist/backups/

# Restore entire component set
pixel-alchemist rollback restore \
  --timestamp 20260101_143022 \
  --target ./src/components/hero/

# Verify rollback integrity
pixel-alchemist validate --path ./src/components/hero/ --health-check
```

### Selective Asset Rollback

Revert specific file to original:

```bash
# Single file
pixel-alchemist rollback file \
  --asset ./src/components/hero/HeroBanner.jsx \
  --to-original \
  --backup-manifest ./.pixel-alchemist/backups/manifest.json

# Multiple files by pattern
pixel-alchemist rollback pattern \
  --pattern "*.webp" \
  --before-timestamp 20260101_120000 \
  --target ./src/components/hero/
```

### Rollback Preview (Dry Run)

```bash
pixel-alchemist rollback preview \
  --timestamp 20260101_143022 \
  --target ./src/ \
  --diff-only
```

## Troubleshooting

### Issue: Contrast ratio fails even with AA colors

**Cause:** Color profile mismatch or gamma correction differences

**Fix:**
```bash
# Regenerate with perceptual color space
pixel-alchemist optimize <input> --color-space lab --perceptual true

# Force accessibility override
pixel-alchemist validate --auto-fix-contrast --target-contrast 4.6:1
```

### Issue: WebP compression artifacts visible

**Cause:** Quality too low for content type

**Fix:**
```bash
# Content-aware quality selection
pixel-alchemist optimize <input> --quality auto --analyze-content

# Manual override for specific asset type
pixel-alchemist convert --format webp --quality 92 --lossless false
```

### Issue: Dark mode variants missing

**Cause:** Color channel separation failed on source

**Fix:**
```bash
# Regenerate with enhanced separation
pixel-alchemist apply --variant dark_mode --color-extraction advanced

# Check source color depth
identify -verbose source.png | grep -i "depth"
```

### Issue: Batch process hangs on specific file

**Cause:** Corrupt source or unsupported format

**Fix:**
```bash
# Skip problematic files and continue
pixel-alchemist batch --manifest.json --skip-on-error --error-log ./errors.log

# Isolate and inspect
pixel-alchemist analyze --file ./problematic-file.png --debug > debug.txt
```

### Issue: Responsive variants not loading correctly

**Cause:** Incorrect srcset ordering or missing sizes attribute

**Fix:**
```bash
# Regenerate with explicit breakpoints
pixel-alchemist optimize <input> --breakpoints "480,768,1024,1440" --sizes "(max-width: 480px) 100vw, (max-width: 768px) 50vw, 33vw"

# Validate responsive setup
pixel-alchemist validate --type responsive-breakpoints --browser test.html
```

### Issue: Memory exhaustion during batch

**Cause:** Too many parallel workers for system RAM

**Fix:**
```bash
# Limit workers to available memory
pixel-alchemist batch --manifest.json --workers 2 --memory-limit 2GB

# Enable streaming for large files
pixel-alchemist convert --stream --chunk-size 10MB
```

## Verification Checklist

After any transformation, verify:

```bash
# 1. File integrity
pixel-alchemist validate --checksum --original-backup

# 2. Visual similarity (SSIM > 0.98)
pixel-alchemist analyze --metric ssim --threshold 0.98

# 3. Accessibility compliance
pixel-alchemist validate --rules accessibility --wcag AA --fail-on-violation

# 4. Performance budgets
pixel-alchemist validate --rules performance --max-size 150KB

# 5. Responsive functionality
pixel-alchemist test --breakpoints "320,768,1024" --viewport emulate

# 6. Production build integrity
pixel-alchemist apply --dry-run && npm run build --if-present
```

## Environment Setup

Required environment variables:
```bash
export PIXEL_ALCHEMIST_INPUT_DIR="./raw-designs/"
export PIXEL_ALCHEMIST_OUTPUT_DIR="./src/assets/optimized/"
export CONVERSION_TARGETS="clicks,scroll_depth,form_completion"
export A11Y_LEVEL="WCAG_2.1_AA"
```

Installation:
```bash
npm install -g @pixel-alchemist/cli
# Verify installation
pixel-alchemist --version
pixel-alchemist health # runs environment diagnostics
```