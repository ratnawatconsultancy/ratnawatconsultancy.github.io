# Ratnawat Consultancy website: rules for working on this repo

One-page website for **Ratnawat Consultancy**, an insurance agency run by Naresh Shantilal Jain at
Shop No. 4, Dipankar Society, Prabhadevi West, Mumbai. It is served by GitHub Pages straight from
the `main` branch, from the repository root. Pushing to `main` publishes the site in about a minute.

The live address is whatever `<link rel="canonical">` in `index.html` says. The owner's Google Business Profile
links to that address, so tell the owner before changing it.

## Files

- `index.html`: the whole site. CSS and JavaScript are inline; the Mahavir image in the header is an
  embedded base64 WebP. The favicon is inline too. Keep it a single self-contained file. The signboard and office
  photos are embedded base64 WebP too. Crop out client papers, number plates and other people's phone numbers.
- `robots.txt` and `sitemap.xml`: for search engines. They must use the live address.
- `google1dacfeeb73359b0b.html`: proves ownership to Google Search Console. Keep it, or the site loses verification.
- `.nojekyll`: stops GitHub from running Jekyll. Keep it.
- `README.md`: one-line description.

## English and Hindi

Text in the HTML is English. Each element's Hindi is in `TEXT.hi` in the script, under the element's `data-i18n`
key, and text set from JavaScript is in `TEXT.en` and `TEXT.hi`. Any visible text you add needs both. Keep
`value` attributes on `<option>` in English, because the value is what reaches the agency's sheet. Test forms in
both languages.

## Hard rules

1. **No leaks.** Never commit client names, policy numbers, any phone number other than the agency's
   (98203 86440 / 919820386440), personal email addresses, links to Google Sheets, Drive or the Apps Script
   editor, API keys or tokens. Commit with GitHub's no-reply email, never a personal one. Any example on the
   page must be clearly labelled "Example" with no real person's details.
2. **Don't change the form contract.** The forms post to `CONFIG.FORM_ENDPOINT` (a Google Apps Script web app
   that writes to the agency's private sheet). Keep that URL as it is. Requests are `POST` with a `text/plain`
   body of JSON:
   - Quote form: `{action: "enquiry", name, mobile, interest, message, whatsappOk, company, source: "Website"}`
   - Renewal form: the same `enquiry` fields with `interest: "Renewal"`. The policy number and insurance company
     go in `message`. Never send the insurance company as `company`: see rule 3.
   - Documents form: same fields with `action: "documents"` plus `files: [{name, type, data}]`
     (`data` is base64 with no `data:` prefix). At most 4 files of 4 MB each; PDF, JPEG, PNG, WebP, HEIC/HEIF.
   - An optional `email` field is also accepted if an email input is ever added.
   - Replies are `{ok: true}` or `{ok: false, errors: [...]}` with keys `name`, `mobile`, `email`, `too_many`,
     `server`, `file_type`, `file_size`, `files`. Show a plain message for each, and keep the WhatsApp fallback
     when the request fails or the endpoint is empty.
3. **Keep the spam trap.** The hidden `company` input must stay hidden from people (not just visually: keep
   `aria-hidden`, `tabindex="-1"`, `autocomplete="off"`) and must be sent empty. The backend silently drops any
   request where it is filled.
4. **Only state facts the owner gave.** Agency name, address, phone, WhatsApp, insurers (New India Assurance,
   LIC, Star Health, Care Health), "every kind of insurance", in business since 2003, serving Prabhadevi, Dadar and Worli. Hours are Monday to Friday
   11 am to 10 pm, Saturday and Sunday 11 am to 8 pm, in both the visible text and the JSON-LD
   `openingHoursSpecification`. Do not invent awards, client counts, ratings, review counts or testimonials.
5. **Insurance advertising care.** No guarantees ("guaranteed claim", "100% settlement"), no "best" or "No. 1"
   claims, no premium figures presented as offers. Keep the line that claim decisions are made by the insurer.
6. **Search details must match the page.** The JSON-LD (`InsuranceAgency`), canonical link and `og:url` must use
   the live address, and the JSON-LD must stay valid JSON.

## Quality bar

- Works from 320 px wide upward with no sideways scrolling; test at 375 px and 1280 px.
- Light and dark mode both readable: text contrast at least 4.5:1.
- Tap targets at least 44 px (40 px for small inline buttons); every input has a visible label.
- Respect `prefers-reduced-motion`.
- The only outside resource is Google Fonts. Add no analytics, trackers, chat widgets or other scripts unless
  the owner asks.

## Testing without bothering the owner

A real form submission creates a lead in the owner's sheet and emails him. Don't send real test enquiries to the
live endpoint. Instead, intercept `fetch` in a headless browser (Playwright is fine) and check the exact payload,
or send a request with `company` filled in, which the backend accepts and discards. Before pushing, check that
the JSON-LD parses and that there are no console errors.
