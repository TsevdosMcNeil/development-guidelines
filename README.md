# TM Development Guidelines

This repository contains development guidelines, coding standards and best practices for projects developed and maintained by **TM Studio**.

The aim is to keep our projects consistent, maintainable and easy for different developers to work on over time.

These guidelines should be followed when starting new projects and when making significant changes to existing projects.

## Guidelines

### WordPress

📄 [WordPress Development Guidelines](wordpress/WORDPRESS-DEVELOPMENT-GUIDELINES.md)

Guidelines for developing and maintaining WordPress projects, including:

* Git and repository requirements
* Project structure and source files
* WordPress coding practices
* Gutenberg development
* ACF and `acf-json`
* HTML and semantic structure
* Images and media
* Accessibility
* CSS and JavaScript
* Security
* Performance
* Plugins and dependencies
* Responsive and browser testing
* Deployment
* Pre-delivery checks

## Repository Structure

Documentation is organised by platform or development area:

```text
development-guidelines/
├── README.md
├── wordpress/
│   └── development-guidelines.md
├── shopify/
├── statamic/
├── frontend/
└── general/
```

Additional guidelines will be added as required.

## Using These Guidelines

Developers should review the relevant guidelines before starting work on a project.

Individual project repositories may contain additional instructions in their own `README.md`. Where project-specific requirements exist, these should be followed alongside the general guidelines in this repository.

When starting a new project, its repository should also link back to the relevant documentation here.

## Keeping the Guidelines Updated

These documents are intended to evolve as our development practices, tools and platforms change.

If you identify:

* an outdated recommendation
* something that needs clarification
* a recurring development issue that should be documented
* a useful standard that should apply across projects

please raise it with the team so the relevant guideline can be reviewed and updated.

## Questions

If you're unsure how a guideline applies to a particular project or believe a different technical approach would be more appropriate, please discuss it with the team before implementation.

The aim of these guidelines is not to prevent developers from making technical decisions, but to make sure those decisions are **consistent, maintainable and properly documented**.
