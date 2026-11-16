# Vexel Academy deployment

Vexel Academy is Vexel Innovations' branded fork of Frappe Learning. It runs as a separate Frappe application and is intentionally not deployed inside the Vercel marketing website.

## Production topology

| Service | Responsibility | Production address |
| --- | --- | --- |
| Vexel Innovations website | Company website, CRM entry points, public platform pages | `www.vexelinnovations.com` |
| Vexel Academy | Courses, batches, lessons, assessments, attendance, and LMS certificates | `academy.vexelinnovations.com` |
| Vexel CRM | Enquiries, field operations, business records, and Vexel integration records | Private Vercel/Supabase application |

Frappe uses MariaDB and Redis. Do not connect this app directly to the website's Supabase database. Use authenticated API/webhook integrations for enrolments, payment confirmations, staff provisioning, and certificate references.

## Before production

1. Provision a Linux VPS or a managed Frappe hosting account. The server needs a persistent volume, a current Docker/Bench-compatible deployment, MariaDB, Redis, HTTPS, and automated backups.
2. Create the DNS record `academy.vexelinnovations.com` and point it to the LMS host. Keep `vexelinnovations.com` on Vercel.
3. Use a strong unique MariaDB root password and a strong Administrator password. Never use the development values in `docker/docker-compose.yml` or `docker/init.sh` in production.
4. Configure an outbound SMTP account using a Vexel-controlled address before inviting learners. Do not rely on a personal mailbox for transactional mail.
5. Configure Paystack through the LMS payment settings or the Vexel integration service. Validate webhooks by signature before granting enrolment.
6. Set the Frappe Website Settings application name to `Vexel Academy`, upload the Vexel logo/favicon, and set the support address to `vexelvision@gmail.com` until a domain mailbox is available.
7. Configure daily database backups, test a restore, apply security updates, and restrict direct database access to the LMS host.
8. Create a staging instance such as `academy-staging.vexelinnovations.com`. Test migrations, payment webhooks, certificate issuance, and integrations there before production.

## Vexel integration contract

The website/CRM must never write into Frappe's MariaDB tables directly. Use a dedicated integration user and API endpoints.

### CRM to LMS

- Create or update learner accounts after an approved enrolment.
- Add a learner to a course/batch after approved Paystack or manual payment.
- Send staff role changes only after an authorised Vexel administrator approves them.

### LMS to CRM

- Send course completion and certificate issuance events.
- Store the LMS certificate ID and verification URL in Vexel CRM for customer support and verification.
- Send attendance and assessment summaries only where the learner has consented.

### Shared data minimisation

Use email, full name, course/batch identifier, enrolment status, completion status, and certificate reference. Do not copy passwords, access tokens, WireGuard configurations, payment card data, or unrestricted learner documents between systems.

## Licence and attribution

This project is a modified AGPL-3.0 fork of Frappe Learning. Keep `license.txt`, upstream notices, and a link to the complete corresponding source available to network users of the deployed service. Do not describe Vexel Academy as official Frappe software.
