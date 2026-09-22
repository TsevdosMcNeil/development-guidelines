# TM WordPress Development Guidelines

These guidelines define the development standards for TM WordPress
projects.

They are intended to keep our websites consistent, maintainable and easy
for another developer to pick up in the future. They apply to current
projects such as **Mulroy**, **Yachtsman**, **Spaces for Creatives** and
**Design Storey**, as well as future WordPress projects.

> **Important:** The Git repository is the source of truth for the
> website's code. If code exists on staging or production but is not
> represented in Git, the development process is not complete.

------------------------------------------------------------------------

## 1. Core Principles

For every WordPress project:

1.  The project must have a repository in the **TM GitHub
    organisation**.
2.  **All source files required to develop and rebuild the website must
    be committed to Git.**
3.  Use standard WordPress functions and APIs wherever possible instead
    of recreating WordPress functionality manually.
4.  **Gutenberg is the default approach for editable page content.**
5.  ACF configuration must be version-controlled when ACF is used.
6.  Follow semantic HTML, accessibility and SEO best practices.
7.  Never make production-only code changes. Changes deployed to staging
    or production must also exist in the repository.
8.  Another developer should be able to clone the repository, read the
    documentation and understand how to work on the project.

------------------------------------------------------------------------

## 2. Git and Repository Requirements

Every WordPress project must have its own repository under the TM GitHub organisation.

Current examples:

* https://github.com/TsevdosMcNeil/design-story
* https://github.com/TsevdosMcNeil/mulroy

When starting a new project, create the repository **before development begins**.

### The repository must contain the source code

Do not commit only compiled/minified assets.

If the project uses any of the following, their source files must be included:

* SCSS
* SASS
* PCSS / PostCSS
* JavaScript source files
* TypeScript
* Pug / Jade
* PHP templates
* Gutenberg block source files
* Tailwind configuration
* PostCSS configuration
* Vite / Webpack / build configuration
* `package.json`
* package lock files
* `composer.json`
* custom plugins
* theme/child-theme source files
* any other files required to rebuild the production assets

For example, committing `style.css` while omitting the SCSS files used to generate it is **not sufficient**.

### Media files and uploads

Normal website content images and other media files **must not be stored inside the theme or committed to the Git repository**.

Images uploaded and managed by content editors should use the standard WordPress Media Library and be stored in:

```text
wp-content/uploads/
```

Do not create custom directories inside the theme such as:

```text
theme/images/
theme/templates/images/
theme/assets/content-images/
```

for images that are actually part of the website's editable content.

Similarly, do not hard-code content images into templates simply to avoid using the WordPress Media Library.

This is important because WordPress should remain responsible for managing content media, including:

* media attachment metadata
* alternative text
* image sizes
* responsive images and `srcset`
* thumbnails and generated image variants
* image replacement and management through the CMS

The Git repository should contain **application/theme assets**, not the website's media library.

There are legitimate exceptions for assets that are part of the theme's functionality or interface rather than editable website content. For example:

* icons
* interface graphics
* SVG assets used by components
* default/fallback images
* loading graphics
* decorative assets that are intrinsically part of the theme
* assets required for a component to function

These files can be stored within the theme and committed to Git.

As a general rule:

> **If an image is website content and could reasonably be changed by an editor, it belongs in the WordPress Media Library, not in the theme or Git repository.**

### Do not commit

Unless there is a specific project requirement, do not commit:

* `node_modules/`
* Composer `vendor/` when it can be recreated from `composer.lock`
* `wp-content/uploads/`
* website content images or media files
* cache directories
* temporary files
* local database dumps
* passwords
* API secrets
* private keys
* `.env` files containing credentials

Use `.gitignore` appropriately.

### Production changes

Do not make changes directly on production and leave them there.

If an emergency change has to be made directly on the server, the same change must be brought back into the repository immediately afterwards.

Otherwise, the next deployment may overwrite it.

------------------------------------------------------------------------

## 3. Docker and Local Development

TM WordPress projects are normally configured to use **Docker** for local development and for the staging environments hosted on the TM Studio server.

Using Docker allows us to keep the development environments consistent between developers and staging, particularly for:

* PHP versions
* MySQL/MariaDB versions
* Node.js versions
* Composer
* PHP extensions
* server configuration
* other project dependencies

For this reason, **using the existing Docker environment for local development is strongly recommended**.

