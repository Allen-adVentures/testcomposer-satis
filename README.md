# Satis Server Setup

This guide walks you through setting up a [Satis](https://getcomposer.org/doc/articles/handling-private-packages-with-satis.md) Composer repository server for private or custom PHP packages.

---

## 🚀 GitHub Pages Deployment

This repository is configured for automatic deployment to GitHub Pages. The Satis repository will be available at:
**https://allen-adventures.github.io/testcomposer-satis**

### Setup Steps:

1. **Enable GitHub Pages**: Go to your repository Settings → Pages → Source: Deploy from a branch → Branch: gh-pages → Save
2. **Push Changes**: The GitHub Actions workflow will automatically build and deploy when you push to the main branch
3. **Access Your Repository**: Your Composer repository will be available at the GitHub Pages URL

### Manual Deployment (Alternative)

If you prefer manual deployment, you can use the provided Makefile:

```bash
# Build and deploy manually
make all

# Or step by step:
make install
make build
make deploy
```

### Using the Repository:

Add this to your project's `composer.json`:

```json
{
    "repositories": [
        {
            "type": "composer",
            "url": "https://allen-adventures.github.io/testcomposer-satis"
        }
    ]
}
```

**NOTE**: The actual composer repository is on the `gh-pages` branch.

---

### 🛠 Create satis.json

In the root of your project, create a satis.json file with the following content:

```json
{
    "name": "my/repository",
    "homepage": "http://packages.example.org",
    "repositories": [
        { "type": "vcs", "url": "https://github.com/mycompany/privaterepo" },
        { "type": "vcs", "url": "http://svn.example.org/private/repo" },
        { "type": "vcs", "url": "https://github.com/mycompany/privaterepo2" }
    ],
    "require": {
        "company/package": "*",
        "company/package2": "*",
        "company/package3": "2.0.0"
    },
    "require-all": false,
    "require-dependencies": true,
    "require-dev-dependencies": true,
    "config": {
        "secure-http": true
    },
    "output-dir": "public",
    "archive": {
        "directory": "dist",
        "format": "tar",
        "prefix-url": "https://your-domain.com/composer/dist",
        "skip-dev": false
    }
}
```

# Details:

"repositories": VCS sources (e.g., GitHub, GitLab, Bitbucket).

"require": Packages to include (version tag must exist like v1.0.0).

"output-dir": Directory where the generated Composer repository will be written. This should be publicly served (e.g., by Nginx).

"archive" (optional): Creates downloadable tarballs. Speeds up installs and adds security.

Documentaion:
[handling-private-packages](https://getcomposer.org/doc/articles/handling-private-packages.md)

---

### 🏷 Tag Your Private Package(s)

Ensure your custom/private packages are tagged with semantic versions:

```bash
  cd path/to/your/package
  git tag v1.0.0
  git push origin v1.0.0
```

---

### 🏗 Build the Satis Repository

Run the following to generate the static Composer repo:

```bash
  php bin/satis build satis.json public/
```

Re-run the build command anytime you update satis.json or push new tags

---

### 🔐 Handling Private Repositories

If your VCS repository is private, you’ll need to provide authentication.

GitHub OAuth:

```bash
  composer config --global github-oauth.github.com your_token
```

HTTP Basic Authentication:

```bash
  composer config http-basic.github.com your_username your_password
```

---

[Official Satis Documentaion Link](https://github.com/composer/satis)
