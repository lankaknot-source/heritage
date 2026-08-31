# HERITAGE ASIA HOLDINGS (PVT) LTD — Land Sales Management System

Black + gold responsive PWA built with HTML, Tailwind CSS, custom CSS, Vanilla JavaScript, Firebase Authentication and Cloud Firestore.

## Included
- Management login with Firebase Email/Password
- Admin / Director / Manager / Site Officer / Sales Officer / Accountant roles
- Land projects and block inventory
- AVAILABLE / HOLD / RESERVED / SOLD live block states
- Customer CRM
- Reservation / sale workflow
- Customer + sales/site officer signatures captured on phone/tablet
- Customer ID image compression
- Project image compression
- Images and signatures stored as compressed Base64 data directly in Firestore (Firebase Storage is not used)
- Payment / installment tracking
- Signed PDF generation in the browser
- PDF stored in Firestore and downloaded immediately
- Optional EmailJS automatic customer email with PDF attachment
- Audit logs
- PWA manifest + service worker for install-like phone/tablet/Windows use

## Firebase project
This build is preconfigured for project: `heritage2-a311b` using the Firebase web configuration supplied by the user.

## Firestore collections
- heritage_users
- heritage_projects
- heritage_blocks
- heritage_customers
- heritage_sales
- heritage_payments
- heritage_documents
- heritage_settings
- heritage_audit_logs

## First Admin Setup
1. Firebase Console -> Authentication -> Sign-in method -> enable Email/Password.
2. Firebase Console -> Authentication -> Users -> create your first admin email/password account.
3. Firestore Rules: temporarily publish `FIRESTORE_RULES_FIRST_ADMIN_SETUP_ONLY.txt`.
4. Open the app and sign in with that Firebase account.
5. The app will show First Admin Setup. Enter a display name and click Create Admin Profile.
6. Immediately replace the temporary rules with `firestore.rules`.

Do not leave the first-admin rules active.

## Firestore-only image storage note
Firestore has a maximum document size of about 1 MiB. This system compresses uploaded images and signatures before saving. The app targets smaller Base64 payloads and blocks oversized generated PDFs. For many high-resolution property photos, Firebase Storage would normally be more appropriate; this build intentionally follows the requirement to use Firestore only.

## EmailJS PDF email setup
In Admin -> Settings add:
- Company email
- EmailJS Service ID
- EmailJS Template ID
- EmailJS Public Key

In the EmailJS template configure:
- To Email: `{{to_email}}`
- Useful variables: `{{customer_name}}`, `{{document_no}}`, `{{project_name}}`, `{{block_no}}`, `{{transaction_type}}`, `{{total_price}}`, `{{paid_total}}`, `{{balance}}`
- Add a Variable Attachment in the template Attachments tab:
  - Parameter name: `pdf_attachment`
  - Filename: `{{pdf_filename}}`
  - Content type: PDF / application/pdf

Without EmailJS settings, the sale still saves, the signed PDF is stored in Firestore, and the PDF downloads locally; email status remains not configured.

## Run
Use a local server rather than opening `index.html` directly.

VS Code: Live Server

or:
```bash
python3 -m http.server 8080
```

Then open `http://localhost:8080`.

## PWA
The project includes `manifest.json` and `sw.js`. HTTPS is required for normal PWA installation outside localhost.

## v1.1 — Mobile safe-area, auto email and block calculations

- Mobile/PWA layout now respects phone status-bar/notch and bottom gesture/navigation safe areas.
- Sticky top bar, modals, toast messages and bottom navigation are kept inside the usable screen area.
- Mobile form controls use touch-friendly sizing and avoid iOS input zoom.
- Block Total Price is now read-only and always recalculates as `Perches × Price / Perch`.
- Selecting a project automatically loads that project's default price per perch and recalculates the block total.
- Sale/Reservation Total Price is pulled from the selected block and is read-only to prevent accidental price mismatches.
- Customer Email is a required, highlighted PDF auto-delivery field.
- Sale confirmation requires EmailJS configuration so the signed PDF can be sent automatically.
- Generated signed PDFs now include block extent and price-per-perch details.
- Documents show email delivery status and offer **Retry Email** when a delivery fails.

### EmailJS attachment requirement

The EmailJS template must accept these variables from the app:

- `to_email`
- `customer_name`
- `document_no`
- `project_name`
- `block_no`
- `transaction_type`
- `total_price`
- `paid_total`
- `balance`
- `company_name`
- `company_email`
- `pdf_attachment` (base64/data URL attachment content)
- `pdf_filename`

Configure the EmailJS template's file attachment to use `pdf_attachment` and the filename variable `pdf_filename`.
