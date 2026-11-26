---
name: screenshot-upload
description: Attach screenshots to pull request descriptions with GitHub CLI, using Google Cloud Storage only when CLI attachment is unavailable.
version: 1.1
---

# Screenshot upload

Prefer GitHub CLI's `--attach` flag. It uploads the local image to GitHub and
replaces the matching local Markdown image path with the hosted URL. This keeps
the image in the PR description without setting up a separate storage upload.
GitHub CLI attachment requires push access to the repository and supports image
and media files.

Set `SCREENSHOT_PATH` to the captured screenshot. Include a Markdown reference
to that same path under the `Screenshots` heading in the PR body, then pass the
path to `--attach`:

```sh
gh pr create \
  --title "PULL-REQUEST-TITLE" \
  --body-file PATH/TO/BODY-FILE \
  --attach "$SCREENSHOT_PATH"
```

For an existing PR, update its body with the screenshot reference and attach
the matching file:

```sh
gh pr edit PR-NUMBER \
  --body-file PATH/TO/BODY-FILE \
  --attach "$SCREENSHOT_PATH"
```

For example, the body file can contain:

```markdown
## Screenshots

![Desktop view](./artifacts/desktop.png)
```

Set `SCREENSHOT_PATH` to `./artifacts/desktop.png`. GitHub CLI rewrites the
local path to the uploaded URL. If the body does not reference an attached
file, the URL is appended to the body instead.

## Google Cloud Storage fallback

Use the configured bucket only if GitHub CLI attachment is unavailable or
cannot provide a reviewer-accessible image. Set `SCREENSHOT_PATH` and
`SCREENSHOT_NAME` before uploading. Embed an accessible HTTPS URL in the PR
description; a `gs://` path alone does not display an image. Do not change
bucket access policies to make screenshots public.

### Copilot cloud agent

Use the JSON service-account key in the `GCP_SERVICE_ACCOUNT_KEY` repository
Agents secret and `BUCKET_NAME` from the environment or an Agents secret. Keep
the key out of the repository, logs, and chat. Run the access check and upload
in the same shell so the temporary credential file is removed on exit:

```sh
set +x
set -eu
: "${GCP_SERVICE_ACCOUNT_KEY:?GCP_SERVICE_ACCOUNT_KEY must be set}"
: "${BUCKET_NAME:?BUCKET_NAME must be set}"
umask 077
KEY_FILE="$(mktemp)"
trap 'rm -f "$KEY_FILE"' EXIT
printf '%s' "$GCP_SERVICE_ACCOUNT_KEY" > "$KEY_FILE"
export CLOUDSDK_AUTH_CREDENTIAL_FILE_OVERRIDE="$KEY_FILE"

gcloud storage ls "gs://${BUCKET_NAME}/"
gcloud storage cp --if-generation-match=0 "$SCREENSHOT_PATH" \
  "gs://${BUCKET_NAME}/screenshots/${SCREENSHOT_NAME}"
```

The key authenticates as the service account, so do not add
`--impersonate-service-account` in cloud sessions. Do not print or commit the
key, and do not leave credential files in the workspace.

### Local sessions

If an authenticated user has permission to impersonate the service account,
use `BUCKET_NAME` and `SERVICE_ACCOUNT_EMAIL` from the environment. From the
repository root, load `.env.gcloud` if present; its values override existing
environment variables. Only source a file you trust, as it is shell code. The
file is git-ignored and must not contain credentials or keys.

```sh
set +x
set -eu
if [ -f .env.gcloud ]; then
  . ./.env.gcloud
fi
: "${BUCKET_NAME:?BUCKET_NAME must be set}"
: "${SERVICE_ACCOUNT_EMAIL:?SERVICE_ACCOUNT_EMAIL must be set}"
: "${SCREENSHOT_PATH:?SCREENSHOT_PATH must be set}"
: "${SCREENSHOT_NAME:?SCREENSHOT_NAME must be set}"

gcloud storage ls "gs://${BUCKET_NAME}/" \
  --impersonate-service-account="$SERVICE_ACCOUNT_EMAIL"
gcloud storage cp --if-generation-match=0 "$SCREENSHOT_PATH" \
  "gs://${BUCKET_NAME}/screenshots/${SCREENSHOT_NAME}" \
  --impersonate-service-account="$SERVICE_ACCOUNT_EMAIL"
```

If all available authentication routes fail, ask whether the user wants to
retry authentication or configure the required credentials and settings
through a secure local secret mechanism. Never ask them to paste service
account keys or other secrets into chat, logs, or the repository.
