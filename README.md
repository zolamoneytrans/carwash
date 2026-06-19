# carwash

Professional car wash booking platform with web and iOS app support.

## Project Overview

- **Web App**: PHP/Laravel-based backend with responsive web UI
- **iOS App**: SwiftUI wrapper app that loads the web application (bundle ID: `africa.carwashes.noq`)
- **CI/CD**: Codemagic integration for automated iOS App Store publishing

## Directory Structure

```
.
├── index.php, *.php          — Web application files
├── assets/                   — Web assets (CSS, JS, images)
├── admin/                    — Admin dashboard
├── business/                 — Business partner dashboard
├── includes/                 — Shared PHP includes
├── uploads/                  — User uploads (images, documents)
├── vendor/                   — Composer dependencies
├── codemagic.yaml           — CI/CD workflow for iOS builds
├── composer.json            — PHP dependencies
├── ios/                     — iOS native app project
│   ├── CarWash.xcodeproj/   — Xcode project file
│   ├── CarWash/             — App source code
│   │   ├── CarWashApp.swift
│   │   ├── ContentView.swift
│   │   ├── Info.plist
│   │   └── Assets.xcassets/
│   ├── exportOptions.plist  — App Store export config
│   └── README.md            — iOS setup guide
└── README.md (this file)
```

## Getting Started

### Web Development

1. Install PHP 7.4+ and MySQL
2. Set up `.env` or configure `db_connect.php` with database credentials
3. Install Composer dependencies: `composer install`
4. Run the web application locally

### iOS App Development

See [ios/README.md](ios/README.md) for complete iOS setup, local build, and App Store submission guide.

Quick start:
```bash
cd ios
open CarWash.xcodeproj
```

## Environment Configuration

### Web App
- Database connection: `db_connect.php`
- SMTP/Email: Uses environment variable `BREVO_SMTP_PASSWORD` (set in `.env` or CI/CD)
- PayFast integration: `payfast-*.php` files

### iOS App
- Web URL: `ContentView.swift` (currently loads `https://carwashes.africa`)
- Team ID: `44Z38W9GRD`
- Bundle ID: `africa.carwashes.noq`

## CI/CD: Codemagic

The `codemagic.yaml` workflow builds and publishes the iOS app to App Store Connect.

**Required environment variables** (set in Codemagic):
- `APP_STORE_CONNECT_API_KEY_ID`
- `APP_STORE_CONNECT_ISSUER_ID`
- `APP_STORE_CONNECT_PRIVATE_KEY` (from .p8 file)

See [ios/README.md](ios/README.md#codemagic-cicd) for detailed setup.

## Security Notes

- Never commit secrets (API keys, SMTP passwords, database credentials) to source control
- Use environment variables for sensitive configuration
- `.gitignore` excludes vendor, uploads, and temporary files
- SSH keys are excluded from the repository

## Development & Testing

### Testing the Web App
```bash
php -S localhost:8000
```

### Testing the iOS App
- **Simulator**: `xcodebuild` with `-sdk iphonesimulator`
- **Device**: Connect iOS device and sign with Apple Team ID

## Deployment

### Web App
Deploy PHP files to your web host. Ensure:
- PHP 7.4+ environment
- MySQL database
- HTTPS/SSL enabled
- Proper file permissions on `uploads/`

### iOS App
Use Codemagic to automate:
1. Build from GitHub
2. Archive with Xcode
3. Export for App Store
4. Upload to App Store Connect

Or manually: see [ios/README.md](ios/README.md#building-for-app-store)

## Support & Resources

- [Apple Developer Documentation](https://developer.apple.com/documentation/)
- [Codemagic Documentation](https://docs.codemagic.io/)
- [PayFast Documentation](https://www.payfast.co.za/developers)

---

**Repository**: [github.com/zolamoneytrans/carwash.git](https://github.com/zolamoneytrans/carwash.git)  
**Bundle ID**: africa.carwashes.noq  
**Team ID**: 44Z38W9GRD
