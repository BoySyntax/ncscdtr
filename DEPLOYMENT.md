# NCSC deployment and handover

## Hosted setup status (2026-11-03)

- NCSC project: `sjqweiiouzsmkkmiiphb`, Singapore, in the separate NCSC organization.
- All 22 migrations applied; all public application tables verified with RLS enabled.
- Email confirmation enabled; password minimum is eight characters. Site URL is
  currently localhost, with localhost password-recovery redirects for testing.
- Hosted `ncscadmin` and `ncscsecurity` accounts created and their server-side roles
  verified. Credentials are retained separately, never in this repository.
- `ncscadmin` maps to an internal Auth address for username login. This mapping
  grants no privileges; the protected profile determines administrator access.
  Signed-in password changes work. Forgotten passwords require a Supabase project
  owner to reset the password because this internal address has no email inbox.
- Gmail SMTP is configured with the temporary shared sender and NCSC DTR display
  name. Confirmation delivery, confirmed-account sign-in and rejection of
  unconfirmed sign-in passed. Supabase's email limit is 30 per hour; provider
  limits also apply. End-to-end password recovery remains to be tested.
- Chief password and a separately generated recovery code are installed; stored
  hash verification passed. Their values are never included in this document.
- Final production URL, Vercel deployment and deployed workflow checks remain.
  Local seed accounts are separate from hosted accounts.

## Deployment procedure

This is a separate deployment. Never reuse a PSA database URL, service-role key,
Vercel project link. Chief credentials are configured independently; a new recovery
code is used even when the custodian requests the same temporary password.
Local source setup is not a deployment.

1. Create a fresh NCSC Supabase project and an empty `ncsc-dtr-system` repository.
2. Verify the new project reference and that Auth and application data are empty.
3. Review/apply the complete ordered `supabase/migrations` chain to that project.
   The NCSC-specific migration is deliberately fresh-install only.
4. Provision a new administrator and `ncscsecurity` account with unique passwords.
   Install a new Chief approval password/recovery code through the trusted setup.
   Do not use the local seed script against hosted services.
5. Import the NCSC repository into a separate Vercel project. Use the checked-in
   TanStack Start configuration. Choose the production branch explicitly.
6. Set the following in Vercel Production using only the NCSC project's values:

| Variable | Kind |
| --- | --- |
| VITE_SUPABASE_URL | Public project URL |
| VITE_SUPABASE_PUBLISHABLE_KEY | Public anon/publishable key |
| SUPABASE_URL | Same project URL |
| SUPABASE_PUBLISHABLE_KEY | Public anon/publishable key |
| SUPABASE_SERVICE_ROLE_KEY | Server-only secret |
| PUBLIC_SITE_URL | Final stable HTTPS NCSC deployment origin |

7. Configure Supabase email and exact redirect URLs as in AUTH_PRODUCTION.md.
8. Verify signup/confirmation, recovery, all three categories, punch persistence,
   Travel Out, exports, inactive access, guard isolation and Chief-approved actions.
9. Transfer hosting/repository/database/sender ownership as agreed at handover.
   Deliver passwords and Chief recovery codes privately, not in repository files.

The destination for hosted helper scripts must be supplied explicitly through
`NCSC_SUPABASE_PROJECT_REF`. Known PSA projects are rejected. Never run migrations
against the PSA project. No hosted destination is preconfigured in this copy.

Before release run `npm run test:local`, `npm run test:provisioning`,
`npm run test:account-types`, `npm run test:e2e`, `npm run typecheck`, changed-file
lint and `npm run build`. Environment files, test credentials and test output are
excluded from source control and deployment.
