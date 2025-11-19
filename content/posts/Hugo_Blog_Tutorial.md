---
title: "How to Set Up a Hugo Blog with Docker and Archie Theme"
date: 19 Nov 2025
draft: false
description: "Complete tutorial for creating a Hugo blog with Docker, Caddy, and the Archie theme"
tags: ["hugo", "docker", "tutorial", "blogging"]
---

# Complete Hugo Blog Setup Guide

This guide will walk you through setting up a Hugo blog using Docker, the Archie theme, and Caddy web server.

## Prerequisites

- VPS or local machine with Docker installed
- Basic command line knowledge

## Step 1: Create Project Structure

```bash
# Create new project directory
mkdir ~/hugo-blog && cd ~/hugo-blog

# Create Hugo site using latest image
docker run --rm -v $(pwd):/src hugomods/hugo:latest new site blog-site

# Fix ownership (Docker creates files as root)
sudo chown -R $USER:$USER blog-site/
```

## Step 2: Set Up Archie Theme

```bash
cd blog-site
git init

# Add Archie theme
git submodule add https://github.com/athul/archie.git themes/archie
```

Create configuration file with proper settings:

```bash
cat > hugo.toml << 'EOF'
baseURL = "/"
languageCode = "en-us"
title = "My Blog"
theme = "archie"
copyright = "© Your Name"

# IMPORTANT: Force relative URLs for proper CSS loading
relativeURLs = true
canonifyURLs = false

# Code highlighting
pygmentsstyle = "monokai"
pygmentscodefences = true
pygmentscodefencesguesssyntax = true

[pagination]
  pagerSize = 3

[params]
    dateFormat = "02 Jan 2006"
	mode = "auto"
	useCDN = false
	subtitle = "Minimal and Clean blog theme"

[[params.social]]
name = "GitHub"
icon = "github"
url = "https://github.com/username"

[[menu.main]]
name = "Home"
url = "/"
weight = 1

[[menu.main]]
name = "Posts"
url = "/posts"
weight = 2
EOF
```

## Step 3: Create Your First Post

```bash
# Create posts directory
mkdir -p content/posts

# Create welcome post with proper front matter
cat > content/posts/first_post.md << 'EOF'
---
title: "Welcome to my blog!!!"
date: 2025-11-19T13:00:00Z
draft: false
description: "Welcome post"
tags: ["blogging"]
---

## First blog post

Welcome to my new Hugo blog! This is my first post using the Archie theme.
EOF
```

## Step 4: Build static site using hugo and the MD files

```bash
docker run --rm -v $(pwd):/src hugomods/hugo:latest --minify
```

## Step 5: Set up Caddy web server
Make sure the port isn't already used
```bash
cd ~/hugo-blog
cat > docker-compose.yml << 'EOF'
services:
  web:
    image: caddy:2-alpine
    ports:
      - "8085:80"
    volumes:
      - ./Caddyfile:/etc/caddy/Caddyfile
      - ./blog-site/public:/srv
    restart: unless-stopped
EOF
```
# Create the Caddyfile
```bash
cat > Caddyfile << 'EOF'
:80 {
    root * /srv
    file_server
    encode gzip
}
EOF
```
## Step 6: Run the webserver
```bash
docker compose up -d
```
#The site should be accessible at [http://localhost:8085/](http://localhost:8085/)

## Key Success Factors

- ✅ Use `hugomods/hugo:latest` (newer Hugo version)
- ✅ Set `relativeURLs = true` (critical for CSS loading)
- ✅ Theme uses `assets/` directory (needs processing)
- ✅ Verify `public/css/` has generated files

---

# How to Create New Posts

## Basic Post Structure

```markdown
---
title: "Your Post Title"
date: 2025-11-10T14:00:00Z
draft: false
description: "Brief description"
tags: ["tag1", "tag2"]
---
---> Your markdown content <---
```
## Publishing Workflow

1. Create `.md` file in `content/posts/`
2. Run: `docker run --rm -v $(pwd):/src hugomods/hugo:latest --minify`
3. Rerun `docker compose up -d`

Happy blogging! 

Next up: How to automate the webserver using github actions
