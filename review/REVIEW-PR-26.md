# Review: flipbox PR #26 - Replace the deprecated DOMNodeInserted listener

PR: https://github.com/EssentialBlocks/flipbox/pull/26
Verdict: **Approve** (small rebase onto dev `551005c` suggested)

## What the PR does
`dev` had already replaced `DOMNodeInserted` with a `MutationObserver` on `document.body`. This PR replaces that with one capture-phase `change` listener on `document` that checks `event.target.id === 'eb-animation-style'`, which is simpler and has no observer running on every DOM change.


## Real merge impact
The PR page can show up to 19 changed files, because the branch was cut from other work that was later merged. A simulated merge into `dev` (`git merge-tree`) changes **1 file, +7/-22** (`assets/js/eb-animation-load.js`). No conflicts.

## Checks
- `node --check` (and `php -l` where PHP changed): no syntax errors.
- Browser test, Local WordPress 7.1.3, Chrome 153, only this plugin active. The PR's JS file was served in place of the plugin's file for the "PR merged" column; for accordion-toggle the PHP was also taken from the merge.

| Check | `dev` | PR merged |
|---|---|---|
| `DOMNodeInserted` listeners registered in the editor | 0 (already removed) | 0 |
| Injected `eb___animated eb___fadeIn` after select `change` | becomes `eb__animated eb__fadeIn` | becomes `eb__animated eb__fadeIn` |
| Console errors / page errors | none | none |

The "select change" check injects a block element and a `#eb-animation-style` select after page load, then fires `change` (a synthetic select, not the real Inspector UI).

## Notes
- Unlike the other plugins, `dev` here already works; this PR is a simplification, not a bug fix. It is still worth merging: it removes a `MutationObserver` that watches the whole `document.body` subtree, and its `observe(document.body, ...)` call has the same null-body hazard as the one fixed in Typing Text PR #39 (not seen as an error here, because the script was not loaded in the canvas iframe).
- Rebase onto current `dev` (`551005c`) before merging.

## Not covered
- Chrome 153 only (no Firefox/Safari), PHP 8.2 only (the plugin supports 7.4), WordPress 7.1.3 on Local.
- The drag chip (third place the icon CSS targets) was not tested.
- The author had not tested this plugin in a browser before opening the PR; these are the first browser measurements.
