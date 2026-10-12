# tranhan1405.github.io

Personal academic website of Tran-Van Han, built with the [al-folio](https://github.com/alshedivat/al-folio) Jekyll theme (MIT license).

## Where things are

| Content | File |
| --- | --- |
| Bio, photo, address | `_pages/about.md`, `assets/img/prof_pic.png` |
| Publications | `_bibliography/papers.bib` (PDFs in `assets/pdf/`; `selected={true}` puts a paper on the home page) |
| News | `_news/*.md` |
| CV | `_data/cv.yml` |
| Teaching | `_pages/teaching.md` |
| Social links | `_data/socials.yml` |

## Deployment

Pushing to `master` runs `.github/workflows/deploy.yml`, which builds the site and publishes it to the `gh-pages` branch. GitHub Pages must serve from `gh-pages` (Settings → Pages → Build and deployment → Deploy from a branch → `gh-pages`).
