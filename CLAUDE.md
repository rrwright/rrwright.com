# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a **Zola static site generator** project for rrwright.com. Zola is a fast static site generator written in Rust.

## Build Commands

```bash
# Build the site (output to public/)
zola build

# Run local development server (http://127.0.0.1:1111)
zola serve

# Validate site structure
zola check
```

## Technology Stack

- **Zola** - Static site generator (install via `brew install zola`)
- **Tera** - Template engine
- **SCSS** - Stylesheets (auto-compiled via `compile_sass = true`)
- **Elasticlunr.js** - Client-side search (index generated at build time)

## Directory Structure

- `content/` - Markdown content files
- `templates/` - Tera templates
- `sass/` - SCSS stylesheets
- `static/` - Static assets (images, JS, etc.)
- `themes/` - Zola themes
- `public/` - Built output (generated)

## Configuration

Main config is `config.toml`. Key settings:
- Syntax highlighting uses `base16-ocean-dark` theme
- Sass compilation is enabled
- Atom feed generation is enabled (`atom.xml`)

## Deployment

GitHub Actions workflow in `.github/workflows/deploy.yml` handles deployment.
