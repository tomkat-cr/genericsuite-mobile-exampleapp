# genericsuite-mobile-exampleapp

<img 
    align="right"
    width="100"
    height="100"
    src="https://genericsuite.carlosjramirez.com/images/gs_logo_circle.svg"
    title="GenericSuite logo by Carlos J. Ramirez"
/>

**GenericSuite Mobile ExampleApp** is a ready-to-publish Flutter project template that demonstrates how to build Android and iOS apps with the [GenericSuite Mobile](https://github.com/tomkat-cr/genericsuite-mobile) library: JSON-driven CRUD editor, menu generator, JWT-aware authentication, and a customizable Material theme.

You can view the [Source Code here](https://github.com/tomkat-cr/genericsuite-mobile-exampleapp).

## Directory Structure

```
genericsuite-mobile-exampleapp/
└── flutter_project_template/     # The Flutter app (package name: gsexampleapp)
    ├── lib/                      # App entry point and screens
    ├── test/                     # Widget/unit tests
    ├── assets/                   # App icon source and other assets
    ├── android/ ios/ web/        # Platform folders
    ├── macos/ windows/ linux/    #   (standard Flutter targets)
    └── Makefile                  # Dev/build/deploy commands
```

## Prerequisites

- [Git](https://www.atlassian.com/git/tutorials/install-git)
- [Flutter](https://docs.flutter.dev/get-started/install) 3.10+ (SDK `^3.5.4`)
- [Make](https://formulae.brew.sh/formula/make): [Mac](https://formulae.brew.sh/formula/make) | [Windows](https://stackoverflow.com/questions/32127524/how-to-install-and-use-make-in-windows)
- An Android/iOS simulator or physical device

### Sibling dependency

`flutter_project_template/pubspec.yaml` pulls the `genericsuite` package as a **git dependency pointing at a relative local path**:

```yaml
genericsuite:
  git:
    url: https://github.com/tomkat-cr/genericsuite-mobile.git
    ref: develop
    path: genericsuite_flutter
```

This only resolves when this repo is checked out as a sibling of `genericsuite-mobile` inside the `genericsuite` superproject's `packages/` directory (i.e. `packages/genericsuite-mobile-exampleapp` and `packages/genericsuite-mobile` both present). Running `flutter pub get` outside that layout will fail to resolve the dependency.

## Installation

```bash
git clone https://github.com/tomkat-cr/genericsuite-mobile-exampleapp.git
cd genericsuite-mobile-exampleapp/flutter_project_template
make install
```

## Running the application

```bash
# Run on the default connected device/simulator
make run

# Run in a Chrome browser
make run_web
```

## Building

```bash
# Debug APK
make build_local

# Release APK
make build

# Release .aab bundle + zipped debug symbols, for Play Store
make build_bundle
```

## Testing

```bash
make test
# or
make qa

# Run a single test file
flutter test test/widget_test.dart
```

## Other commands

```bash
make update            # flutter pub upgrade
make generate_icons    # regenerate launcher icons from assets/images/app_logo_circle.png
make clean             # pub cache clean + rm build/ + rm .dart_tool + rm logs/ + flutter clean
make fresh             # clean + install
```

## Development

### Starting a New Project from This Template

* Clone this repository.

* Make a copy of all your code, especially the `ios` directory. **PLEASE DON'T SKIP THIS STEP**, otherwise if you get the error mentioned in the Troubleshooting section, you won't be able to run the app in an iOS device simulator.

* Copy the `flutter_project_template` directory to your desired location, e.g. `~/Documents/FlutterProjects/MyApp`.

* Open the `MyApp` directory in your preferred IDE, e.g. Visual Studio Code, Cursor, Antigravity, Android Studio, etc.

*  Search and replace globally the following strings (case sensitive, complete words):
    - `com.genericsuite.gsexampleapp` with your app package domain and app name (use short name for the app name, e.g. `com.mydomain.myapp`)
    - `com.genericsuite` with your app package domain (e.g. `com.mydomain`)
    - `gsexampleapp` with your app name in all lowercase (e.g. `myapp`)
    - `GS ExampleApp` with your app short name (capitalized, short because it's the name in the SmartPhone launch screen, e.g. `My App Short Name`)
    - `GenericSuite Mobile Example App` with your app long name (capitalized, e.g. `My App Long Name`)
    - `genericsuite-mobile-example-app` with your app name (capitalized, e.g. `my-app`)

* Rename the following files:
    - `gsexampleapp.iml` to `myapp.iml`
    - `android/gsexampleapp_android.iml` to `android/myapp_android.iml`
    - `android/app/src/main/kotlin/com/genericsuite/gsexampleapp` to `android/app/src/main/kotlin/com/mydomain/myapp`

* Install the dependencies:
    ```bash
    flutter pub get
    ```

* Replace the following directory/files with your own versions:
    - `assets/images/app_logo_circle.png`

* Generate app icons (to let the app have a custom icon with its logo):
    ```bash
    make generate_icons
    ```

### Publishing to Google Play Store

#### Keystore

* If you don't have a keystore, generate one using the `make generate_keystore` command:
    ```bash
    make generate_keystore
    ```
    Notes:
    - It will ask you for the keystore password, key password and key alias. Write them down in a safe place, you will need them for the next step and for signing your app.

* Create a file named `android/key.properties` that contains a reference to your keystore:
    ```properties
    storePassword=<password-from-previous-step>
    keyPassword=<password-from-previous-step>
    keyAlias=upload
    storeFile=<keystore-file-location>
    ```
    Notes:

    - Don't include the angle brackets (`< >`). They indicate that the text serves as a placeholder for your values.

    - The `storeFile` might be located at the following paths (replace `<user name>` with your actual username):

    1. MacOS: `/Users/<user name>/upload-keystore.jks`
    2. Windows: `C:\\Users\\<user name>\\upload-keystore.jks`

    - The Windows path to `keystore.jks` must be specified with double backslashes: `\\`.

    - The MacOS path to `keystore.jks` must be specified with a single forward slash: `/`.

    - Check [Build and release an Android app](https://docs.flutter.dev/deployment/android) for more information.

    Warning:
    - Keep the `key.properties` file private; don't check it into public source control.

#### Setting the version number

* The version number is the one in the `pubspec.yaml` file. If you want to change it, you can do it by editing the `version` field. For example:
    ```yaml
    version: 1.0.0+1
    ```


#### Building the bundle

* Build the bundle (it's a Google Play Store requirement):
    ```bash
    make build_bundle
    ```

#### Logging in to the Google Play Console

* Go to the Google Play Console: [https://play.google.com/console](https://play.google.com/console)

* Login with your Google Account and choose developer account

* If the app is not listed, you need to add it clicking on "Create app"

* Click on the app name to open the app dashboard

#### Creating a new release for internal testing

* Click on "Test and release" > "Testing" > "Internal testing"

* Click on "Create New Release"

* Upload the bundle file (created by the `make build_bundle` command and available in the `build/app/outputs/bundle/release/` directory as `app-release.aab`)

* Follow the instructions to create the release

* In the "Internal testing" section, you will see the release you just created

* Click on "Edit Release"

* Scroll down. In the "Release details" section, enter the "Release name" with the version number and the "Release notes" with the changelog entries for the version. The version number is the one in the `pubspec.yaml` file. The changelog entries are the ones in the `CHANGELOG.md` file.

* Click on "Next"

* Click on "Save and Publish"

## Troubleshooting

If you get the following error running `main.dart` in an iOS device simulator, even after installing CocoaPods:

```
Warning: CocoaPods not installed. Skipping pod install.
  CocoaPods is a package manager for iOS or macOS platform code.
  Without CocoaPods, plugins will not work on iOS or macOS.
  For more info, see https://flutter.dev/to/platform-plugins

For installation instructions, see https://guides.cocoapods.org/using/getting-started.html#installation

CocoaPods not installed or not in valid state.
```

1. Delete the `ios` directory.
2. Restore the `ios` directory from your original code.
3. Run:
   ```bash
   make clean_ios
   ```

## GenericSuite Mobile library

Check [GenericSuite Mobile](https://github.com/tomkat-cr/genericsuite-mobile) for the underlying library's architecture (JSON-driven CRUD, `AppCallablesSuper`, `CreateGsApp`, GetIt DI, auth flow).

## Documentation

- Main: [https://genericsuite.carlosjramirez.com](https://genericsuite.carlosjramirez.com)
- Mirror: [https://genericsuite.readthedocs.io](https://genericsuite.readthedocs.io)

## License

[GenericSuite](https://genericsuite.carlosjramirez.com) is open-sourced software licensed under the [MIT license](./LICENSE).

## Credits

This project is developed and maintained by [Carlos J. Ramirez](https://carlosjramirez.com). For more information or to contribute to the GenericSuite project, visit [GenericSuite on GitHub](https://github.com/tomkat-cr/genericsuite-mobile-exampleapp).

Happy Coding!
