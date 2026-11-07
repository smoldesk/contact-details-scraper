# Contact Details Scraper

![Contact Details Scraper](https://raw.githubusercontent.com/smoldesk/apify-scrapers/main/assets/contact-details/banner.webp)

Get emails, phone numbers, social profiles and contact forms from any list of websites. Reads the homepage plus the contact, about, team and legal pages, checks every email's domain for a mail server, and drops junk (noreply, placeholders, tracking keys, website-builder social accounts). Runs on [Apify](https://apify.com/smoldesk/contact-details-scraper); you pay only for websites where contacts are found.

## What you get

- **Emails**: all emails on the site, the best one as `primaryEmail`, each with the page it was found on, whether it's on the site's own domain and whether its domain accepts mail (DNS MX check).
- **Phones**: from `tel:` links, structured data and page text, in international and E.164 format.
- **Social profiles**: Facebook, Instagram, LinkedIn, X, TikTok, YouTube, Pinterest, WhatsApp and Telegram, one column each plus the full list.
- **Contact form**: the URL of a page with a real contact form.
- **Status for every website**: `contacts found`, `no contacts found`, `protected by a bot check (not scraped)`, `unreachable`, `HTTP 4xx`. Every row has the same fields.
- **Enrich a dataset**: read website URLs from another Apify dataset (for example a [Google Maps Scraper](https://github.com/smoldesk/google-maps-scraper) run) and copy its fields into `sourceData`.

## Run it

Open the [Contact Details Scraper on Apify](https://apify.com/smoldesk/contact-details-scraper), paste websites and click Start. Or call it from code with your [Apify token](https://console.apify.com/settings/integrations):

```bash
curl -X POST "https://api.apify.com/v2/acts/smoldesk~contact-details-scraper/run-sync-get-dataset-items?token=YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"websites": ["barleyswine.com", "roaringfork.com", "oddduckaustin.com"]}'
```

Python (`pip install apify-client`):

```python
from apify_client import ApifyClient

client = ApifyClient("YOUR_TOKEN")
run = client.actor("smoldesk/contact-details-scraper").call(run_input={
    "websites": ["basecamp.com", "buffer.com", "ghost.org"],
    "onlyOwnDomainEmails": True,
    "dropEmailsWithoutMailServer": True,
})
for row in client.dataset(run["defaultDatasetId"]).iterate_items():
    print(row["domain"], row["primaryEmail"], row["linkedin"])
```

Enrich a Google Maps Scraper run (JavaScript, `npm install apify-client`):

```js
import { ApifyClient } from 'apify-client';

const client = new ApifyClient({ token: 'YOUR_TOKEN' });
const run = await client.actor('smoldesk/contact-details-scraper').call({
    datasetId: 'YOUR_GOOGLE_MAPS_RUN_DATASET_ID',
    datasetUrlField: 'website',
});
const { items } = await client.dataset(run.defaultDatasetId).listItems();
console.log(items.map((r) => [r.sourceData?.name, r.primaryEmail, r.phones[0]]));
```

## Sample output

```json
{
  "domain": "fabrikatx.com",
  "name": "Fabrik Austin",
  "status": "contacts found",
  "primaryEmail": "contact@fabrikatx.com",
  "emails": ["contact@fabrikatx.com"],
  "phones": ["+1 512-222-9816"],
  "instagram": "http://instagram.com/fabrikatx",
  "contactFormUrl": "https://www.fabrikatx.com/",
  "emailDetails": [
    {"email": "contact@fabrikatx.com", "ownDomain": true, "foundOn": "https://www.fabrikatx.com/",
     "foundIn": "schema.org", "domainAcceptsMail": true}
  ],
  "pagesChecked": ["https://www.fabrikatx.com/", "https://www.fabrikatx.com/contact", "https://www.fabrikatx.com/about-fabrik"]
}
```

On 690 restaurant websites in Austin, TX, 73% had contacts (52% an email, 61% a social profile) and the run took 6 minutes.

## Pricing

Pay once per website **with contacts found**. Websites with nothing, unreachable or bot-protected websites and duplicates are free. Same price whether 1 or 10 pages are read.

| Per 1,000 websites with contacts | Free | Starter | Scale | Business |
|---|---|---|---|---|
| Website with contacts | $3.00 | $2.50 | $2.00 | $1.50 |

## Limits

- Websites behind a bot check (Cloudflare "Just a moment…", about 8% in our test) are skipped and marked, not scraped.
- No browser: contacts that appear only after JavaScript runs are missed.
- Emails hidden with Cloudflare email obfuscation or shown as images are not read.
- The DNS check confirms the domain accepts mail, not that the mailbox exists.

## More scrapers

Contact Details Scraper is one of 19 no-login scrapers by Smoldesk Studios. See the full list: [smoldesk/apify-scrapers](https://github.com/smoldesk/apify-scrapers).
