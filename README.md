# tool-sites

Source code for three tool sites, each in its own folder. All three are live
on Cloudflare Workers; the pages here are the site content itself.

| Folder | Site | Live URL |
| --- | --- | --- |
| `ideogram45/` | Ideogram 4.5 Prompt Builder | https://ideogram45.qqbridge.workers.dev/ |
| `flux3klein/` | FLUX 3 Klein Prompt Builder | https://flux3klein.qqbridge.workers.dev/ |
| `clipprompt/` | ClipPrompt (local video prompt builder) | https://clipprompt.kill-bill.de5.net/ |

Notes:

- Deployment scripts are intentionally not included: they contain
  account-specific Cloudflare configuration. The pages in each folder are
  static HTML and can be served as-is.
- The PNG favicons used by the live sites are not included here because
  this upload route only accepts text files; the SVG favicons are included.
  The live sites are unaffected.
