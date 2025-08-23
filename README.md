# Templates Repository

Welcome to the **Templates** repository!

This repository contains a collection of semi-modern Bootstrap-based HTML/CSS templates. These templates are designed to help you quickly start new web projects, prototype ideas, or standardize the look and feel of your frontend applications.

All templates in this repository use `{{variables}}` syntax for easy customization. You can replace these placeholders manually or automate the process using a script.

## Table of Contents

- [Overview](#overview)
- [How to Use](#how-to-use)
- [Automating Variable Replacement](#automating-variable-replacement)
- [Available Templates](#available-templates)
- [Contributing](#contributing)
- [License](#license)

## Overview

The templates are built using HTML and CSS with [Bootstrap](https://getbootstrap.com/) for responsive, modern layouts. Each template includes placeholder variables such as `{{title}}`, `{{description}}`, or `{{author}}`, allowing you to tailor the content to your needs.

## How to Use

1. **Browse the templates** to find one that suits your project.
2. **Copy the template** to your project directory.
3. **Replace all `{{variables}}`** in the template with your own values. You can do this manually in your text editor.

## Automating Variable Replacement

To speed up template customization, you can use a simple script to replace variables automatically. For example, here is a Bash script you can adapt:

```bash
# Usage: ./replace-vars.sh template.html output.html title="My Site" author="Eliseo"
cp "$1" "$2"
shift 2
for var in "$@"
do
  key=$(echo "$var" | cut -d= -f1)
  value=$(echo "$var" | cut -d= -f2-)
  sed -i "s/{{${key}}}/${value}/g" "$2"
done
```

> **Tip:** Review your output file to ensure all placeholders have been replaced as expected.

## Available Templates

- Semi-modern Bootstrap HTML/CSS layouts
- Landing pages
- Simple web app shells

Check each directory for detailed usage instructions or extra notes.

## Contributing

Contributions are welcome! To add a new template or improve existing ones:

1. Fork the repository and create a new branch.
2. Add or update template files (use `{{variable}}` for placeholders).
3. Add a short description at the top of your template.
4. Open a pull request describing your changes.

## License

This repository is licensed under the [MIT License](LICENSE).

---

For questions, suggestions, or template requests, please open an issue!
