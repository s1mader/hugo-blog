---
title: "How to Set Up a Hugo Blog with Docker and Archie Theme-incomplete"
date: 2025-11-10T13:00:00Z
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
````bash
# Create new project directory
mkdir ~/hugo-blog && cd ~/hugo-blog

# Create Hugo site using latest image
docker run --rm -v $(pwd):/src hugomods/hugo:latest new site blog-site

# Fix ownership (Docker creates files as root)
sudo chown -R $USER:$USER blog-site/
````

## Step 2: Set Up Archie Theme
````bash
cd blog-site
git init

# Add Archie theme
git submodule add https://github.com/athul/archie.git themes/archie

# Create configuration file
cat > config.toml << 'EOF'
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
