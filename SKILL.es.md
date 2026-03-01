name: pixel-alchemist
version: 1.2.0
description: Transmuta elementos de diseño crudo en experiencias de usuario optimizadas y enfocadas en conversión
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
    description: Ruta a los assets de diseño crudo
  - name: PIXEL_ALCHEMIST_OUTPUT_DIR
    required: true
    description: Ruta para las salidas optimizadas
  - name: CONVERSION_TARGETS
    required: false
    default: "clicks,form_submits,scroll_depth"
    description: Métricas de conversión separadas por comas para optimizar
  - name: A11Y_LEVEL
    required: false
    default: "WCAG_2.1_AA"
    description: Nivel de cumplimiento de accesibilidad (WCAG_2.1_A, AA, AAA)
---

# Pixel Alchemist

## Propósito

Pixel Alchemist transforma elementos de diseño crudos (exportaciones de Figma, archivos de Sketch, assets de imágenes) en componentes UI optimizados y listos para producción con cumplimiento automático de accesibilidad y mejoras de rendimiento. Analiza ratios de contraste de color, escalas tipográficas, sistemas de espaciado y jerarquía visual para maximizar el engagement y las tasas de conversión.

**Casos de uso principales:**
- Convertir exportaciones de sistemas de diseño en componentes listos para producción con accesibilidad integrada
- Optimizar imágenes héroes y CTAs para maximizar tasas de clic (CTR)
- Generar automáticamente variantes modo oscuro/claro con ratios de contraste adecuados
- Transformar PNGs/SVGs crudos en WebP/AVIF con rendimiento de carga óptimo
- Aplicar patrones UI consistentes en páginas de producto para coherencia de marca
- Convertir diseños estáticos en prototipos interactivos con gestión de estados

## Alcance

### Comandos Principales

```
pixel-alchemist optimize <input> [options]
pixel-alchemist convert --format <target> --source <input>
pixel-alchemist analyze --type <metric>
pixel-alchemist apply --variant <theme>
pixel-alchemist validate --rules <accessibility|performance|conversion>
pixel-alchemist batch --manifest <json_file>
```

### Especificaciones Detalladas de Comandos

#### 1. `optimize`
Transforma assets crudos en componentes UI optimizados con optimización de conversión.

**Sintaxis:**
```
pixel-alchemist optimize <input_path> \
  --output <dir> \
  --template <component|page|system> \
  --target-conversion <primary|secondary> \
  --sizes <responsive_breakpoints> \
  --format <webp|avif|png|svg> \
  --a11y-strictness <relaxed|standard|strict>
```

**Ejemplo real:**
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
Convierte por lotes entre formatos manteniendo umbrales de calidad.

**Sintaxis:**
```
pixel-alchemist convert \
  --source ./raw-assets/ \
  --format webp \
  --quality 85 \
  --strip-metadata \
  --rename-pattern "compressed_{filename}"
```

**Ejemplo real:**
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
Analiza assets de diseño para potencial de conversión y problemas de cumplimiento.

**Sintaxis:**
```
pixel-alchemist analyze \
  --type contrast_ratio \
  --input ./components/button-primary.svg \
  --wcag-level AA \
  --report json
```

**Ejemplo real:**
```bash
pixel-alchemist analyze \
  --type hierarchy \
  --input ./pages/landing/ \
  --viewport mobile \
  --engagement-metrics bounce_rate,time_on_page \
  --output ./reports/hierarchy-analysis.json
```

**Tipos de análisis:**
- `contrast_ratio` - Accesibilidad de color
- `hierarchy` - Arquitectura de información visual
- `color-harmony` - Puntuación de cohesión de paleta 0-100
- `conversion-funnel` - Efectividad de colocación de CTAs
- `responsive-behavior` - Consistencia de breakpoints

#### 4. `apply`
Aplica variantes optimizadas a codebases existentes con verificaciones de seguridad.

**Sintaxis:**
```
pixel-alchemist apply \
  --variant dark_mode \
  --source ./optimized/dark/ \
  --target ./src/theme/ \
  --dry-run \
  --backup-originals
```

**Ejemplo real:**
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
Ejecuta validación exhaustiva contra múltiples conjuntos de reglas.

**Sintaxis:**
```
pixel-alchemist validate \
  --rules accessibility,performance,conversion \
  --path ./deployed/site/ \
  --lighthouse-config ./config/lhci.json \
  --fail-on-warnings
```

**Ejemplo real:**
```bash
pixel-alchemist validate \
  --rules accessibility \
  --path ./build/production/ \
  --axe-level AA \
  --max-violations 0 \
  --ignore-rules color-contrast,image-alt
```

#### 6. `batch`
Procesa múltiples assets vía JSON de manifiesto.

**Ejemplo de manifiesto (`batch-manifest.json`):**
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

**Comando:**
```bash
pixel-alchemist batch --manifest batch-manifest.json --parallelism 4
```

## Proceso de Trabajo

### Pipeline Estándar de Transformación