### Repository structure

The repository may contain Docker configuration and other development infrastructure at its root level, while the actual WordPress installation lives inside a directory called:

```text
wordpress/
```

A typical project may look something like:

```text
project/
├── docker-compose.yml
├── .env.example
├── README.md
├── docker/
│   └── ...
│
└── wordpress/
    ├── wp-admin/
    ├── wp-content/
    │   ├── plugins/
    │   └── themes/
    ├── wp-includes/
    └── ...
```

The exact Docker configuration may vary between projects, so always check the project's `README.md` before starting development.

### If you use Docker

If you are comfortable working with Docker, use the Docker configuration supplied with the project.

This is the preferred approach because it helps ensure that your local environment matches the versions and configuration used by the project and staging server.

Before changing PHP, Node.js, database or other dependency versions, check the existing project configuration and consider whether the same change will also be required on staging and production.

Do not update versions locally without considering compatibility with the environments where the website will eventually run.

### If you do not use Docker

Knowledge of Docker is useful but is **not required in order to work on the WordPress codebase**.

If you prefer to use another local development environment, such as MAMP or a native PHP/MySQL setup, you can work directly with the WordPress installation inside:

```text
wordpress/
```

For example, theme development will normally happen somewhere inside:

```text
wordpress/wp-content/themes/
```

and custom plugin development inside:

```text
wordpress/wp-content/plugins/
```

However, if you are not using Docker, it is your responsibility to make sure your local environment uses versions compatible with the project.

In particular, check:

* PHP version
* required PHP extensions
* MySQL/MariaDB compatibility
* Node.js version
* npm/package dependencies
* Composer dependencies

A project working correctly with a different PHP or Node.js version on your machine does **not necessarily mean it will work correctly on staging or production**.

This is one of the main reasons Docker is strongly recommended.

### Do not remove or replace the Docker configuration

Even if you choose not to use Docker locally, **do not remove, replace or ignore the project's Docker configuration**.

The Docker files are part of the project and may be required by:

* other developers
* the TM Studio staging server
* future maintenance
* debugging
* deployment workflows

Changes to Docker configuration should be treated like any other infrastructure change and committed to the repository.

If you believe the Docker configuration needs to be changed, please discuss the change with the team first.

### Dependency versions

Avoid unnecessarily changing dependency versions simply because newer versions are available.

For example, do not automatically change:

```text
PHP 8.3 → PHP 8.4
Node 20 → Node 24
MySQL 8 → another database/version
```

without checking compatibility with:

* WordPress
* the theme
* plugins
* Composer packages
* npm packages
* staging
* production hosting

Where possible, local development, staging and production should use compatible versions of the project's main dependencies.

> **The goal is reproducibility:** another developer should be able to clone the repository, start the documented environment and work with the same core software versions used by the rest of the team.


------------------------------------------------------------------------

## 4. Project Documentation

Every repository should contain a useful `README.md`.

At minimum, it should explain:

-   what the project is
-   local development requirements
-   how to install dependencies
-   how to build frontend assets
-   development/watch commands
-   production build command
-   important WordPress/plugin dependencies
-   any unusual project architecture
-   deployment information where appropriate

The aim is simple:

> Another developer should be able to clone the repository and
> understand how to get started without reverse-engineering the project.

------------------------------------------------------------------------

## 5. WordPress Standards

Use WordPress's standard APIs and functions wherever possible.

Do not manually recreate functionality that WordPress already provides
unless there is a clear technical reason.

This applies particularly to:

-   images and media
-   menus
-   URLs
-   permalinks
-   post queries
-   metadata
-   excerpts
-   pagination
-   navigation
-   users
-   forms and actions
-   scripts and styles

### Do not modify WordPress core

Never edit files inside WordPress core.

### Do not modify third-party plugins

Never edit a third-party plugin directly.

Updates will overwrite those changes.

Use hooks, filters, a custom plugin or the theme/child theme instead.

### URLs

Do not hard-code site URLs into templates.

Use appropriate WordPress functions such as:

``` php
home_url()
site_url()
get_permalink()
get_template_directory_uri()
get_stylesheet_directory_uri()
```

### Scripts and styles

Use the WordPress enqueue system:

``` php
wp_enqueue_style()
wp_enqueue_script()
```

Do not manually add application scripts and styles to `<head>` unless
there is a specific reason.

