# FusionAuth IAM Security Lab

Εργαστηριακή αξιολόγηση του FusionAuth ως self-hosted πλατφόρμας Identity and Access Management. Το project καταγράφει ρυθμίσεις και δοκιμές για χρήστες και ομάδες, RBAC, SSO, MFA, OIDC/PKCE, πολιτικές πρόσβασης, audit logs, περιορισμένα API keys και Kickstart. Περιλαμβάνει επίσης τα ευρήματα και τους περιορισμούς της αξιολόγησης, με βάση το τοπικό περιβάλλον δοκιμών.

> **Κατάσταση:** Το repository περιέχει το βασικό Docker Compose και ασφαλή παραδείγματα Kickstart. Δεν περιέχει ακόμη την OIDC ενσωμάτωση και τις role-protected σελίδες της εφαρμογής case study, ούτε τη δημόσια ανωνυμοποιημένη αναφορά.

## Περιβάλλον εργαστηρίου

- FusionAuth 1.69.4 σε τοπικό Docker περιβάλλον.
- PostgreSQL για τη βάση δεδομένων και OpenSearch για αναζήτηση.
- Mailpit για δοκιμές email.
- Ξεχωριστό demo Todo με Node.js/Express, EJS και MongoDB.

Το Todo demo εκκινήθηκε επιτυχώς, αλλά δεν τεκμηριώνεται σύνδεσή του με FusionAuth μέσω OIDC. Είναι ξεχωριστός κλώνος του [Docker multi-container-app](https://github.com/docker/multi-container-app) και δεν περιλαμβάνεται σε αυτό το repository.

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
├── docker-compose.yml                   # βασικό FusionAuth stack, τιμές από .env
├── docker-compose.kickstart.example.yml # απομονωμένο Kickstart demo stack
├── .env.example                         # placeholders, ποτέ πραγματικά secrets
├── kickstart-example/kickstart.json     # παράδειγμα bootstrap
└── docs/
    ├── report/              # δημόσιο, ανωνυμοποιημένο PDF
    └── evidence/             # επιλεγμένα redacted screenshots
```

Μην αντιγράψεις στο δημόσιο repository τα αρχικά αρχεία σημειώσεων, τα exports των logs, ολόκληρο τον φάκελο screenshots ή `.env` αρχεία. Πριν από κάθε προσθήκη έλεγξε για API keys, passwords, client secrets, tokens, OTP/recovery codes, email, IDs χρηστών και IP addresses. Τα secrets που εμφανίστηκαν σε παλιά αρχεία πρέπει να ανακληθούν/αλλάξουν πριν δημοσιευτεί οτιδήποτε.

## Εκκίνηση

Αν δεν έχεις ήδη τοπικό `.env`, ξεκίνα από το παράδειγμα και βάλε δικές σου τιμές:

```bash
cp .env.example .env
# Επεξεργάσου το .env και αντικατάστησε όλα τα replace-* placeholders.
docker-compose up -d
docker-compose ps
```

Για το ξεχωριστό Kickstart demo stack:

```bash
docker-compose -f docker-compose.kickstart.example.yml up -d
```

Το Kickstart sample χρειάζεται μοναδικό `FUSIONAUTH_API_KEY` στο τοπικό `.env` και εκτελείται μόνο σε καινούργιο/μη αρχικοποιημένο FusionAuth instance. Μην ανεβάσεις ποτέ το πραγματικό `.env` ή το τοπικό `kickstart/kickstart.json`. Για δοκιμές χρησιμοποίησε ξεχωριστά credentials και μην εκθέτεις το development stack σε δημόσιο δίκτυο.

## Υλικό αναφοράς

Η δημόσια έκδοση της αναφοράς θα τοποθετηθεί στο `docs/report/` αφού αφαιρεθούν προσωπικά στοιχεία. Τα screenshots θα προστεθούν στο `docs/evidence/` μόνο αφού επανελεγχθούν και καλυφθούν τυχόν ευαίσθητα δεδομένα.

## Άδεια χρήσης

Δεν έχει οριστεί ακόμη άδεια χρήσης. Πρόσθεσε `LICENSE` μόνο αφού επιλέξεις την άδεια που ταιριάζει στο project και στα δικαιώματα των περιεχόμενων υλικών.
