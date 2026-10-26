# ErrantAnt.Website

The website of Errant Ant at errantant.com: one page in the style of the app's start page, with a Coming soon button.
It is an Astro static site; its top bar, tiles, motion and backdrop come from the shared `vui-web` package
(`/home/yuki/project/VUI.Web`), pinned in `packages/`. The app screenshots shown in the first group are WebP files in
`public/screenshots/`.

## Local development

```bash
npm install
npm run dev
```

Build the production site into `dist/` and serve it locally with:

```bash
npm run build
npm run preview
```

## Updating VUI.Web

Pack the new version in the VUI.Web repository, copy the tarball into `packages/`, then install it by its file name:

```bash
npm install vui-web@file:packages/vui-web-0.3.0.tgz
```

Remove the previous tarball once nothing depends on it.

## GitHub Pages

The workflow in `.github/workflows/deploy.yml` builds and deploys each push to `main`. In the repository settings,
select **GitHub Actions** as the Pages source and set `errantant.com` as the custom domain; the build already contains
`public/CNAME` for that domain. DNS records for errantant.com still need to point to GitHub Pages at the domain
provider.
