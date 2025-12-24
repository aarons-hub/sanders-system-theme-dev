# Sanders System Theme v1.0

A child theme based on Twenty Twenty-Five for WordPress, created by Aaron Sanders.

## Description

Sanders System is a custom WordPress theme that extends Twenty Twenty-Five with custom styling, blocks, and functionality.

## Features

- Custom gradient backgrounds
- Sticky header with scroll animation
- Custom button hover effects
- Contact form styling (Contact Form 7)
- Responsive mobile design
- Custom WordPress blocks

## Development

### Prerequisites

- Node.js (v20 or higher recommended)
- npm
- WordPress installation with Twenty Twenty-Five parent theme

### Installation

1. Clone this repository into your WordPress themes directory:
   ```bash
   cd wp-content/themes/
   git clone <repository-url> sanders-system-theme-dev
   ```

2. Install dependencies:
   ```bash
   npm install
   ```

3. Build the theme:
   ```bash
   npm run build
   ```

### Development Commands

- `npm start` - Start development mode with hot reload
- `npm run build` - Build production-ready theme
- `npm run format` - Format code
- `npm run lint:css` - Lint CSS files
- `npm run lint:js` - Lint JavaScript files

## GitHub Workflows and Secrets

If you need to set up automated workflows for deployment or CI/CD, see the comprehensive guide:

**[📖 How to Create Secret Tokens for Workflows](./WORKFLOW_SECRETS.md)**

This guide covers:
- Creating and managing GitHub secrets
- Using secrets in workflow files
- Security best practices
- Complete deployment examples
- Troubleshooting common issues

## Theme Structure

```
sanders-system-theme-dev/
├── build/           # Compiled blocks (generated)
├── src/             # Source files for custom blocks
├── css/             # Additional stylesheets
├── js/              # JavaScript files
├── parts/           # Template parts
├── styles/          # Block styles
├── templates/       # Page templates
├── functions.php    # Theme functions
├── style.css        # Main theme stylesheet
└── theme.json       # Theme configuration
```

## Credits

- **Theme**: Sanders System v1.0
- **Author**: Aaron Sanders
- **Based on**: Twenty Twenty-Five
- **WordPress Version**: 6.0+

## License

GPL-2.0-or-later

## Support

For issues, questions, or contributions, please use the GitHub repository issue tracker.
