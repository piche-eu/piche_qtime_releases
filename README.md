# QTIME - Releases

The public repository, responsible for distribution artifacts for auto-update purposes to users.

# Step 1 - Installation 
To create a release package, you will need to use Fastforge. To install it, run this command

```
dart pub global activate fastforge
```

For Windows, also install openssl using choco [(you need administrator privileges for this)](https://www.howtogeek.com/194041/how-to-open-the-command-prompt-as-administrator-in-windows-10/):

```
choco install openssl
```

If choco not installed, [here is the instruction](https://chocolatey.org/install).

# Step 2 - Generating Keys
To let our updater know that a downloaded update is not corrupted and came from us (instead of a malicious attacker), we recommend signing each release with a signature.

## MacOS

To create a key signature, run the following command:
```
dart run auto_updater:generate_keys
```

It will generate a private key and save it in your login Keychain on your Mac. You don’t need to do anything with it, but do keep it safe.

Output:

```
A key has been generated and saved in your keychain. Add the `SUPublicEDKey` key to
the Info.plist of each app for which you intend to use Sparkle for distributing
updates. It should appear like this:

    <key>SUPublicEDKey</key>
    <string>pfIShU4dEXqPd5ObYNfDBiQWcXozk7estwzTnF9BamQ=</string>
```
It will print your public key to embed into applications. Copy that key (it’s a base64-encoded string) and insert it into Info.plist.

## Windows 
To ensure smooth development, request the generated keys from @oleksiikunakov-flutter and put them both at `windows\runner\resources` folder (private and public)

# Step 3 - Version check
Before proceeding with build generation, ensure:

- pubspec.yaml contains correct version.
- if building for Windows - installer_ff.iss contains correct version.

# Step 4 - Build release package
You do not need to run `fvm flutter build` or preemptively clean the project - by default Fastforge does it automatically.
The targets and specifics of the release build are described inside of `/distribute_options.yaml` within jobs descriptions.

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

# Step 5 - Get signature
To make sure, that updater understands, when newly posted build is an update, we need to get signature of that build. Here is how to do this for both platforms. We will need this signature later.

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

# Step 6 - Publish

After creating release artifacts (.exe or .zip respectively) - publish them in "Release" section of this repository with appropriate tag and version.
If there is no release with your's build number - create one yourself. If there is already - add your build to binaries of this tag.

# Step 7 - Update appcast

auto_updater uses appcasts to get information about software updates. An appcast is an RSS feed with some extra information for updating purposes

After receiving signature for a build, open appcast.xml and either add item or update existing one with your data - that includes:

- version,
- signature,
- length
- additional information, such as release notes, minimum versions, etc.

# Step 8 - Testing out

Use an older version of your app, or if you don’t have one yet, make one seem older by editing pubspec.yaml and running debug to test out the functionality of the updater.

# Additional information

- https://pub.dev/packages/auto_updater
- https://fastforge.dev/getting-started
- https://sparkle-project.org/documentation/
- https://github.com/vslavik/winsparkle/wiki
