# Agentic AI Context: peterfaria.me Hugo Blog

This document provides comprehensive context for AI agents working with the peterfaria.me blog codebase.

## Project Overview

**Project Type**: Hugo Static Site Generator Blog  
**Hosting**: GitHub Pages with custom domain  
**Repository**: zshift/peterfaria.me  
**Primary Use**: Technical blog focused on programming content  
**Last Updated**: July 25, 2025  

## Technology Stack

### Core Framework
- **Hugo** (v0.80.0) - Static site generator
- **Theme**: Custom "vitae" theme located in `themes/vitae/`
- **Configuration**: `config.toml` for site settings

### Frontend Technologies
- **CSS**: Custom CSS with modern features
  - CSS Custom Properties for theming
  - Responsive design patterns
  - Dark mode implementation
- **JavaScript**: Minimal JS for enhanced features
  - Dark mode toggle (`public/js/dark-mode.js`)
  - KaTeX auto-render for math (`public/js/auto-render.js`)
- **Typography**: Roboto font family via web fonts
- **Icons**: Font Awesome integration

### Deployment & Hosting
- **GitHub Actions**: Automated deployment via `.github/workflows/gh-pages.yml`
- **GitHub Pages**: Static hosting
- **Custom Domain**: `peterfaria.me` configured via `CNAME` file

## Directory Structure & Key Files

```
├── config.toml              # Hugo configuration
├── CNAME                    # GitHub Pages custom domain
├── content/
│   └── posts/               # Blog posts in Markdown
├── static/                  # Static assets (images, CSS, etc.)
├── public/                  # Generated site output (build artifacts)
├── themes/
│   └── vitae/               # Custom Hugo theme
├── layouts/                 # HTML templates
└── .github/
    └── workflows/
        └── gh-pages.yml     # Deployment automation
```

## Content Management

### Blog Posts
- **Location**: `content/posts/`
- **Format**: Markdown files with Hugo front matter
- **Naming**: Date-prefixed format (e.g., `20230204-f-sharp-ces.md`)
- **Categories**: Programming, technical tutorials

### Static Assets
- **Images**: `static/images/` and `public/images/`
- **Favicons**: Complete favicon set in both `static/` and `public/`
- **Custom CSS**: `static/css/peterfaria.css` for overrides

## Styling Architecture

### CSS Organization
1. **`public/css/main.css`** - Primary stylesheet with:
   - CSS custom properties for theming
   - Responsive layout rules
   - Typography definitions
2. **`public/css/all.css`** - Font Awesome icons
3. **`public/css/katex.css`** - Math rendering styles
4. **`public/css/syntax.css`** - Code syntax highlighting
5. **`public/css/normalize.css`** - CSS reset

### Design Patterns
- **Mobile-first responsive design**
- **CSS Grid and Flexbox** for layouts
- **Dark mode support** via CSS custom properties
- **Semantic HTML** structure

## Features & Functionality

### Core Features
- **Responsive Design**: Mobile-optimized layout
- **Dark Mode**: JavaScript-toggled theme switching
- **Math Rendering**: KaTeX for mathematical expressions
- **Syntax Highlighting**: Code block highlighting
- **Social Integration**: GitHub, Mastodon, RSS links
- **SEO Optimization**: Meta tags, Open Graph, Twitter Cards

### Performance Features
- **Static Generation**: Pre-built HTML for fast loading
- **Web Fonts**: Optimized font loading
- **Minimal JavaScript**: Performance-focused approach

## Development Workflow

### Local Development
1. Hugo serves content from `content/` directory
2. Static assets processed from `static/` to `public/`
3. Theme templates in `themes/vitae/` or `layouts/`

### Deployment Process
1. **Trigger**: Push to main branch
2. **Build**: GitHub Actions runs Hugo build
3. **Deploy**: Generated `public/` content deployed to GitHub Pages
4. **Domain**: Served via custom domain `peterfaria.me`

## Configuration Patterns

### Hugo Configuration (`config.toml`)
- Site metadata and social links
- Theme configuration
- Content organization settings
- Build and deployment parameters

### GitHub Actions Workflow
- **Build Step**: Hugo site generation
- **Deploy Step**: GitHub Pages deployment
- **Dependencies**: Node.js for any build tools

## Content Creation Guidelines

### Blog Post Structure
- **Front Matter**: Title, date, tags, categories
- **Content**: Markdown with Hugo shortcodes
- **Images**: Reference images in `static/images/`
- **Math**: Use KaTeX syntax for mathematical expressions

### File Naming Conventions
- **Posts**: `YYYYMMDD-slug.md` format
- **Images**: Descriptive names in `static/images/`
- **Assets**: Organized by type in `static/` subdirectories

## Customization Points

### Theme Customization
- **Templates**: Override in `layouts/` directory
- **Styles**: Add to `static/css/peterfaria.css`
- **Scripts**: Add to `static/js/` if needed

### Content Types
- **Posts**: Primary content type for blog articles
- **Pages**: Static pages (about, contact, etc.)
- **Categories/Tags**: Automatic taxonomy generation

## AI Agent Guidelines

### When Working with This Codebase
1. **Respect Hugo conventions** - Follow Hugo's directory structure and naming patterns
2. **Maintain responsive design** - Ensure changes work across device sizes
3. **Preserve dark mode** - Test changes in both light and dark themes
4. **Consider performance** - Keep the static site lightweight and fast
5. **Follow existing patterns** - Match the established code style and organization

### Common Tasks
- **Adding Posts**: Create Markdown files in `content/posts/`
- **Styling Changes**: Update `static/css/peterfaria.css`
- **Configuration**: Modify `config.toml` for site settings
- **Asset Management**: Add files to appropriate `static/` subdirectories

### Testing & Validation
- **Local Development**: Use Hugo's built-in server for testing
- **Build Verification**: Ensure `public/` generates correctly
- **Cross-browser Testing**: Verify compatibility across browsers
- **Mobile Testing**: Validate responsive behavior

## Technical Constraints

### Hugo Limitations
- **Static Generation**: No server-side processing
- **Build Time**: Large sites may have longer build times
- **Template Syntax**: Hugo's Go-based templating

### GitHub Pages Constraints
- **File Size**: Repository size limits
- **Build Time**: GitHub Actions time limits
- **Custom Domains**: DNS configuration requirements

This context document should be referenced when making changes to ensure consistency with the existing architecture and patterns.
