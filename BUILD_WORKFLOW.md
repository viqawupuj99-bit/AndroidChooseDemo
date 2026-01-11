# GitHub Actions Secrets Setup for Signed Builds

This project includes three build workflows:

## 1. Basic Debug Build
- Runs on every push and pull request
- Builds debug APK
- No special configuration needed

## 2. Release Build (Unsigned)
- Runs on every push and pull request  
- Builds release APK without signing
- No special configuration needed

## 3. Signed Release Build
- Runs only on pushes to main branch
- Requires GitHub Secrets to be configured
- Produces signed release APK

## Required GitHub Secrets for Signed Builds

To enable signed release builds, configure these secrets in your repository:

### 1. KEYSTORE_BASE64
Your keystore file encoded in base64.

To generate:
```bash
base64 -w 0 your-release-keystore.jks
```

Copy the output and add it as a secret named `KEYSTORE_BASE64`

### 2. KEYSTORE_PASSWORD
The password for your keystore file.

### 3. KEY_ALIAS
The alias of the key in your keystore.

### 4. KEY_PASSWORD
The password for the key.

## How to Set Secrets

1. Go to your GitHub repository
2. Navigate to Settings → Secrets and variables → Actions
3. Click "New repository secret"
4. Add each secret with the exact names listed above

## Generating a New Keystore (if needed)

If you don't have a keystore, create one:

```bash
keytool -genkey -v -keystore release-keystore.jks \
  -alias your-key-alias \
  -keyalg RSA -keysize 2048 -validity 10000
```

**Important**: Keep your keystore file and passwords secure! Never commit them to your repository.

## Local Signed Build

You can also build signed APKs locally:

```bash
./gradlew assembleRelease \
  -Pandroid.injected.signing.store.file=/path/to/your-release-keystore.jks \
  -Pandroid.injected.signing.store.password=YOUR_KEYSTORE_PASSWORD \
  -Pandroid.injected.signing.key.alias=YOUR_KEY_ALIAS \
  -Pandroid.injected.signing.key.password=YOUR_KEY_PASSWORD
```

Or configure signing in your local.properties file (not committed to git):

```properties
KEYSTORE_FILE=/path/to/your-release-keystore.jks
KEYSTORE_PASSWORD=your_keystore_password
KEY_ALIAS=your_key_alias
KEY_PASSWORD=your_key_password
```

## Build Artifacts

- **Debug builds**: Available for 7 days
- **Release unsigned builds**: Available for 14 days
- **Signed release builds**: Available for 30 days
- **Tagged releases**: APK automatically attached to GitHub release
