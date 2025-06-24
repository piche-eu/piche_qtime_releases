# QTIME - Releases

The public repository, responsible for distribution artifacts for auto-update purposes to users.

# Installation 
To install Fastforge globally

```
dart pub global activate fastforge
```

For Windows, also install openssl:

```
choco install openssl
```

# Generating Keys

## MacOS

Run the following command:
```
dart run auto_updater:generate_keys
```

Output:

```
A key has been generated and saved in your keychain. Add the `SUPublicEDKey` key to
the Info.plist of each app for which you intend to use Sparkle for distributing
updates. It should appear like this:

    <key>SUPublicEDKey</key>
    <string>pfIShU4dEXqPd5ObYNfDBiQWcXozk7estwzTnF9BamQ=</string>
```
Copy the results of the command into Info.plist as is. 

## Windows 

Run the following command:
```
dart run auto_updater:generate_keys
```

Output: 

```
Generated two files:
dsa_priv.pem: your private key. Keep it secret and don't share it!
dsa_pub.pem: public counterpart to include in youe app.
BACK UP YOUR PRIVATE KEY AND KEEP IT SAFE!
If you lose it, your users will be unable to upgrade!
```

Save those keys to a `windows/resources` and ensure that private one never gets published to main repository.

Check if the windows/Runner.rc looks like this:

```
/////////////////////////////////////////////////////////////////////////////
//
// WinSparkle
//

// And verify signature using DSA public key:
DSAPub      "resources\\dsa_pub.pem"
```

# Windows - Before proceeding
In the main repository, open windows/packaging/exe/installer_ff.iss and update `#define MyAppVersion` to a correct version

# Usage
To run Fastforge for all releases and jobs:

```
fastforge release
```

To build specific release:

```
fastforge release --name <NAME>
```

To run specific job of release:

```
fastforge release --name <NAME> --job <JOB_NAME>
```

To prevent `fvm flutter clean` before building release, add `--[no-]skip-clean` at the end of command.

After creating release artifacts (.exe or .zip respectively) - publish them in "Release" section of this repository with appropriate tag and version.

# Get signature

## MacOS
To get signature of newly created build, use this command: 

```
fvm dart run auto_updater:sign_update dist/<your>/<zip_file>
```

Output:
```
sparkle:edSignature="pbdyPt92pnPkzLfQ7BhS9hbjcV9/ndkzSIlWjFQIUMcaCNbAFO2fzl0tISMNJApG2POTkZY0/kJQ2yZYOSVgAA==" length="13400992"
```

## Windows
To get signature of newly created build, use this command: 

```
fvm dart run auto_updater:sign_update dist/<built_version>/QTimeInstaller.exe
```

Output:
```
sparkle:dsaSignature="MEUCIQCVbVzVID7H3aUzAY5znpi+ySZKznkukV8whlMFzKh66AIgREUGOmvavlcg6hwAwkb2o4IqVE/D56ipIBshIqCH8rk=" length="13400992"
```

Note: variable "length" in resulting output could be = 0, it is not a problem.

# Update appcast

After receiving signature for a build, open appcast.xml and either add item or update existing one with your data - that includes version, signature, length and others. Commit to a separate branch for testing purposes and verify, that app downloads and install new updates. 

# Additional information

- https://pub.dev/packages/auto_updater
- https://fastforge.dev/getting-started
- https://sparkle-project.org/documentation/
- https://github.com/vslavik/winsparkle/wiki
