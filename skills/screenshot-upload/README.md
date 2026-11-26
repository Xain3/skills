# Screenshot upload setup

This guide explains how to configure screenshot uploads in local and cloud agent environments. Authentication differs by environment; use the route that matches where the upload command runs.

## Environment overview

| Environment | Authentication | Status from setup work |
| --- | --- | --- |
| Local Codex or Copilot session | `gcloud` service account impersonation | Upload and byte-for-byte verification succeeded in local WSL |
| GitHub Copilot cloud agent | Repository Agents secret with a service account JSON key | Skill discovery succeeded; a later cloud upload was verified byte-for-byte |
| Codex Cloud | Prefer Google Cloud OIDC/workload identity federation | Recommended, but the Codex Cloud upload flow and skill discovery have not been verified |

Results apply to the environment configuration at the time of each test. Check the current setup again in new environments and sessions.

## Requirements for every route

- Install the Google Cloud CLI (`gcloud`) in the environment that runs the upload.
- Set `BUCKET_NAME` to the intended bucket. The test bucket was `image-host-9632`.
- Allow HTTPS access to `storage.googleapis.com`. Authentication may also require OAuth, STS, or IAM Credentials endpoints, depending on the login method.
- Give the upload identity only bucket-scoped permissions. Storage Object Creator plus Storage Object Viewer supports uploads, listing, and download verification without object deletion. Check inherited IAM grants too.
- Use a unique object name and the `--if-generation-match=0` precondition so an existing object cannot be overwritten.
- Keep credentials out of source files, commits, logs, screenshots, and chat. Never print secret values.
- Verify uploads by downloading the exact object and comparing its bytes with the source. A successful CLI exit alone is not proof.
- A `gs://` path is not an embeddable image URL. Confirm that reviewers can access the URL before adding it to a PR.

Do not make a bucket or its objects public as a shortcut. An ordinary HTTPS object URL works only when the viewer has access. Signed URLs expire; the tests used a 12-hour lifetime, so they are unsuitable as lasting PR image links. If there is no reviewer-accessible URL that preserves the intended access policy, use an approved alternative or explain the limitation.

## Local sessions: Codex or Copilot

The local route uses the active Google identity to impersonate a service account. The user needs `iam.serviceAccounts.getAccessToken` on the target account, commonly granted through Service Account Token Creator. The impersonated service account needs the bucket permissions described above.

From the repository root, the skill loads `.env.gcloud` if it exists. This is a trusted shell file, so inspect it before sourcing it. It is git-ignored and may contain configuration such as:

- `BUCKET_NAME`
- `SERVICE_ACCOUNT_EMAIL`
- `BUCKET_REGION` (only needed for signed URL generation when automatic bucket-location detection is unavailable)

The file must not contain service account keys or other credentials. Do not commit it.

The local upload commands use service account impersonation:

    gcloud storage ls "gs://${BUCKET_NAME}/" \
      --impersonate-service-account="${SERVICE_ACCOUNT_EMAIL}"

    gcloud storage cp --if-generation-match=0 "${SCREENSHOT_PATH}" \
      "gs://${BUCKET_NAME}/screenshots/${SCREENSHOT_NAME}" \
      --impersonate-service-account="${SERVICE_ACCOUNT_EMAIL}"

A bucket location is not needed to upload. It may be needed to generate a signed URL if the identity cannot call `storage.buckets.get`; use the bucket’s actual region rather than guessing.

## GitHub Copilot cloud agent

Configure `GCP_SERVICE_ACCOUNT_KEY` as a repository Copilot Agents secret, and provide `BUCKET_NAME` as an environment value or Agents secret available to the agent. Local developer variables and GitHub Actions-only secrets are not necessarily available to a Copilot cloud session.

The skill writes the JSON key to a temporary file with restrictive permissions, sets `CLOUDSDK_AUTH_CREDENTIAL_FILE_OVERRIDE`, and removes the file when the shell exits. Keep the authentication check and upload in that same shell. Do not use `--impersonate-service-account` in this route: `gcloud` authenticates directly as the service account key.

The cloud environment must resolve and reach the authentication and Storage endpoints used by the CLI. One test initially failed because `oauth2.googleapis.com` would not resolve; a later retry succeeded after the network configuration changed. Check DNS and HTTPS connectivity before sending credentials. DNS or connection failures indicate a network-stage failure; any HTTP response, even an error response, confirms that the host was reached.

## Codex Cloud

Codex Cloud network secrets are request-proxy credentials. Programs receive a placeholder rather than the raw secret, so a network secret containing service account JSON cannot be used as the temporary key file in the Copilot procedure. Legacy secrets are available during setup, not as normal runtime credentials.

For Codex Cloud, prefer the environment’s **Advanced → OIDC → Google Cloud** setup:

1. Configure Google Cloud workload identity trust using the issuer, audience, and subject values shown by the Codex environment.
2. Grant the federated identity only the bucket-level permissions required for upload and verification.
3. Install `gcloud` and allow the token-exchange and Storage endpoints required by the selected federation flow.
4. Add and verify a Codex Cloud-specific authentication sequence in this skill before relying on it.

Local impersonation tests do not verify Codex Cloud OIDC. Copilot cloud tests do not verify Codex Cloud skill discovery. Treat the OIDC steps above as setup guidance, not as a tested command sequence.

## Naming, verification, and cleanup

Use a unique, descriptive object name under `screenshots/`, for example:

    screenshots/ui-desktop-<UTC timestamp>-<random id>.png

For a mock upload test:

1. Generate a clearly labeled synthetic screenshot in temporary storage.
2. Upload it with the no-overwrite precondition.
3. Download that exact object and compare its bytes with the source.
4. If embedding it, check the HTTP status and content type from the intended viewer context.
5. Remove temporary credential and verification files. Preserve the uploaded test object unless the user asks to remove it.

Tests should not modify repository files, PR descriptions, or bucket policies unless specifically requested. Do not infer that a bucket is public or private based on an upload test; inspect its access separately when authorized.
