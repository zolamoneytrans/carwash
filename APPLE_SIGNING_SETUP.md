# Apple Code Signing Setup for Codemagic

This guide walks you through exporting your signing certificate and provisioning profile from Apple Developer to upload to Codemagic.

## Prerequisites

- Apple Developer account with access to Team ID: `44Z38W9GRD`
- Xcode installed (for certificate extraction)
- App ID `africa.carwashes.noq` created in Apple Developer portal
- App Store distribution certificate and provisioning profile already created

---

## Step 1: Export Signing Certificate from Keychain (macOS)

### Option A: Export Distribution Certificate (.p12)

1. Open **Keychain Access** on your Mac
2. Search for your **Apple Distribution** certificate for Team ID `44Z38W9GRD`
3. Right-click the certificate → **Export**
4. Save as `CarWash_Distribution.p12`
5. Enter a password (required) — **remember this for `CERTIFICATE_PASSWORD`**
6. Save to a secure location

### Option B: Via Xcode (Automatic)

If using automatic Xcode provisioning:

```bash
# Extract certificate from provisioning profile
security find-certificate -p "/path/to/provisioning/profile.mobileprovision" > cert.pem

# Convert to .p12
openssl pkcs12 -export -in cert.pem -inkey private_key.pem -out CarWash_Distribution.p12 -passout pass:YOUR_PASSWORD
```

---

## Step 2: Download Provisioning Profile from Apple Developer

1. Go to [developer.apple.com](https://developer.apple.com)
2. Sign in with your Apple ID
3. Navigate to **Certificates, Identifiers & Profiles** → **Profiles**
4. Find the **App Store** provisioning profile for `africa.carwashes.noq`
5. Download it (saves as `.mobileprovision` file)
6. Save it with a clear name: `CarWash_AppStore.mobileprovision`

---

## Step 3: Verify Files

```bash
# Verify .p12 certificate structure
openssl pkcs12 -in CarWash_Distribution.p12 -noout -info

# Check provisioning profile UUID (use this as PROVISIONING_PROFILE value)
security cms -D -i CarWash_AppStore.mobileprovision
```

---

## Step 4: Upload to Codemagic

### In Codemagic UI:

1. Go to your **carwash** app
2. Click **Settings** (gear icon, top-right)
3. Select **Code signing** tab
4. **Upload Signing certificate:**
   - Click **Upload certificate**
   - Select `CarWash_Distribution.p12`
   - Enter the password you created in Step 1
5. **Upload Provisioning Profile:**
   - Click **Upload provisioning profile**
   - Select `CarWash_AppStore.mobileprovision`
   - Note the reference name (e.g., `CarWash AppStore`)

### Create Environment Variables:

In **Settings** → **Environment variables**, add:

| Variable | Value | Notes |
|----------|-------|-------|
| `CERTIFICATE_PASSWORD` | `your_p12_password` | Password from Step 1 |
| `PROVISIONING_PROFILE` | `CarWash_AppStore.mobileprovision` | File reference from upload |
| `APP_STORE_API_KEY` | (from App Store Connect) | See Step 5 |
| `APP_STORE_ISSUER_ID` | (from App Store Connect) | See Step 5 |

---

## Step 5: Get App Store Connect API Key

1. Go to [appstoreconnect.apple.com](https://appstoreconnect.apple.com)
2. Navigate to **Users and Access** → **Keys**
3. Click **Generate a Key** (or use existing)
4. Download the `.p8` file (private key)
5. Copy the **Key ID** (for `APP_STORE_API_KEY`)
6. Copy the **Issuer ID** (for `APP_STORE_ISSUER_ID`)

Add these as environment variables in Codemagic:

```
APP_STORE_API_KEY=<contents of .p8 file>
APP_STORE_ISSUER_ID=<your issuer ID>
```

---

## Step 6: Re-run Build in Codemagic

1. Refresh **codemagic.yaml** (click "Check for configuration files")
2. Trigger a new build for `main` branch
3. Monitor build logs for signing success

---

## Troubleshooting

- **"No Accounts"** → Upload certificate (Step 4)
- **"No profiles found"** → Upload provisioning profile & verify `PROVISIONING_PROFILE_SPECIFIER` matches
- **"Invalid certificate password"** → Verify `CERTIFICATE_PASSWORD` env var is correct
- **"API key rejected"** → Ensure `APP_STORE_API_KEY` and `APP_STORE_ISSUER_ID` are set and not expired

For more info, see [Codemagic Docs: Code Signing](https://docs.codemagic.io/code-signing/ios-code-signing/).
