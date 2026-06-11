# James Scott website

This is a simplified Quarto rebuild of the personal website for James Scott.

## Local preview

Install Quarto, then run:

```bash
quarto preview
```

## Render

```bash
quarto render
```

The rendered site is written to `docs/`, which can be served by GitHub Pages.

## GitHub Pages deployment

Two common options:

1. Render locally with `quarto render`, commit the `docs/` directory, and configure GitHub Pages to serve from the `docs/` folder on the main branch.
2. Use the included GitHub Actions workflow to render and deploy automatically.

## Content files

- `index.qmd`: homepage
- `research.qmd`: research overview and students
- `books.qmd`: books page
- `teaching.qmd`: teaching and public course materials
- `software.qmd`: selected software projects
- `consulting.qmd`: consulting and litigation shell
- `styles.css`: custom styling
- `assets/img/`: images
- `assets/pdf/`: CV and public PDFs

## Notes before publishing

- Replace any bracketed placeholder language on `consulting.qmd` with verified final wording.
- Confirm the department name and titles are current.
- Confirm that all PDFs in `assets/pdf/` are intended to be public.
- If using a custom domain, add a `CNAME` file at the repository root.
