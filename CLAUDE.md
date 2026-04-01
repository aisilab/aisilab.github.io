# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Jekyll-based static website for the AI Safety & Interpretability Lab (AISI Lab) at the University of Southern Denmark. The site uses the remote theme`lgalke/swiss` and is hosted on GitHub Pages.

## Development Commands

### Local Development
```bash
bundle install              # Install dependencies
bundle exec jekyll serve    # Run local dev server (http://localhost:4000)
bundle exec jekyll build    # Build the site to _site/ directory
```

### Deployment
The site is automatically deployed via GitHub Pages when changes are pushed to the main branch. No manual deployment needed.

## Site Architecture

### Key Configuration
- **_config.yml**: Central configuration file containing site title, description, social links, theme settings, and collection definitions
- **Remote theme**: Uses `lgalke/swiss` theme from GitHub
- **Base URL**: https://aisilab.github.io

### Content Structure

#### Pages
Content pages are markdown files in the root directory:
- **index.html**: Homepage using the `home` layout
- **research.md**: Research areas and publications (/research/)
- **people.md**: Team members page (/team/) - renders as "Team" in navigation
- **teaching.md**: Teaching information

#### Collections
**_team/**: Team member profiles as individual markdown files with front matter:
```yaml
---
name: [Full Name]
position: [Job Title]
image: [Path to photo]
photocredit: [Photographer credit]
photocredit_url: [Optional link]
---
[Biography in markdown]
```

Team members are rendered in the order specified in `_config.yml` under `collections.team.order`.

### Custom Components

#### Layouts (_layouts/)
- **home.html**: Custom homepage layout with header, description boxes, and navigation

#### Includes (_includes/)
Reusable HTML components for embedding:
- **publication.html**: Renders publication entries with title, authors, venue, abstract (collapsible), and links (paper, code, data, authorcopy)
- **figure.html**: Embeds images with captions
- **footer.html**, **head.html**: Standard page elements

#### Using Includes
In markdown files, use Liquid syntax:
```liquid
{% include publication.html
    title="Paper Title"
    authors="Author Names"
    venue="Conference/Journal Name"
    year="2023"
    paper_url="https://..."
    code_url="https://..."
    data_url="https://..."
    authorcopy_url="https://..."
    abstract="Optional abstract text"
%}

{% include figure.html
    path="/assets/images/filename.png"
    caption="Caption text"
    alt="Alt text"
%}
```

### Assets
- **assets/images/**: Image files for team photos, figures, and diagrams

## Content Editing Guidelines

### Adding Team Members
1. Create new file in `_team/` directory (e.g., `firstname-lastname.md`)
2. Add front matter with name, role, image path, and photo credits
3. Write biography below front matter
4. Update `_config.yml` collections.team.order to control display sequence

### Adding Publications
Edit `research.md` and add new publication using the `{% include publication.html %}` syntax with appropriate parameters.

### Theme Customization
Theme color can be changed in `_config.yml` (options: black, blue, gray, magenta, orange, red, white, yellow).

## Jekyll Specifics

- Changes to `_config.yml` require restarting the Jekyll server
- Front matter in markdown files controls layout and metadata
- Liquid templating is used for dynamic content rendering
- GitHub Pages automatically rebuilds on push to main branch
