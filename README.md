# Jinyao Chen - Personal Website

My personal portfolio built with Jekyll and hosted on GitHub Pages.

🌐 **Live Site:** [https://cjy513203427.github.io](https://cjy513203427.github.io)

## About

This website puts independent products and developer tools first: KpopBase, ChipRanks, an InterSystems IRIS MCP server, and an ObjectScript VS Code extension. Research, professional experience, and education follow the portfolio.

## Features

- Live projects with local screenshots and direct website links
- Expandable feature previews for KpopBase and ChipRanks
- Open-source tools, research, and contributions
- Professional experience, education, and contact links
- Responsive layout, keyboard navigation, and reduced-motion support

## Tech Stack

- **Jekyll** - Static site generator
- **GitHub Pages** - Hosting
- **Custom Layout** - Responsive portfolio with self-hosted fonts
- **GitHub Actions** - Automated deployment

## Local Development

```bash
# Install Jekyll and dependencies
gem install bundler jekyll

# Run local server
bundle exec jekyll serve

# Visit http://localhost:4000
```

## Deployment

The site automatically deploys via GitHub Actions when changes are pushed to the `master` branch.

## Structure

```
.
├── _layouts/          # Custom layout templates
│   └── default.html   # Main page layout
├── _includes/
│   └── project-card.html # Reusable project showcase
├── _data/
│   └── projects.yml    # Live projects and feature previews
├── assets/
│   ├── css/
│   │   └── style.scss # Custom styles
│   ├── img/           # Portrait and icons
│   └── projects/      # Project screenshots
├── _config.yml        # Jekyll configuration
└── index.md           # Homepage content
```

Edit `_data/projects.yml` to update the featured projects, website links, tags, or screenshot previews. The page uses native HTML disclosures and works without JavaScript.

## Contact

- **Email:** cjy513203427@gmail.com
- **GitHub:** [@cjy513203427](https://github.com/cjy513203427)
- **LinkedIn:** [jinyao-chen](https://www.linkedin.com/in/jinyao-chen-8b5b29225/)
- **Google Scholar:** [Jinyao Chen](https://scholar.google.com/citations?user=NfZXs0sAAAAJ&hl)

---

© 2024 Jinyao Chen. All rights reserved.
