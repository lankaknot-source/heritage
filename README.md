# HERITAGE ASIA HOLDINGS — v1.2

Responsive HTML/Tailwind/JavaScript land-sales manager using Firebase Authentication and Firestore only. The interface preserves phone notch/status-bar and bottom gesture safe areas.

## Setup

1. In Firebase Console, enable Authentication → Email/Password.
2. Create the first admin Authentication account.
3. In Firestore create `users/{AUTH_UID}` with: `name`, `email`, `role: "admin"`, `active: true`.
4. Publish `firestore.rules` from this package.
5. Serve this folder over HTTPS or localhost. Do not open `index.html` directly with `file://`.

Roles: admins and managers can delete; officers can add/edit; viewers are read-only. A user profile delete does not delete the Firebase Authentication account—remove that separately in Firebase Console. The app prevents users from deleting their own profile through the rules.

All delete actions show a clear permanent-delete confirmation and write an audit entry. Sales, payment and document deletion should only be used in line with the company's accounting and document-retention policy.

## Price calculation

Block and sale forms include **Calculate Total**. The saved total is always recomputed as `perches × pricePerPerch`, so an altered or stale displayed total is never trusted. Decimal perches are supported.

## EmailJS PDF auto-delivery

1. Create an EmailJS account and connect the company mailbox under **Email Services**. Copy the Service ID.
2. Create an Email Template and copy its Template ID.
3. Set **To Email** to `{{to_email}}`.
4. Suggested subject: `HERITAGE ASIA HOLDINGS - {{document_type}} - {{document_no}}`.
5. Add a **Variable Attachment**. Set the parameter name to `pdf_attachment` and filename to `{{document_no}}.pdf`.
6. Copy the EmailJS Public Key from Account/API Keys.
7. In the app open **Settings** and save Service ID, Template ID and Public Key.

Available template parameters: `to_email`, `customer_name`, `project_name`, `block_no`, `total_price`, `payment_amount`, `balance`, `document_type`, `document_no`, and `pdf_attachment`.

When a sale/reservation is saved with status **Confirmed**, the PDF is generated, stored as Base64 in the Firestore `documents` record, and emailed automatically. A failed email remains visible as `emailStatus: Failed`; the sale itself is not lost. EmailJS browser keys are public identifiers, not secrets, but restrict the EmailJS service/template to approved origins and monitor quota.

## Firestore collections

`projects`, `blocks`, `customers`, `sales`, `payments`, `documents`, `users`, `auditLogs`.

Firestore has a 1 MiB document limit. Keep generated PDFs/photos aggressively compressed; for larger production documents, move binary files to dedicated object storage while retaining metadata in Firestore.