------------------------------------------------------------------------

## 6. Gutenberg and Editable Content

**Gutenberg should be the default approach for editable page content.**

New page components should normally be implemented as reusable Gutenberg
blocks or appropriate native WordPress blocks.

The CMS should allow editors to update content without editing HTML or
source code.

### Blocks should be reusable

Avoid hard-coding a component for one particular page when it could
reasonably be a reusable block.

At the same time, avoid creating many almost-identical blocks when one
configurable block can handle the variations cleanly.

### When Gutenberg is not appropriate

There may be situations where Gutenberg is not the best solution.

That is fine, but if you choose another approach:

1.  explain why Gutenberg is unsuitable;
2.  discuss/document the alternative;
3.  make sure the content remains maintainable for editors where
    appropriate.

Do not bypass Gutenberg simply because writing a hard-coded template is
quicker.

------------------------------------------------------------------------

## 7. ACF

When using **Advanced Custom Fields (ACF)**, field definitions must be
version-controlled.

### ACF JSON

Where fields are managed through the WordPress admin, use an `acf-json`
directory and commit it to Git.

This ensures that field group changes are stored alongside the code.

Example:

``` text
theme/
├── acf-json/
│   ├── group_xxxxxxxxxxxxx.json
│   └── group_yyyyyyyyyyyyy.json
```

Do not allow important ACF configuration to exist **only in the
production database**.

### Programmatically registered fields

If ACF fields are registered programmatically, keep the PHP definitions
in the repository instead.

They can be organised in `functions.php` or, preferably for larger
projects, in appropriately structured include files/classes.

------------------------------------------------------------------------

## 8. HTML and Semantic Structure

Keep HTML semantic and valid.

### Headings

Normally there should be **one `<h1>` per page**, representing the main
page heading.

Use a logical hierarchy after that:

``` text
H1
├── H2
│   ├── H3
│   └── H3
└── H2
    └── H3
```

Do not choose heading elements because of their visual appearance.

For example, do not use an `<h3>` simply because its default size
matches the design. Use the semantically correct heading and control its
appearance with CSS.

### Semantic elements

Use appropriate HTML elements where possible:

``` html
<header>
<nav>
<main>
<section>
<article>
<aside>
<footer>
```

Avoid unnecessary wrapper `<div>` elements.

### Links and buttons

Use:

-   `<a>` for navigation
-   `<button>` for actions

Do not create clickable `<div>` or `<span>` elements when a native
interactive element is appropriate.

------------------------------------------------------------------------

## 9. Images and Media

Use standard WordPress image functions rather than manually constructing
image markup.

For example:

``` php
wp_get_attachment_image()
get_the_post_thumbnail()
the_post_thumbnail()
```

This helps WordPress manage:

-   alternative text
-   responsive image sources
-   `srcset`
-   image dimensions
-   registered image sizes
-   lazy loading
-   escaping and markup consistency

### Alternative text

Images must have appropriate `alt` attributes.

For content images, the alt text should normally be managed through the
WordPress Media Library.

Decorative images should generally use:

``` html
alt=""
```

Do not manually hard-code alt text in a template when it should come
from WordPress.

### Responsive images

Do not bypass WordPress's responsive image functionality unnecessarily.

Avoid manually creating:

``` html
<img src="...">
```

when a WordPress attachment/image function can generate the correct
markup.

### Image sizes

Do not load the original full-resolution image when a smaller registered
WordPress image size is appropriate.

------------------------------------------------------------------------

## 10. Accessibility

Accessibility should be considered during development, not added
afterwards.

At minimum:

-   interactive elements must be keyboard accessible
-   forms must have proper labels
-   links and buttons must use the correct semantic elements
-   images must have appropriate alt attributes
-   focus states must remain visible
-   navigation must be usable with a keyboard
-   accordions/modals/menus should have appropriate state information
-   colour alone should not be used to communicate important meaning

### Focus styles

Do not remove browser focus outlines unless an accessible replacement is
provided.

Avoid:

``` css
outline: none;
```

without an appropriate alternative focus style.

### ARIA

Use ARIA when necessary, but prefer native semantic HTML whenever it
already provides the required behaviour.

Do not add ARIA attributes simply for the sake of adding them.

------------------------------------------------------------------------

## 11. CSS

Keep all CSS source files in Git.