1. **Fase de Ingesta**
   ```bash
   # Admisión de diseño crudo
   pixel-alchemist ingest --source ./figma-exports/ --format figma
   ```
   - Valida integridad de archivos
   - Extrae metadatos de capas
   - Identifica límites de componentes
   - Genera manifiesto de assets

2. **Fase de Análisis**
   ```bash
   # Análisis exhaustivo
   pixel-alchemist analyze --type all --input ./manifest.json
   ```
   - Ejecuta análisis de contraste de color (CIE LAB delta-E)
   - Mide cumplimiento de ritmo tipográfico
   - Calcula puntuación de potencial de conversión 0-100
   - Detecta violaciones de accesibilidad

3. **Fase de Optimización**
   ```bash
   # Aplicar optimizaciones
   pixel-alchemist optimize --manifest analysis.json --target production
   ```
   - Aplica transformaciones específicas de preset
   - Genera variantes responsivas
   - Crea variantes de tema (oscuro/claro/alto-contraste)
   - Optimiza tamaños de archivo (balance lossy/lossless)

4. **Fase de Validación**
   ```bash
   # Validación pre-despliegue
   pixel-alchemist validate --rules all --fail-on-warnings --browser Puppeteer
   ```
   - Puntuación Lighthouse CI (objetivo >95)
   - Escaneo de accesibilidad axe-core
   - Testing de regresión visual (diff de píxeles < 0.1%)
   - Validación de presupuesto de rendimiento

5. **Fase de Despliegue**
   ```bash
   # Aplicación segura
   pixel-alchemist apply --variant optimized --strategy atomic-update
   ```
   - Crea backup de codebase
   - Genera reporte de diff
   - Realiza verificaciones de sanity
   - Actualiza manifiesto/índice

## Reglas de Oro

Estas reglas son aplicadas por el sistema y no pueden ser eludidas:

### Regla 1: Preservación de Contraste
- Todas las transformaciones de color deben mantener ratios mínimos de contraste WCAG 2.1 AA
- Nunca reducir contraste por debajo de 4.5:1 para texto normal, 3:1 para texto grande
- Auto-fallback a alternativas accesibles si el objetivo falla

### Regla 2: Preservación de Fuente Sin Pérdidas
- Archivos fuente originales siempre retenidos en `./.pixel-alchemist/backups/`
- Todas las operaciones son atómicas y reversibles
- Cero operaciones destructivas sin `--force-destructive` explícito

### Regla 3: Integridad de Conversión
- Assets optimizados deben mantener similitud visual >98% (métrica SSIM)
- Sin cambios de color >2 unidades delta-E
- Renderizado tipográfico debe coincidir con original a relación de píxeles de dispositivo 1, 2, 3

### Regla 4: Presupuesto de Rendimiento
- Todas las optimizaciones PNG: <150KB (máx), objetivo <100KB
- Todas las optimizaciones SVG: <50KB (máx), objetivo <30KB
- Sprite sheets: total <500KB
- WebP/AVIF deben lograr reducción de tamaño 25-40% sobre fuente

### Regla 5: Aislamiento de Componentes
- Transformaciones operan dentro del alcance del componente únicamente
- Sin filtrado cruzado de estilos/variables entre componentes
- Cada componente autónomo con sus assets

### Regla 6: No-Degradación de A11y
- Nunca eliminar marcado semántico o etiquetas ARIA
- Texto alt preservado o mejorado (nunca eliminado)
- Rutas de navegación por teclado mantenidas
- Estados de foco deben permanecer visibles en todas las variantes

### Regla 7: Veracidad Responsiva
- Variantes responsivas generadas deben usar exactamente las mismas proporciones de aspecto
- Sin recorte de contenido sin flag `--allow-crop` explícita
- Mantener estructura HTML semántica en todos los breakpoints

## Ejemplos

### Ejemplo 1: Optimizar Banner Héro para Conversión

**Input del usuario:**
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

**Salida del sistema:**
```
✓ Analizado hero-q1.png (2480x1240)
✓ Objetivo de conversión: primary_cta optimizado (CTR potencial +23%)
✓ Variantes generadas: 4 tamaños responsivos
✓ Compresión WebP: 248KB → 67KB (73% reducción)
✓ Atributo srcset creado para carga responsiva
✓ Imagen crítica extraída above-the-fold (375w)
✓ Lazy loading configurado para variantes below-fold
✓ Salida: ./src/components/hero/HeroBanner.jsx
✓ <link rel="preload"> añadido para variante crítica
✓ Accesibilidad: ratio de contraste 7.2:1 (pasa AAA)
```

**`HeroBanner.jsx` generado:**
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

### Ejemplo 2: Conversión por Lotes de Exportaciones de Figma

**Manifiesto (`figma-manifest.json`):**
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

**Comando del usuario:**
```bash
pixel-alchemist batch --manifest figma-manifest.json --workers 8 --report ./reports/batch-conversion.html
```

**Salida:**
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

### Ejemplo 3: Validar Implementación de Modo Oscuro

**Comando del usuario:**
```bash
pixel-alchemist validate \
  --rules accessibility \
  --path ./src/theme/dark/ \
  --wcah-level AAA \
  --color-samples 1000 \
  --axe-rules "color-contrast,image-alt,aria-valid-attr" \
  --output ./reports/dark-mode-a11y.json
```

