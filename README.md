# peterfaria.me

![github pages](https://github.com/zshift/peterfaria.me/workflows/github%20pages/badge.svg)

My personal website built with Hugo and deployed to GitHub Pages.

## Required Tools

### Prerequisites

- **Hugo** (v0.80.0+) - Static site generator
  - Installation: [Hugo Installation Guide](https://gohugo.io/getting-started/installing/)
  - Windows: `choco install hugo-extended` or `scoop install hugo-extended`
  - macOS: `brew install hugo`
  - Linux: Download from [releases](https://github.com/gohugoio/hugo/releases)

- **Git** - Version control
  - Installation: [Git Downloads](https://git-scm.com/downloads)

- **Node.js** (Optional) - For additional build tools
  - Installation: [Node.js Downloads](https://nodejs.org/)
  - Required for GitHub Actions deployment workflow

### Development Setup

1. **Clone the repository**
   ```bash
   git clone https://github.com/zshift/peterfaria.me.git
   cd peterfaria.me
   ```

2. **Initialize theme submodules** (if using git submodules for themes)
   ```bash
   git submodule update --init --recursive
   ```

3. **Start local development server**
   ```bash
   hugo server -D
   ```
   Site will be available at `http://localhost:1313`

4. **Build for production**
   ```bash
   hugo
   ```
   Generated site will be in the `public/` directory

### Optional Tools

- **VS Code** - Recommended editor with Hugo syntax support
- **Hugo Language Server** - VS Code extension for Hugo templating
- **Markdown extensions** - For enhanced Markdown editing
- **Git GUI client** - GitKraken, GitHub Desktop, or Sourcetree

## Project Structure

- `content/posts/` - Blog posts in Markdown format
- `static/` - Static assets (images, CSS, fonts)
- `themes/vitae/` - Custom Hugo theme
- `config.toml` - Site configuration
- `public/` - Generated site output (auto-generated)
- `.github/workflows/` - GitHub Actions deployment configuration

## Breaking Changes (Hugo 0.80 → 0.148)

**⚠️ Important:** This site currently uses Hugo v0.80.0. Upgrading to Hugo v0.148+ introduces several breaking changes that may affect this blog:

### Major Breaking Changes in v0.148.0

1. **URL Structure Changes (affects `uglyURLs = true` sites)**
   - Home, section, and taxonomy pages now generate correct URLs
   - Pages previously published to `/section.html` now publish to `/section/index.html`
   - This affects permalink structure and may break existing links

2. **Custom Output Format Path Changes**
   - Pages with custom output formats using `path` parameter have updated URLs
   - Branch pages now respect the configured path properly

3. **Template and Theme Changes**
   - Some template behaviors have been updated
   - Theme compatibility may be affected

### Other Notable Changes (0.80 → 0.148)

- **Google Analytics Template**: `_internal/google_analytics_async.html` was deprecated in v0.82.0 and replaced with `_internal/google_analytics.html`
- **GitInfo Changes**: `Page.GitInfo()` now returns a pointer, and GitInfo has new `Ancestors` method
- **Template Functions**: New functions like `math.MaxInt64` added
- **Content Handling**: Improvements to content adapters and cascade functionality
- **Render Hooks**: Link and image render hook enablement changed to enums
- **CSS/Asset Processing**: Various improvements to TailwindCSS and minification

### Upgrade Recommendations

Before upgrading Hugo:
1. **Test locally** with the new version first
2. **Check URLs** - ensure all internal links still work
3. **Verify theme compatibility** with newer Hugo versions
4. **Update config** if using deprecated settings
5. **Review build output** for any warnings or errors

For detailed migration information, see:
- [Hugo v0.148.0 Breaking Changes](https://discourse.gohugo.io/t/breaking-changes-in-v0-148-0/55257)
- [Hugo Release Notes](https://github.com/gohugoio/hugo/releases)

## Deployment

The site automatically deploys to GitHub Pages when changes are pushed to the main branch via GitHub Actions workflow.

