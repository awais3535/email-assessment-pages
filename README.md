# Email Automation — Test Contact Directory

A small, public, static contact directory for the **Automation Engineer Assessment — n8n Automated Email Follow-Up System**. Six individual pages provide controlled email test data directly in their initial HTML responses.

The assessment demonstrates fetching HTML, extracting and validating email addresses, deduplicating results, and retaining source URLs before an authorized follow-up step. This repository contains the website and usage documentation; it does not implement the n8n workflow.

## Website structure

```text
email-assessment-pages/
├── index.html
├── contact-1.html
├── contact-2.html
├── contact-3.html
├── contact-4.html
├── contact-5.html
├── contact-6.html
├── styles.css
└── README.md
```

The homepage links to the six contacts without listing their email addresses. Each individual page contains one unique designated address, repeated as readable link text and as its `mailto:` target. All internal navigation and stylesheet paths are relative.

No JavaScript, external assets, frameworks, packages, authentication, or build step is required.

## Test contact pages

Expected homepage after GitHub Pages is enabled: <https://awais3535.github.io/email-assessment-pages/>

| Contact | Public URL after deployment | Exact test address |
| --- | --- | --- |
| 01 | <https://awais3535.github.io/email-assessment-pages/contact-1.html> | `awais.ur.rehman+1@66loop.com` |
| 02 | <https://awais3535.github.io/email-assessment-pages/contact-2.html> | `awais.ur.rehman+2@66loop.com` |
| 03 | <https://awais3535.github.io/email-assessment-pages/contact-3.html> | `awais.ur.rehman+3@66loop.com` |
| 04 | <https://awais3535.github.io/email-assessment-pages/contact-4.html> | `awais.ur.rehman+4@66loop.com` |
| 05 | <https://awais3535.github.io/email-assessment-pages/contact-5.html> | `awais.ur.rehman+5@66loop.com` |
| 06 | <https://awais3535.github.io/email-assessment-pages/contact-6.html> | `awais.ur.rehman+6@66loop.com` |

These URLs are deployment targets, not confirmation that the site has already been published.

## Preview locally

Open `index.html` directly in a browser, or, if Python 3 is installed, serve the repository:

```sh
python3 -m http.server 8000
```

Visit <http://localhost:8000/>. Inspect desktop and mobile layouts, use Tab to navigate the links, open each contact, and return to the directory. Selecting the email address opens the configured email application; it does not automatically send a message.

## Deploy with GitHub Pages

1. Review the files and validation results, then commit and push them to `main` using the commands below.
2. Open the `awais3535/email-assessment-pages` repository on GitHub.
3. In **Settings → Pages**, select **Deploy from a branch** as the source.
4. Select branch **main**, folder **/ (root)**, and save.
5. Wait for GitHub's deployment to complete and open the published site URL.

Relative links support the `/email-assessment-pages/` repository path. If the repository or account name changes, update the documented absolute URLs and the n8n input URLs. The HTML links need no change.

## Verify public URLs

After deployment, confirm that the homepage, all six contact pages, and `styles.css` load without authentication. Open each directory card and use **Back to Directory** to return. Check raw HTML using browser View Source or:

```sh
curl --fail --show-error --location https://awais3535.github.io/email-assessment-pages/contact-1.html
```

Repeat for contacts 2–6. Verify successful responses contain the expected visible address and matching `mailto:` link, with no other email addresses. Confirm dots, plus signs, numbers, and the domain remain unchanged. A browser-rendered address alone is insufficient: it must also appear literally in the HTTP response.

Public accessibility must be checked after publishing; local validation cannot establish deployment status.

## Use with n8n

1. Prepare six input items, each containing a `sourceUrl` field with one of the individual contact URLs above.
2. Configure an **HTTP Request** node to perform a `GET` against each item's `sourceUrl`, with no authentication, and return the response as text. Use `{{$json.sourceUrl}}` as the URL expression when that field is on the current item.
3. Keep the original `sourceUrl` associated with its HTML response through the workflow. Do not rely on the response body containing its own public URL. Use item linking or a merge step if the request node replaces the input fields.
4. Extract email matches from the returned HTML, validate them, and deduplicate before any follow-up action.
5. Output each unique `email` together with its associated `sourceUrls` for auditing.

### Extraction, validation, and deduplication

A practical extraction pattern for this controlled fixture is:

```regex
[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}
```

This is a fixture-oriented pattern, not a complete implementation of every valid email syntax. Apply a full-match format check to extracted candidates. Syntax validation does not establish mailbox existence, deliverability, ownership, or sending permission.

Each page's address appears twice in raw HTML: once in the `mailto:` attribute and once as visible text. An extraction over the entire HTML will therefore find two matches for the same address. Collapse exact repeated addresses, and group their unique source URLs. Across all six pages, the expected result is **six unique email addresses**, each associated with its corresponding contact URL.

Preserve the complete address. In particular, do not remove plus tags, strip dots, or decode the plus sign as a space. The six tagged addresses are separate test values even if the mailbox provider routes them to one inbox. Repeated fetching of the same page should not create duplicate addresses or duplicate source URL entries.

Handle failed HTTP responses explicitly: report the affected source URL and do not treat an error page as a successful contact fetch. Retry or flag it for review before continuing that item.

## Security and sending authorization

These addresses must be controlled test recipients, not unrelated contacts. Confirm you control the listed recipients and are authorized to send assessment messages before enabling any email-sending step. The directory is public: its contents are visible to anyone, including crawlers. Never add credentials, tokens, or private contact data.

## Commit and push manually

Run from the repository root after reviewing the files:

```sh
git add index.html contact-1.html contact-2.html contact-3.html contact-4.html contact-5.html contact-6.html styles.css README.md
git commit -m "Add static email assessment contact directory"
git push origin main
```

Creating these files does not commit, push, enable GitHub Pages, or publish the site automatically.