**Salida:**
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

### Ejemplo 4: Aplicar Variantes de Optimización de Conversión

**Comando del usuario:**
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

**Salida dry run:**
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

## Comandos de Rollback

### Rollback Completo del Sistema

Restaurar todos los assets al estado pre-optimización:

```bash
# Listar puntos de backup disponibles
pixel-alchemist rollback list --backup-dir ./.pixel-alchemist/backups/

# Restaurar conjunto completo de componentes
pixel-alchemist rollback restore \
  --timestamp 20260101_143022 \
  --target ./src/components/hero/

# Verificar integridad del rollback
pixel-alchemist validate --path ./src/components/hero/ --health-check
```

### Rollback Selectivo de Asset

Revertir archivo específico a original:

```bash
# Archivo único
pixel-alchemist rollback file \
  --asset ./src/components/hero/HeroBanner.jsx \
  --to-original \
  --backup-manifest ./.pixel-alchemist/backups/manifest.json

# Múltiples archivos por patrón
pixel-alchemist rollback pattern \
  --pattern "*.webp" \
  --before-timestamp 20260101_120000 \
  --target ./src/components/hero/
```

### Vista Previa de Rollback (Dry Run)

```bash
pixel-alchemist rollback preview \
  --timestamp 20260101_143022 \
  --target ./src/ \
  --diff-only
```

## Solución de Problemas

### Problema: Ratio de contraste falla incluso con colores AA

**Causa:** Desajuste de perfil de color o diferencias de corrección gamma

**Solución:**
```bash
# Regenerar con espacio de color perceptual
pixel-alchemist optimize <input> --color-space lab --perceptual true

# Forzar override de accesibilidad
pixel-alchemist validate --auto-fix-contrast --target-contrast 4.6:1
```

### Problema: Artefactos de compresión WebP visibles

**Causa:** Calidad demasiado baja para tipo de contenido

**Solución:**
```bash
# Selección de calidad aware de contenido
pixel-alchemist optimize <input> --quality auto --analyze-content

# Override manual para tipo de asset específico
pixel-alchemist convert --format webp --quality 92 --lossless false
```

### Problema: Variantes de modo oscuro faltantes

**Causa:** Separación de canales de color falló en fuente

**Solución:**
```bash
# Regenerar con separación mejorada
pixel-alchemist apply --variant dark_mode --color-extraction advanced

# Verificar profundidad de color de fuente
identify -verbose source.png | grep -i "depth"
```

### Problema: Proceso por lotes se cuelga en archivo específico

**Causa:** Fuente corrupta o formato no soportado

**Solución:**
```bash
# Saltar archivos problemáticos y continuar
pixel-alchemist batch --manifest.json --skip-on-error --error-log ./errors.log

# Aislar e inspeccionar
pixel-alchemist analyze --file ./problematic-file.png --debug > debug.txt
```

### Problema: Variantes responsivas no cargan correctamente

**Causa:** Ordenamiento incorrecto de srcset o atributo sizes faltante

**Solución:**
```bash
# Regenerar con breakpoints explícitos
pixel-alchemist optimize <input> --breakpoints "480,768,1024,1440" --sizes "(max-width: 480px) 100vw, (max-width: 768px) 50vw, 33vw"

# Validar configuración responsiva
pixel-alchemist validate --type responsive-breakpoints --browser test.html
```

### Problema: Agotamiento de memoria durante batch

**Causa:** Demasiados workers paralelos para RAM del sistema

**Solución:**
```bash
# Limitar workers a memoria disponible
pixel-alchemist batch --manifest.json --workers 2 --memory-limit 2GB

# Habilitar streaming para archivos grandes
pixel-alchemist convert --stream --chunk-size 10MB
```

## Lista de Verificación

Después de cualquier transformación, verificar:

```bash
# 1. Integridad de archivos
pixel-alchemist validate --checksum --original-backup

# 2. Similitud visual (SSIM > 0.98)
pixel-alchemist analyze --metric ssim --threshold 0.98

# 3. Cumplimiento de accesibilidad
pixel-alchemist validate --rules accessibility --wcah AA --fail-on-violation

# 4. Presupuestos de rendimiento
pixel-alchemist validate --rules performance --max-size 150KB

# 5. Funcionalidad responsiva
pixel-alchemist test --breakpoints "320,768,1024" --viewport emulate

# 6. Integridad de build de producción
pixel-alchemist apply --dry-run && npm run build --if-present
```

## Configuración del Entorno

Variables de entorno requeridas:
```bash
export PIXEL_ALCHEMIST_INPUT_DIR="./raw-designs/"
export PIXEL_ALCHEMIST_OUTPUT_DIR="./src/assets/optimized/"
export CONVERSION_TARGETS="clicks,scroll_depth,form_completion"
export A11Y_LEVEL="WCAG_2.1_AA"
```

Instalación:
```bash
npm install -g @pixel-alchemist/cli
# Verificar instalación
pixel-alchemist --version
pixel-alchemist health # ejecuta diagnósticos de entorno
```
```