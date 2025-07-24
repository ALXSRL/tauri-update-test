# tauri-update-test

Test repo for updating a tauri app using github actions

## Instructions to make the Tauri app updateable with GitHub Actions

### Part 1: Setting up the Tauri Updater Plugin

First, follow the instructions at [https://v2.tauri.app/fr/plugin/updater/](https://v2.tauri.app/fr/plugin/updater/) to set up the updater plugin.

...

I've set up the `.env` file with the following variables:

```sh
TAURI_SIGNING_PRIVATE_KEY_PASSWORD=<password_entered_when_creating_the_key>
TAURI_SIGNING_PRIVATE_KEY=<absolute_path_to_your_private_key_file>
# Here I have set the key to be in the `src-tauri/.tauri` folder
```

Now each time to run the `tauri build` command, the app will be signed with the private key in the active environment (.env file doesn't count).

For this I have made the custom `load_keys.ps1` script that loads the keys from the `.env` file and sets them as environment variables.

... follow the rest of the instructions from the Tauri documentation ...

### Part 2: Setting up GitHub Actions

I followed the instructions at [https://github.com/tauri-apps/tauri-action](https://github.com/tauri-apps/tauri-action).

In `.github/workflows/release.yml`, I have set up the workflow to build the app for all platforms and create a release on GitHub. It is inspired from the official example on the tauri-action repository, but modified to only handle Windows, and specified the app folder as `tauri-update-test-app`. The release is triggered on pushes to the `release` branch.

### Part 3: Creating a Release

1. Make your changes in the main / development branch.
2. Don't forget to update the version everywhere in the app. (A script is recommended to automate this.)
3. Merge to the `release` branch.
4. Push the changes to the `release` branch.
5. GitHub Actions will automatically build the app for all platforms and create a release with the built artifacts. (check the Actions tab in your repository to monitor the build progress, and check if any errors occurred during the build.)

Then you can test the update process by launching a previous version of the tauri app and checking if it updates to the latest version. (or just going in dev mode but setting an older version in the `tauri.conf.json` file).