- Consistent with real life: in line with the process and logic of real life, and comply with languages and habits that the users are used to.
- Consistent within interface: all elements should be consistent, such as: design style, icons and texts, position of elements, etc.

## Feedback
- Operation feedback: enable the users to clearly perceive their operations by style updates and interactive effects.
- Visual feedback: reflect current state by updating or rearranging elements of the page.

## Efficiency
- Simplify the process: keep operating process simple and intuitive.
- Definite and clear: enunciate your intentions clearly so that the users can quickly understand and make decisions.
- Easy to identify: the interface should be straightforward, which helps the users to identify and frees them from memorizing and recalling.

## Controllability
- Decision making: giving advices about operations is acceptable, but do not make decisions for the users.
- Controlled consequences: users should be granted the freedom to operate, including canceling, aborting or terminating current operation.

# Installation

> **Zola** is a prerequisite. Please refer to the [Zola installation](https://www.getzola.org/documentation/getting-started/installation/) docs.

First download this theme to your `themes` directory:

```bash
$ cd themes
$ git clone https://github.com/angular-rust/ruex.git
```

or add as a submodule
```bash
$ git submodule add https://github.com/angular-rust/ruex  themes/ruex
```

and then enable it in your `config.toml`:

```toml
theme = "ruex"
```

# Structure

## Hero

**Angular Rust** is designed for product websites, hence we let **hero** part fills whole of screen.
You can customize your **hero** by using `hero` block in the `index.html`.

```html
{% block hero %}
    <div>
        Your cool hero html...
    </div>
{% endblock hero %}
```

## Page

Every markdown file located in `content` directory will become a **Page**. There also will display as
a navigate link on the top-right corner. 
You can change the frontmatter's `weight` value to sort the order (ascending order).

```
+++
title = "Changelog"
description = "Changelog"
weight = 2
+++

```

## CSS variables

You can override theme variable by creating a file named `_variables.html` in your `templates` directory.

```html
<style>
    :root {
        /* Primary theme color */
        --primary-color: #FED43F;
        /* Primary theme text color */
        --primary-text-color: #543631;
        /* Primary theme link color */
        --primary-link-color: #F9BB2D;
        /* Secondary color: the background body color */
        --secondary-color: #fcfaf6;
        --secondary-text-color: #303030;
        /* Highlight text color of table of content */
        --toc-highlight-text-color: #d46e13;
    }
</style>
```

# Configuration

You can customize some builtin property in `config.toml` file:

```toml
[extra]
logo_name = "Angular Rust"
logo_path = "ruex.svg"
extra_menu = [
    { title = "Github", link = "https://github.com/angular-rust/ruex"}
]
```

# Showcases

Please see the [showcases page](/showcases).

# Contributing

Thank you very much for considering contributing to this project!

We appreciate any form of contribution:

- New issues (feature requests, bug reports, questions, ideas, ...)
- Pull requests (documentation improvements, code improvements, new features, ...)