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

To speed up template customization, you can use a simple script to replace variables automatically. For example, here is a PHP script you can adapt:

```php
function parse_tpl($tpl, $vars, $main=false) {
    global $_conf;

    // preserve original comment-stripping behavior
    if (function_exists('strip_comments')) {
        $tpl = strip_comments($tpl);
    }

    // preserve original global vars behavior (safe for PHP <7)
    $vars['domain'] = isset($_conf['domain']) ? $_conf['domain'] : '';
    $vars['home']   = isset($_conf['home']) ? $_conf['home'] : '';

    $tag_pattern = '/{{([0-9a-z._\- ]+)(\(.*?\))?}}/si';

    // Process tags left-to-right until none remain
    while (preg_match($tag_pattern, $tpl, $m, PREG_OFFSET_CAPTURE)) {
        $full_tag   = $m[0][0];
        $tag_pos    = $m[0][1];
        $name_raw   = isset($m[1][0]) ? $m[1][0] : '';
        $params_raw = isset($m[2][0]) ? $m[2][0] : '';

        // safety: empty name -> remove and continue
        if ($name_raw === '') {
            $tpl = substr_replace($tpl, '', $tag_pos, strlen($full_tag));
            continue;
        }

        // ---------- if_exists (use empty() to match original behavior) ----------
        if (substr($name_raw, 0, 10) === 'if_exists ') {
            $var_name  = trim(substr($name_raw, 10));
            $open_tag  = '{{'.$name_raw.'}}';
            $close_tag = '{{end '.$name_raw.'}}';
            $block_info = find_matching_end($tpl, $open_tag, $close_tag, $tag_pos);
            if ($block_info === false) {
                // malformed: remove open tag only
                $tpl = substr_replace($tpl, '', $tag_pos, strlen($open_tag));
                continue;
            }
            list($inner_start, $inner_len, $close_pos) = $block_info;
            // IMPORTANT: mimic original empty() semantics exactly
            if (empty($vars[$var_name])) {
                // remove whole block
                $tpl = substr_replace($tpl, '', $tag_pos, $close_pos - $tag_pos);
            } else {
                // keep inner content, remove wrapper tags
                $inner = substr($tpl, $inner_start, $inner_len);
                $tpl = substr_replace($tpl, $inner, $tag_pos, $close_pos - $tag_pos);
            }
            continue;
        }

        // ---------- if_not_exists (use !empty()) ----------
        if (substr($name_raw, 0, 14) === 'if_not_exists ') {
            $var_name  = trim(substr($name_raw, 14));
            $open_tag  = '{{'.$name_raw.'}}';
            $close_tag = '{{end '.$name_raw.'}}';
            $block_info = find_matching_end($tpl, $open_tag, $close_tag, $tag_pos);
            if ($block_info === false) {
                $tpl = substr_replace($tpl, '', $tag_pos, strlen($open_tag));
                continue;
            }
            list($inner_start, $inner_len, $close_pos) = $block_info;
            if (!empty($vars[$var_name])) {
                // variable exists -> remove whole block
                $tpl = substr_replace($tpl, '', $tag_pos, $close_pos - $tag_pos);
            } else {
                // keep inner content, remove tags
                $inner = substr($tpl, $inner_start, $inner_len);
                $tpl = substr_replace($tpl, $inner, $tag_pos, $close_pos - $tag_pos);
            }
            continue;
        }

        // ---------- Array / repeated block ----------
        $open_tag  = '{{'.$name_raw.$params_raw.'}}';
        $close_tag = '{{end '.$name_raw.'}}';
        $block_info = find_matching_end($tpl, $open_tag, $close_tag, $tag_pos);

        if ($block_info !== false && isset($vars[$name_raw]) && is_array($vars[$name_raw]) && count($vars[$name_raw])>0) {
            list($inner_start, $inner_len, $close_pos) = $block_info;
            $inner_tpl = substr($tpl, $inner_start, $inner_len);

            // parse optional params like (n,delimiter)
            $n = 0; $delimiter = '';
            if ($params_raw !== '') {
                $ps = trim($params_raw, '()');
                if (preg_match('|([0-9]*),(.*)|si', $ps, $pm)) {
                    $n = (int)$pm[1];
                    $delimiter = trim($pm[2], '"\'');
                }
            }

            $replacement = '';
            $cnt = 0; $cnt_all = 0;
            foreach ($vars[$name_raw] as $item) {
                $cnt_all++; $cnt++;
                if (is_array($item)) {
                    // merge parent context so inner tags can access parent vars like {{title}}
                    $inner_vars = array_merge($vars, $item);
                    $replacement .= parse_tpl($inner_tpl, $inner_vars);
                } else {
                    $replacement .= (string)$item;
                }

                if ($n > 0 && $cnt == $n && $cnt_all < count($vars[$name_raw])) {
                    $replacement .= $delimiter;
                    $cnt = 0;
                }
            }

            // replace the whole block (open .. inner .. close)
            $tpl = substr_replace($tpl, $replacement, $tag_pos, $close_pos - $tag_pos);
            continue;
        }

        // If there is a close tag for this name but no array present -> remove block entirely
        if ($block_info !== false && !(isset($vars[$name_raw]) && is_array($vars[$name_raw]) && count($vars[$name_raw])>0)) {
            list($inner_start, $inner_len, $close_pos) = $block_info;
            $tpl = substr_replace($tpl, '', $tag_pos, $close_pos - $tag_pos);
            continue;
        }

        // ---------- Simple variable replacement (single occurrence) ----------
        $full_open = '{{'.$name_raw.$params_raw.'}}';
        $value = '';
        if (isset($vars[$name_raw])) {
            if (is_array($vars[$name_raw])) {
                $value = implode(',', $vars[$name_raw]);
            } elseif (is_object($vars[$name_raw])) {
                $value = '';
            } else {
                $value = $vars[$name_raw];
            }
        }
        $tpl = substr_replace($tpl, (string)$value, $tag_pos, strlen($full_open));
    }

    return $tpl;
}
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
