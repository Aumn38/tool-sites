# TEST-REPORT — tool-sites fix pass (review follow-up)

Date: 2026-10-31. Parent commits on GitHub main before this fix:
`ffc427d` (local-only rebuild) and `011210a` (voluntary donation entry).
This report covers the fix pass only; it does not re-claim the original
rebuild's test results. The original rebuild report (2026-10-31, Chinese)
is superseded by this file for current status.

## What changed in this pass
- Donation modal: Creem storefront link (`creem.io/ctrl`) removed from all
  three sites. Replaced with Alipay + WeChat personal collection QR codes
  (embedded as data URIs; QR source PNGs are not committed because the
  upload route accepts text only). Modal wording kept: core features are
  permanently free, donations unlock nothing.
- Donation modal: `role="dialog"`, `aria-modal`, focus moves to the close
  button on open and returns to the previously focused element on close;
  Escape still closes.
- JSON import validation (all three sites): rejects files whose `site`
  differs from the current site, whose `version` is not 2, whose `state`
  is not an object, and whose `templates` is not an array; invalid
  template items are skipped and counted instead of breaking the import.
- Template storage reads are now wrapped (`loadTemplates()` returns `[]`
  on corrupt storage) so a damaged localStorage entry cannot throw on
  load/delete/refresh. `applyState` now rejects non-object state.
- README: ClipPrompt line corrected from "Wan 3.0 reverse-prompt tool"
  to "local video prompt builder" (the reverse tool was removed in the
  2026-10-31 rebuild; the old line was stale).

## Checks that passed (local, 2026-10-31)
- `node --check` on every inline `<script>` block in all three pages: OK.
- Import-validation logic exercised in Node in isolation (6 cases):
  valid file passes; wrong-site file rejected; wrong-version file
  rejected; missing state rejected; bad template items filtered
  (1 good kept, 3 invalid skipped); corrupt localStorage returns `[]`.
- Static checks: `creem.io` occurrences = 0 in all three pages;
  QR images = 2 per page; site ids correct per site
  (`ideogram45` / `flux3klein` / `clipprompt`); Google site-verification
  tags unchanged (ideogram 1, flux 1, clipprompt 2).
- QR crops visually verified: finder patterns intact, quiet zone
  preserved, Alipay display name painted out (only the QR itself is
  embedded). No QR decoder was available on this machine, so decoding
  was NOT verified by software.

## NOT verified (stated plainly)
- Real-browser end-to-end test (button clicks, clipboard, localStorage
  across refresh, JSON file import/export in a real browser): NOT done
  (no Chromium/jsdom available to the builder).
- Phone / tablet real-device test: NOT done (CSS media queries only).
- Real payment test: NOT done on purpose. No real money was sent to
  either QR code. The QR images are the author's own collection codes
  supplied by the author; a donor-side scan test must be done by the
  author or a volunteer, not by paying.
- Live Cloudflare verification is recorded separately after deployment
  (see the deployment report in chat); this file's "live" claims must
  not be read as verified until that step completes.

## Superseded text
Earlier versions of this report said the rebuild had "not yet been pushed
to GitHub" and "not yet been deployed to Cloudflare". That was true when
the report was first written and is no longer true: the rebuild and the
donation entry are both on GitHub main and deployed live. Do not quote
those lines as the current status.
