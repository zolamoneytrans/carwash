# iOS App Setup Guide

## Project Structure

- **ios/CarWash.xcodeproj** — Xcode project file for the native iOS app
- **ios/CarWash/CarWashApp.swift** — SwiftUI app entry point
- **ios/CarWash/ContentView.swift** — Main WebView container loading the web app
- **ios/CarWash/Info.plist** — App metadata and configuration
- **ios/CarWash/LaunchScreen.storyboard** — Launch screen UI
- **ios/CarWash/Assets.xcassets** — App icons and asset catalog
- **ios/exportOptions.plist** — Xcode export settings for App Store distribution
- **codemagic.yaml** — CI/CD workflow for automated builds and App Store uploads

## Prerequisites

- **macOS** (required for Xcode build)
- **Xcode 14.0+** with iOS 16.0 SDK
- **Apple Developer Account** with Team ID: `44Z38W9GRD`
- **App Store Connect** credentials
- **GitHub** account for source control

## Local Build & Test

### 1. Open Project in Xcode

```bash
open ios/CarWash.xcodeproj
```

### 2. Set Team ID

In Xcode, select the **CarWash** target:
- **Signing & Capabilities** → **Team** → Select your Apple Team
- Ensure **Automatic signing** is enabled

### 3. Update Bundle Identifier (if needed)

- **Build Settings** → **Product Bundle Identifier** → `africa.carwashes.noq`

### 4. Build for Simulator

```bash
xcodebuild -project ios/CarWash.xcodeproj \
  -scheme CarWash \
  -configuration Debug \
  -sdk iphonesimulator \
  build
```

### 5. Build for Device

```bash
xcodebuild -project ios/CarWash.xcodeproj \
  -scheme CarWash \
  -configuration Release \
  -sdk iphoneos \
  build
```

## Building for App Store

### 1. Create Archive

```bash
xcodebuild -project ios/CarWash.xcodeproj \
  -scheme CarWash \
  -configuration Release \
  -sdk iphoneos \
  archive \
  -archivePath build/CarWash.xcarchive
```

### 2. Export for App Store

```bash
xcodebuild -exportArchive \
  -archivePath build/CarWash.xcarchive \
  -exportPath build/ios \
  -exportOptionsPlist ios/exportOptions.plist
```

### 3. Upload to App Store Connect

Use **Xcode Organizer** or **Transporter** CLI:

```bash
xcrun altool --upload-app \
  -f build/ios/CarWash.ipa \
  -t ios \
  -u your-email@apple.com \
  -p your-app-specific-password
```

## Codemagic CI/CD

The `codemagic.yaml` workflow automates building and publishing to App Store Connect.

### Required Environment Variables (set in Codemagic):

- `APP_STORE_CONNECT_API_KEY_ID` — App Store Connect API key ID
- `APP_STORE_CONNECT_ISSUER_ID` — App Store Connect issuer ID  
- `APP_STORE_CONNECT_PRIVATE_KEY` — App Store Connect API private key (.p8 content)

### Trigger a Build

Push to the main branch or trigger manually in Codemagic UI.

## App Store Connect Setup

1. **Create App Record**
   - Go to [App Store Connect](https://appstoreconnect.apple.com/)
   - Create new iOS app with:
     - Bundle ID: `africa.carwashes.noq`
     - Name: CarWash
     - Primary Language: English
     - Category: Lifestyle or Business

2. **Generate API Key**
   - Users & Access → API Keys → Create new key
   - Download `.p8` file and store securely
   - Note: **Key ID** and **Issuer ID**

3. **Add App Information**
   - App Title, Subtitle, Description
   - Keywords, Support URL, Privacy Policy
   - App Icon (1024×1024) — use the generated icon from `ios/CarWash/Assets.xcassets`

4. **Create Version Release**
   - Add version number (e.g., 1.0)
   - Add release notes
   - Add screenshots (3–5 per device type)

## Troubleshooting

### "Team ID mismatch"
→ Verify `DEVELOPMENT_TEAM` in `project.pbxproj` matches Apple Developer account Team ID.

### "Provisioning profile not found"
→ Enable **Automatic signing** in Xcode or manually create a provisioning profile on Apple Developer Portal.

### "Invalid certificate"
→ Revoke and re-create signing certificate in Apple Developer Portal.

### "Build fails on Codemagic"
→ Ensure all required env vars are set and `.p8` key is valid (not expired).

## Notes

- The app loads the web URL from **ContentView.swift** (currently `https://carwashes.africa`)
- Update the URL if needed in `ContentView.swift`
- App icons are placeholder blue; replace with proper branded icons
- Launch screen displays "🚗 CarWash" with a blue background

---

For more info, see [Apple Developer Documentation](https://developer.apple.com/documentation/) and [Codemagic Docs](https://docs.codemagic.io/).