If production CSS is generated from SCSS, SASS, PostCSS, Tailwind or
another tool, the original source files and build configuration must be
committed.

### General rules

-   avoid excessive inline CSS
-   avoid unnecessary `!important`
-   follow the existing project's CSS architecture
-   keep selectors maintainable
-   avoid unnecessarily specific selectors
-   don't introduce a completely different naming methodology halfway
    through a project
-   use responsive layouts rather than relying on arbitrary fixed
    dimensions

### Responsive design

Do not test only the exact desktop and mobile sizes shown in Figma.

Layouts must also work at widths **between** the supplied designs.

Avoid solutions that work at one exact viewport width but break
immediately above or below it.

------------------------------------------------------------------------

## 12. JavaScript

Keep all JavaScript source files in Git.

### General rules

-   use native browser functionality where appropriate
-   don't use JavaScript for something that CSS or semantic HTML can
    handle reliably
-   avoid unnecessary libraries
-   avoid polluting the global scope
-   scripts should fail gracefully when their target element does not
    exist
-   avoid unnecessary DOM queries and event listeners
-   keep components/modules understandable and maintainable

Do not assume that a particular component exists on every page.

For example, initialise it conditionally rather than allowing the script
to throw an error.

### Dependencies

If npm dependencies are required, declare them in `package.json`.

Commit the appropriate lock file, for example:

``` text
package-lock.json
yarn.lock
pnpm-lock.yaml
```

Do not rely on packages that exist only on one developer's computer.

------------------------------------------------------------------------

## 13. Security

Never commit:

-   passwords
-   API keys
-   access tokens
-   database credentials
-   private keys
-   production secrets

### Input and output

Do not trust user-controlled input.

Sanitise data when receiving/storing it and escape data when outputting
it.

Common WordPress functions include:

``` php
sanitize_text_field()
sanitize_email()
esc_html()
esc_attr()
esc_url()
wp_kses_post()
```

Use the appropriate function for the context.

### Permissions

For privileged actions:

-   check user capabilities
-   use WordPress nonces where appropriate
-   validate requests server-side

Do not rely only on frontend restrictions.

------------------------------------------------------------------------

## 14. Performance

Performance should be considered while building components.

### Assets

Do not load large CSS or JavaScript libraries globally when they are
needed only on one component/page.

Where practical, load assets only where they are required.

### Images

Use appropriately sized images and WordPress responsive image
functionality.

Avoid loading unnecessarily large source images.

### Database queries

Avoid unnecessary database queries, especially inside loops.

If a value can be retrieved once and reused, do that rather than
repeating the same expensive operation.

### Plugins

Do not install a plugin for every small piece of functionality.

Before adding a plugin, consider:

-   whether WordPress already provides the functionality
-   whether the existing codebase already provides it
-   whether a small amount of maintainable custom code would be more
    appropriate
-   whether the plugin is actively maintained
-   whether it introduces significant frontend assets or other overhead

------------------------------------------------------------------------

## 15. Plugins and Third-Party Dependencies

Before adding a plugin or library, consider its long-term maintenance.

Avoid dependencies that are:

-   abandoned
-   poorly maintained
-   unnecessarily large
-   duplicating existing functionality
-   incompatible with the project's supported PHP/WordPress versions

Important dependencies should be documented in the project README.

Never edit a plugin's source code directly to customise its behaviour.

------------------------------------------------------------------------

## 16. Browser and Responsive Testing

Before considering frontend work complete, test it across relevant
browsers and viewport sizes.

At minimum, check current versions of:

-   Chrome
-   Safari
-   Firefox

Where the project requires it, also test Edge and relevant mobile
browsers/devices.

### Responsive checks

Test:

-   desktop
-   laptop
-   tablet
-   mobile
-   intermediate viewport widths

Check particularly:

-   navigation
-   forms
-   carousels/sliders
-   accordions
-   modals
-   hover interactions
-   touch interactions
-   typography
-   image cropping
-   long content
-   unusually short content

There should be no accidental horizontal page scrolling.

------------------------------------------------------------------------

## 17. Forms

All forms should:

-   use proper `<label>` elements
-   clearly identify required fields
-   display useful validation messages
-   remain usable with a keyboard
-   handle errors gracefully
-   provide clear success feedback
-   validate important data server-side

Do not rely exclusively on client-side JavaScript validation.

When a form sends email, test actual delivery rather than assuming a
successful frontend submission means the email arrived.

