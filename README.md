# 🚀 Templates Repository <sup><kbd>Pre-release</kbd></sup>

[![MIT License](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](../../pulls)
[![Bootstrap](https://img.shields.io/badge/Built%20with-Bootstrap-7952B3.svg)](https://getbootstrap.com/)
![Pre-release](https://img.shields.io/badge/status-pre--release-yellow)
<!-- Add more badges as needed (coverage, CI status, etc.) -->

> **Pre-release:** This repository is under active development. Features and templates may change frequently.

---

## 📝 Overview

Welcome to the **Templates** repository!  
This collection provides semi-modern, Bootstrap-based HTML/CSS templates to help you:

- Kickstart new web projects
- Prototype website ideas
- Standardize layouts across multiple pages or projects

All templates use the `{{variable}}` syntax for fast and flexible customization.

---

## 📖 Table of Contents

- [Overview](#-overview)
- [Features](#-features)
- [Getting Started](#-getting-started)
- [Design Naming Convention](design-naming-convention)
- [Automating Variable Replacement](#-automating-variable-replacement)
- [Available Templates](#-available-templates)
- [Contributing](#-contributing)
- [License](#-license)
- [Contact](#-contact)

---

## ✨ Features

- Responsive layouts with [Bootstrap](https://getbootstrap.com/)
- Customizable with `{{variable}}` placeholders
- Clear directory structure
- Simple, ready-to-use HTML & CSS
- Suitable for landing pages, apps, and more

---

## 🚦 Getting Started

1. **Browse** the templates directory and pick a template.
2. **Copy** the template files you need into your project.
3. **Replace** all `{{variable}}` placeholders with your values (manually or with a script).

> **Tip:** Always check for any unreplaced variables before deploying.

---

## 🗂️ Design Naming Convention

All template designs in this repository follow a specific encoding system to describe their layout and configuration. The naming convention is as follows:

`Columns | Menu | Framework | Standard | Color`

Where each segment represents:

- **Columns (\*)**: The number of columns in the layout (1, 2, or 3).  
- **Menu (\*)**: Position of the menu.  
  - `R` = Right  
  - `L` = Left  
- **Framework (\*)**: The first letter of the framework used (A-Z).  
- **Standard (\*)**:  
  - `S` = Standard  
  - `N` = Non Standard  
  - `C` = Compact  
- **Color (\*)**: Any number between 0 and 20 to represent the color scheme.

**Example:**  
`3-CMRBS1`  
Breakdown:  
- `3` = 3 columns  
- `C` = Columns 
- `M` = Menu is:
- `R` = Right  
- `B` = Framework starting with "B" (Bootstrap)  
- `S` = Standard  
- `1` = Color scheme 1

This system allows for quick identification of each template's structure and style at a glance.

---

## ⚡ Automating Variable Replacement

You can automate the replacement of `{{variable}}` placeholders using a script.  
For example, here’s a PHP function you can adapt:

```php
function parse_tpl($tpl, $vars) {
    return preg_replace_callback('/{{([a-z0-9._\\- ]+)}}/i', function($matches) use ($vars) {
        return isset($vars[$matches[1]]) ? $vars[$matches[1]] : '';
    }, $tpl);
}
```
*See the project for more advanced usage.*

---

## 📦 Available Templates

- Semi-modern Bootstrap HTML/CSS layouts
- Landing pages
- Basic web app shells

> Each template folder may include specific instructions or extra notes.

---

## 🤝 Contributing

Contributions are welcome! To add or improve templates:

1. Fork the repository and create a new branch.
2. Add or update template files (use `{{variable}}` placeholders).
3. Add a brief description at the top of your template file.
4. Open a pull request describing your changes.

Please follow [code of conduct](CODE_OF_CONDUCT.md) and best practices.

---

## 📄 License

This repository is licensed under the [MIT License](LICENSE).

---

## 📬 Contact

For questions, suggestions, or template requests:  
Open an [issue](../../issues) or contact the maintainer.

---

> Made with ❤️ by Elisei N. for “Loro web service”
