# Android Build Workflows - Quick Reference

## Available Build Types

### 1. **Basic Debug Build** (`basic-build`)
- **Purpose**: Development and testing
- **Triggers**: Push/PR to main or develop branches
- **Output**: Debug APK (debuggable, not optimized)
- **Signing**: Debug keystore (auto-generated)
- **Retention**: 7 days
- **Use case**: Testing features, debugging

### 2. **Release Build Unsigned** (`release-build`)
- **Purpose**: Release candidate without signing
- **Triggers**: Push/PR to main or develop branches
- **Output**: Release APK (optimized, unsigned)
- **Signing**: None
- **Retention**: 14 days
- **Use case**: Testing release builds, preparing for manual signing

### 3. **Signed Release Build** (`signed-build`)
- **Purpose**: Production-ready signed APK
- **Triggers**: Push to main branch only
- **Output**: Release APK (optimized, signed)
- **Signing**: Release keystore from secrets
- **Retention**: 30 days
- **Use case**: Publishing to Play Store or distributing to users

## Running Workflows

### Automatic Triggers
- Push to `main` or `develop` → Runs all applicable workflows
- Pull Request → Runs basic-build and release-build
- Push tag (e.g., `v1.0.0`) → Runs signed-build and creates GitHub release

### Manual Trigger
1. Go to Actions tab in GitHub
2. Select "Android Build" workflow
3. Click "Run workflow"
4. Choose the branch
5. Click "Run workflow" button

## Local Build Commands

```bash
# Debug build
./gradlew assembleDebug

# Release build (unsigned)
./gradlew assembleRelease

# Release build (signed) - requires local.properties setup
./gradlew assembleRelease
```

## Setting Up Secrets (Required for Signed Builds)

Navigate to: **Repository → Settings → Secrets and variables → Actions**

Add these secrets:
- `KEYSTORE_BASE64`: Base64-encoded keystore file
- `KEYSTORE_PASSWORD`: Keystore password
- `KEY_ALIAS`: Key alias name
- `KEY_PASSWORD`: Key password

## Build Output Locations

```
test/build/outputs/apk/
├── debug/
│   └── test-debug.apk
└── release/
    ├── test-release-unsigned.apk  (if not signed)
    └── test-release.apk           (if signed)
```

## Troubleshooting

### Signed build fails
- Check if all 4 secrets are set correctly
- Verify keystore is valid and base64-encoded properly
- Check workflow logs for specific error messages

### Build artifacts not found
- Verify the path in workflow matches actual build output
- Check if build completed successfully

### Wrong APK built
- Check branch triggers in workflow file
- Verify workflow conditions (`if:` statements)

## Workflow Features

✅ Gradle dependency caching for faster builds  
✅ Automatic artifact upload  
✅ Support for manual workflow dispatch  
✅ Secure keystore handling  
✅ Automatic cleanup of sensitive files  
✅ GitHub release integration for tags  

## Security Best Practices

❌ Never commit keystore files to repository  
❌ Never commit passwords in code or config files  
✅ Always use GitHub Secrets for credentials  
✅ Use `.gitignore` to exclude keystore files  
✅ Keep local.properties out of version control  
✅ Regularly rotate signing keys for production apps  
