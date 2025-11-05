# Portfolio Website

A minimal, static portfolio website built with Astro and TailwindCSS, optimized for performance (Lighthouse 95+).

## Features

- 🚀 **Zero client-side frameworks** - Pure static HTML/CSS
- 📱 **Mobile-first responsive design**
- ♿ **Accessible** - Semantic HTML, skip-to-content link, proper focus styles
- 🔍 **SEO optimized** - OpenGraph, Twitter cards, sitemap, robots.txt
- ⚡ **Performance focused** - Lazy-loading images, system fonts, minimal JS
- 🎨 **Modern UI** - Clean design with TailwindCSS

## Pages

- `/` - Homepage with hero section and 3 CTAs
- `/projects` - Project showcase with cards from JSON file
- `/resume` - PDF resume viewer with download button
  

## Local Development

### Prerequisites

- Node.js 18+ and npm

### Setup

1. Install dependencies:
```bash
npm install
```

2. Start the development server:
```bash
npm run dev
```

3. Open your browser to `http://localhost:4321`

### Development Commands

- `npm run dev` - Start development server
- `npm run build` - Build for production
- `npm run preview` - Preview production build locally

## Build

Build the static site:

```bash
npm run build
```

The output will be in the `dist/` directory, ready to be served by any static hosting service.

## Deployment to Nginx on Raspberry Pi

### Step 1: Build the Site

On your development machine:

```bash
npm run build
```

### Step 2: Transfer Files to Raspberry Pi

Use `scp` or `rsync` to transfer the `dist/` directory:

```bash
# Using scp
scp -r dist/ pi@your-pi-ip:/var/www/portfolio/

# Or using rsync (recommended)
rsync -avz --delete dist/ pi@your-pi-ip:/var/www/portfolio/
```

### Step 3: Configure Nginx

Create or edit `/etc/nginx/sites-available/portfolio`:

```nginx
server {
    listen 80;
    server_name yourdomain.com www.yourdomain.com;

    root /var/www/portfolio;
    index index.html;

    # Enable gzip compression
    gzip on;
    gzip_vary on;
    gzip_min_length 1024;
    gzip_types text/plain text/css text/xml text/javascript application/x-javascript application/xml+rss application/javascript application/json;

    # Cache static assets
    location ~* \.(jpg|jpeg|png|gif|ico|css|js|svg|woff|woff2|ttf|eot)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }

    # Handle all routes (SPA-like behavior)
    location / {
        try_files $uri $uri/ /index.html;
    }

    # Security headers
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header X-XSS-Protection "1; mode=block" always;
    add_header Referrer-Policy "no-referrer-when-downgrade" always;
}
```

### Step 4: Enable the Site

```bash
# Create symlink
sudo ln -s /etc/nginx/sites-available/portfolio /etc/nginx/sites-enabled/

# Test configuration
sudo nginx -t

# Reload Nginx
sudo systemctl reload nginx
```

### Step 5: Set Up SSL (Optional but Recommended)

Install Certbot:

```bash
sudo apt update
sudo apt install certbot python3-certbot-nginx
```

Get SSL certificate:

```bash
sudo certbot --nginx -d yourdomain.com -d www.yourdomain.com
```

Certbot will automatically configure Nginx for HTTPS.

## Customization

### Update Site Information

1. **Site URL**: Update `site` in `astro.config.mjs`
2. **Email**: Update the mailto in `src/layouts/BaseLayout.astro` footer
3. **LinkedIn**: Update the footer link in `src/layouts/BaseLayout.astro`
4. **Name**: Update name in `src/pages/index.astro`

### Add Projects

Edit `content/projects.json` to add, remove, or modify projects. The structure is:

```json
{
  "id": "unique-id",
  "title": "Project Title",
  "description": "Project description",
  "tech": ["Tech1", "Tech2"],
  "links": {
    "live": "https://example.com",
    "github": "https://github.com/example"
  },
  "thumbnail": "/images/project.jpg"
}
```

### Add Resume PDF

Place your resume PDF in the `public/` directory as `resume.pdf`. The file will be automatically available at `/resume.pdf`.

**Note**: You need to add your own `resume.pdf` file to the `public/` directory before building or deploying.

### Update Project Images

Place project thumbnails in `public/images/` and reference them in `content/projects.json` as `/images/filename.jpg`.

## Performance Tips

- Images are lazy-loaded by default
- Use system fonts (already configured)
- All assets are optimized during build
- Enable gzip compression in Nginx (see config above)
- Set proper cache headers (see Nginx config above)

## Browser Support

- Modern browsers (Chrome, Firefox, Safari, Edge)
- Mobile browsers (iOS Safari, Chrome Mobile)
- Progressive enhancement ensures basic functionality in older browsers

## License

MIT