------------------------------------------------------------------------

## 18. Code Quality and Maintainability

Write code for the next developer, not only for the current task.

Prefer code that is:

-   clear
-   predictable
-   reusable where appropriate
-   easy to debug
-   consistent with the existing project

Avoid clever or unnecessarily complicated solutions when a simpler
implementation will work.

### Comments

Use comments to explain **why** something unusual is being done, rather
than describing obvious code line-by-line.

If a workaround exists because of a browser issue, plugin limitation or
unusual client requirement, document it.

------------------------------------------------------------------------

## 19. Deployment

A deployment should come from version-controlled code.

The normal flow should be:

``` text
Local development
       ↓
Git repository
       ↓
Staging
       ↓
Production
```

Production should **not** become a separate source of code.

### Before deployment

Make sure:

-   changes are committed
-   the correct branch is being deployed
-   production assets have been built
-   there are no accidental debug files
-   no credentials have been committed
-   the site has been tested on staging where appropriate

### After deployment

Check:

-   the affected pages
-   browser console
-   forms
-   navigation
-   responsive behaviour
-   PHP/application error logs where available

------------------------------------------------------------------------

## 20. Pre-Delivery Checklist

Before marking a development task or project as complete, check the
following.

### Repository

-   [ ] Latest code is committed.
-   [ ] All source SCSS/SASS/PCSS files are included.
-   [ ] All JavaScript/TypeScript source files are included.
-   [ ] Build configuration is included.
-   [ ] `package.json` / `composer.json` are included where applicable.
-   [ ] Lock files are committed.
-   [ ] ACF configuration is version-controlled.
-   [ ] No credentials or secrets are committed.
-   [ ] README documentation is up to date.

### WordPress

-   [ ] Standard WordPress APIs/functions are being used where
    appropriate.
-   [ ] No WordPress core files have been modified.
-   [ ] No third-party plugin files have been modified.
-   [ ] Editable content can be managed appropriately through WordPress.
-   [ ] Gutenberg has been used for editable page components unless
    there is a documented reason not to.

### HTML / SEO / Accessibility

-   [ ] Page has an appropriate `<h1>`.
-   [ ] Heading hierarchy is logical.
-   [ ] Images have appropriate alt attributes.
-   [ ] Semantic HTML is used where appropriate.
-   [ ] Links and buttons use appropriate elements.
-   [ ] Keyboard navigation works.
-   [ ] Focus states are visible.
-   [ ] Forms have labels.

### Frontend

-   [ ] Desktop layout tested.
-   [ ] Tablet layout tested.
-   [ ] Mobile layout tested.
-   [ ] Intermediate viewport widths tested.
-   [ ] Chrome tested.
-   [ ] Safari tested.
-   [ ] Firefox tested.
-   [ ] No accidental horizontal scrolling.
-   [ ] Browser console contains no relevant JavaScript errors.

### Functionality

-   [ ] Navigation tested.
-   [ ] Forms tested.
-   [ ] Interactive components tested.
-   [ ] Links tested.
-   [ ] Error states tested where appropriate.
-   [ ] PHP/application logs checked where appropriate.

### Deployment

-   [ ] Production matches the repository.
-   [ ] No production-only changes exist.
-   [ ] Production build has been generated correctly.
-   [ ] A quick post-deployment test has been completed.

------------------------------------------------------------------------

## 21. When Unsure

If you are unsure about how something should be implemented, ask before
creating a completely custom solution.

In particular, check first when you are considering:

-   bypassing Gutenberg
-   manually recreating WordPress image markup
-   introducing a new frontend framework
-   installing a major plugin
-   changing the project's build system
-   creating a custom replacement for standard WordPress functionality
-   making a direct production change
-   introducing a new external service or dependency

A short discussion before implementation is much easier than replacing
an unsuitable approach afterwards.

------------------------------------------------------------------------

## Summary

The overall aim is not to enforce one particular coding style for every
situation.

It is to make sure that TM WordPress projects are:

-   properly version-controlled
-   maintainable
-   accessible
-   secure
-   performant
-   editable through WordPress
-   built using WordPress conventions
-   documented well enough for another developer to continue the work

Above all:

> **Git is the source of truth. Keep the complete source code in the
> repository, use WordPress's standard functionality wherever possible,
> and build projects so that another developer can confidently pick them
> up in the future.**
