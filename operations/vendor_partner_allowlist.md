# Vendor / Partner Allow-list

> Operator-editable. Add domains under the appropriate section. The IMAP
> classifier loads this on each scan tick (CC1 multi-folder poller). No
> restart needed.

## Sections (semantic categorization)

### vendor.payments
- stripe.com
- mercury.com
- rtsfinancial.com
- otrcapital.com
- apexcapitalcorp.com
- triumphpay.com
- achdirect.com
- chase.com
- bankofamerica.com
- wellsfargo.com

### vendor.infrastructure
- aws.amazon.com
- amazonaws.com
- cloudflare.com
- digitalocean.com
- linode.com
- vercel.com
- googleusercontent.com  # gmail/workspace identity-verified senders
- google.com

### vendor.communications
- twilio.com
- plivo.com
- telnyx.com
- sendgrid.com
- mailgun.com
- postmark.com
- resend.com
- mailchimp.com
- zoho.com
- zohomail.com
- zoho.in

### vendor.ai
- anthropic.com
- openai.com

### vendor.dev_tools
- github.com
- gitlab.com
- mongodb.com  # billing for Atlas
- atlas.mongodb.com
- notion.so
- slack.com
- nssm.cc

### vendor.data
- datfreight.com
- truckstop.com
- 123loadboard.com

### vendor.regulatory
- fmcsa.dot.gov
- dot.gov
- safer.fmcsa.dot.gov
- qcmobile.fmcsa.dot.gov

### partner.factor
(operator: add factor partners' from-domains here)

### partner.shipper
(operator: add shipper from-domains here)

## Format rules

1. Each line under a section header is a domain in lowercase.
2. Comments after `#` are stripped.
3. Subdomains use leading `.` to match all (e.g., `.googleusercontent.com`).
4. Empty lines + `(operator:...)` placeholders are ignored.
5. Section names must match the regex `[a-z_]+\.[a-z_]+` and become the
   classifier's `lane` value when a sender matches.
