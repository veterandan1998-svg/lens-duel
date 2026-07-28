# LensDuel Android TWA wrapper

This branch contains a GitHub Actions workflow that generates a Trusted Web Activity (TWA) Android project with Bubblewrap in CI, builds a signed Android App Bundle (AAB), and uploads it as an artifact named `com.lensduel.myapp.aab`.

IMPORTANT: Do NOT commit your keystore into the repository. Add it as a GitHub Actions secret as described below.

Secrets required (Repository settings → Secrets → Actions):

- KEYSTORE_BASE64 — base64-encoded contents of your release JKS keystore
- KEYSTORE_PASSWORD — keystore password
- KEY_ALIAS — key alias (e.g., lensduel)
- KEY_PASSWORD — key (alias) password

How to generate a release keystore locally and create the secrets:

1) Generate keystore (run locally):

```bash
keytool -genkeypair -v -keystore my-release-key.jks -alias lensduel -keyalg RSA -keysize 2048 -validity 10000
```

2) Base64-encode the keystore and copy the output:

macOS / Linux:

```bash
base64 my-release-key.jks | pbcopy
```

Windows (PowerShell):

```powershell
[Convert]::ToBase64String([IO.File]::ReadAllBytes("my-release-key.jks")) | clip
```

3) In GitHub: Settings → Secrets and variables → Actions, create the four secrets listed above.

4) Trigger the workflow in Actions → Build TWA AAB → Run workflow. The workflow will produce a signed AAB and upload it as an artifact named `com.lensduel.myapp.aab`.

Notes:
- The workflow will use Bubblewrap (https://github.com/GoogleChromeLabs/bubblewrap) to initialize and build the TWA from your PWA manifest at https://misty-lens-duel-pro.base44.app/manifest.json.
- If the PWA manifest is not present or Bubblewrap init fails, the workflow will fail. If that happens I can update the workflow to use a fallback WebView wrapper instead.
