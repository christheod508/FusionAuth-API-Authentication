# FusionAuth IAM Security Lab

Εργαστηριακή αξιολόγηση του FusionAuth ως self-hosted πλατφόρμας Identity and Access Management. Το project καταγράφει ρυθμίσεις και δοκιμές για χρήστες και ομάδες, RBAC, SSO, MFA, OIDC/PKCE, πολιτικές πρόσβασης, audit logs, περιορισμένα API keys και Kickstart. Περιλαμβάνει επίσης τα ευρήματα και τους περιορισμούς της αξιολόγησης, με βάση το τοπικό περιβάλλον δοκιμών.

> **Κατάσταση δημοσίευσης:** Ο παρών φάκελος περιέχει κυρίως αναφορά, σημειώσεις και αποδεικτικό υλικό. Δεν περιέχει ακόμη το κύριο `docker-compose.yml` ούτε τον κώδικα της εφαρμογής case study. Επομένως δεν είναι ακόμη πλήρως αναπαραγώγιμο project με απλό clone και εκκίνηση.

## Περιβάλλον εργαστηρίου

- FusionAuth 1.69.4 σε τοπικό Docker περιβάλλον.
- PostgreSQL για τη βάση δεδομένων και OpenSearch για αναζήτηση.
- Mailpit για δοκιμές email.
- Ξεχωριστό demo Todo με Node.js/Express, EJS και MongoDB.

Το Todo demo εκκινήθηκε επιτυχώς, αλλά δεν τεκμηριώνεται σύνδεσή του με FusionAuth μέσω OIDC. Δεν παρουσιάζεται ως ολοκληρωμένη εφαρμογή case study.

## Αποτελέσματα και όρια

| Περιοχή | Αποτέλεσμα |
|---|---|
| Χρήστες, ομάδες και ρόλοι | Δημιουργία χρηστών/ομάδων και έλεγχος ρόλων `admin`, `editor`, `viewer`. |
| SSO και MFA | Δοκιμές σύνδεσης σε εφαρμογές του ίδιου tenant και TOTP challenge. |
| OIDC / PKCE | Ελέγχθηκε το discovery και η ρύθμιση PKCE/S256. Η ενσωμάτωση της εφαρμογής case study εκκρεμεί. |
| SAML και Client Credentials | Έγινε ρύθμιση/διερεύνηση, αλλά δεν υπάρχει πλήρης επιτυχής end-to-end απόδειξη και για τα δύο. |
| Google Identity Provider | Ρυθμίστηκε, αλλά η δοκιμή σταμάτησε σε σφάλμα scope/redirect URI· δεν καταγράφεται ως επιτυχές login. |
| Πολιτικές και JWT lambda | Ελέγχθηκαν ρυθμίσεις κωδικού, κλειδώματος και registration. Η `jwt1` προσθέτει `clearanceLevel` στο access token από το `user.data.department`. |
| Audit, Event Logs και Reports | Εξετάστηκαν οι καταγραφές και τα reports. Η επιτυχής παράδοση webhook χρειάζεται σαφέστερο αποδεικτικό. |
| Admin και API key | Δοκιμάστηκαν περιορισμένος διαχειριστικός ρόλος και περιορισμένα API permissions. Το Kickstart περιβάλλον εκκινήθηκε· χρειάζεται τελικό αποδεικτικό της εφαρμοσμένης ρύθμισης. |
| Ενσωμάτωση | Το αρχικό Express Todo demo λειτουργεί, αλλά η σύνδεση OIDC και οι role-protected σελίδες εκκρεμούν. |
| Παραμετροποιήσεις | Έγιναν ρυθμίσεις theme και tenant. Το email template και οι custom registration fields χρειάζονται καθαρό τελικό αποδεικτικό. |
| Επεκτασιμότητα | Δεν εκτελέστηκε δοκιμή δύο FusionAuth instances πίσω από nginx ή load test. |

Η αναλυτική αναφορά και τα screenshots πρέπει να προστεθούν μόνο σε δημόσια, αποχαρακτηρισμένη έκδοση. Η αρχική αναφορά περιέχει στοιχεία ταυτοποίησης στο εξώφυλλο, ενώ τα πρωτογενή notes, exports και screenshots περιέχουν ή ενδέχεται να περιέχουν διαπιστευτήρια και προσωπικά δεδομένα.

## Προτεινόμενη δομή GitHub

```text
.
├── README.md
├── docker-compose.yml       # sanitized, χωρίς πραγματικά secrets
├── .env.example             # μόνο placeholders
├── kickstart/
│   └── kickstart.json       # sanitized demo configuration
├── app/                     # case study, όταν ολοκληρωθεί η OIDC ενσωμάτωση
└── docs/
    ├── report/              # δημόσιο, ανωνυμοποιημένο PDF
    └── evidence/             # επιλεγμένα redacted screenshots
```

Μην αντιγράψεις στο δημόσιο repository τα αρχικά αρχεία σημειώσεων, τα exports των logs, ολόκληρο τον φάκελο screenshots ή `.env` αρχεία. Πριν από κάθε προσθήκη έλεγξε για API keys, passwords, client secrets, tokens, OTP/recovery codes, email, IDs χρηστών και IP addresses. Τα secrets που εμφανίστηκαν σε παλιά αρχεία πρέπει να ανακληθούν/αλλάξουν πριν δημοσιευτεί οτιδήποτε.

## Εκκίνηση

Οι παρακάτω εντολές ισχύουν αφού προστεθούν στο repository το καθαρισμένο Compose αρχείο και το `.env.example`:

```bash
cp .env.example .env
docker compose up -d
docker compose ps
```

Μην ανεβάσεις ποτέ το πραγματικό `.env`. Για δοκιμές/τοπική ανάπτυξη, χρησιμοποίησε ξεχωριστά credentials και άλλαξε τα προεπιλεγμένα passwords πριν από οποιαδήποτε έκθεση του service στο δίκτυο.

## Υλικό αναφοράς

Η δημόσια έκδοση της αναφοράς θα τοποθετηθεί στο `docs/report/` αφού αφαιρεθούν προσωπικά στοιχεία. Τα screenshots θα προστεθούν στο `docs/evidence/` μόνο αφού επανελεγχθούν και καλυφθούν τυχόν ευαίσθητα δεδομένα.

## Άδεια χρήσης

Δεν έχει οριστεί ακόμη άδεια χρήσης. Πρόσθεσε `LICENSE` μόνο αφού επιλέξεις την άδεια που ταιριάζει στο project και στα δικαιώματα των περιεχόμενων υλικών.
