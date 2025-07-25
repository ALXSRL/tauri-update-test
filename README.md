# tauri-update-test

Test repo for updating a tauri app using github actions

## Instructions to make the Tauri app updateable with GitHub Actions

### Part 1: Setting up the Tauri Updater Plugin

First, follow the instructions at [https://v2.tauri.app/fr/plugin/updater/](https://v2.tauri.app/fr/plugin/updater/) to set up the updater plugin.

...

I've set up the `.env` file with the following variables:

```sh
TAURI_SIGNING_PRIVATE_KEY_PASSWORD=<password_entered_when_creating_the_key>
TAURI_SIGNING_PRIVATE_KEY=<absolute_path_to_your_private_key_file|content_of_your_private_key>
# Here I have set the key to be in the `src-tauri/.tauri` folder
```

Don't forget to put your `.env` and `.pem` files in your `.gitignore` file to avoid committing them to the repository.

Now each time to run the `tauri build` command, the app will be signed with the private key in the active environment (.env file doesn't count).

For this I have made the custom `load_keys.ps1` script that loads the keys from the `.env` file and sets them as environment variables.

... follow the rest of the instructions from the Tauri documentation ...

### Part 2: Setting up GitHub Actions

I followed the instructions at [https://github.com/tauri-apps/tauri-action](https://github.com/tauri-apps/tauri-action).

In `.github/workflows/release.yml`, I have set up the workflow to build the app for all platforms and create a release on GitHub. It is inspired from the official example on the tauri-action repository, but modified to only handle Windows, and specified the app folder as `tauri-update-test-app`. The release is triggered on pushes to the `release` branch.

### Part 3: Creating a Release

Required setup : 
- Make sure you have Created "Action Secrets" in your GitHub repository with the following keys:
  - `TAURI_SIGNING_PRIVATE_KEY` -> the content of your private key file
  - `TAURI_SIGNING_PRIVATE_KEY_PASSWORD` -> the password you used when creating the private key.

1. Make your changes in the main / development branch.
2. Don't forget to update the version everywhere in the app. (A script is recommended to automate this.)
3. Merge to the `release` branch.
4. Push the changes to the `release` branch.
5. GitHub Actions will automatically build the app for all platforms and create a release with the built artifacts. (check the Actions tab in your repository to monitor the build progress, and check if any errors occurred during the build.)

On your release page you will find something like this:

![](images/2025-07-25-16-57-31.png)

Make sure the latest.json file and the packaged app files (at least the one you wanted to build) are present in the release.
The `latest.json` file is crucial for the updater to work, as it contains the information about the latest version of the app.

Here is an example of what the `latest.json` file should look like:

```json
{
  "version": "0.1.0",
  "notes": "See the assets to download this version and install.",
  "pub_date": "2025-07-25T14:54:55.635Z",
  "platforms": {
    "windows-x86_64": {
      "signature": "<signature_of_the_app>",
      "url": "https://github.com/ALXSRL/tauri-update-test/releases/download/app-v0.1.0/tauri-update-test-app_0.1.0_x64_en-US.msi"
    }
  }
}
```

Then you can test the update process by launching a previous version of the tauri app and checking if it updates to the latest version. (or just going in dev mode but setting an older version in the `tauri.conf.json` file).

