# Viora — Android YouTube Companion

Viora is a small Android companion app for discovering public YouTube videos, saving a personal on-device collection, and viewing a signed-in YouTube channel's subscription list.

It is intentionally **not** a YouTube clone and does not block or alter ads. Video playback is handed to the official YouTube app when installed (otherwise Android opens the normal web link).

## Included in this starter

- Modern Turkish dark UI built with Jetpack Compose.
- Public video search through YouTube Data API v3.
- Save/remove videos in a local collection.
- Google authorization using the official Play services authorization flow and the read-only `youtube.readonly` scope.
- Load the connected channel and up to 50 subscriptions.
- Disconnect account access from the app.
- GitHub Actions workflow that builds a debug APK and uploads it as an artifact.

The Google access token is kept in memory for this starter and is not written to local storage.

## 1. Create the YouTube Data API key

1. Create a project in Google Cloud Console.
2. Enable **YouTube Data API v3**.
3. Create an API key and restrict it to YouTube Data API v3.
4. For local builds, add this line to your user-level Gradle properties (`~/.gradle/gradle.properties`):

   ```properties
   YOUTUBE_API_KEY=YOUR_API_KEY
   ```

5. For GitHub Actions, add a repository Actions secret named `YOUTUBE_API_KEY` with the same key.

Do not commit a real key into this repository.

## 2. Configure Google account authorization

1. In the same Cloud project, configure the OAuth consent screen.
2. Create an OAuth client of type **Android**.
3. Use this exact package name: `com.viora.app`.
4. Register the SHA-1 certificate fingerprint for the APK signing key you will install.
   - For a local debug build, Android Studio's Gradle `signingReport` task shows the debug SHA-1.
   - For GitHub-built debug APKs, make sure the SHA-1 registered in Cloud Console matches the signing certificate used for the APK you install. A different signing key requires its own matching OAuth Android client entry.
5. Add your Google account as a test user while the consent screen is in testing mode, if Google requires it.

The app requests only `https://www.googleapis.com/auth/youtube.readonly`. It never asks for or stores a Google password.

## 3. Build

Open this folder in Android Studio, allow Gradle sync, then run the `app` configuration on an Android device (Android 6.0+). For a command-line build:

```bash
gradle --no-daemon :app:assembleDebug -PYOUTUBE_API_KEY="YOUR_API_KEY"
```

The debug APK is created at:

`app/build/outputs/apk/debug/app-debug.apk`

## 4. GitHub Actions

Upload the project to a new GitHub repository. Add the `YOUTUBE_API_KEY` Actions secret, then run **Actions → Viora Android Build**. Download the `viora-debug-apk` artifact from the successful workflow.

## Notes

- YouTube API quotas and access are controlled by Google Cloud project settings.
- The current version syncs the channel name and a first page of subscriptions. It does not claim to mirror every YouTube feature or private/system playlists such as Watch Later.
- YouTube's developer policies require API clients to preserve the normal player behavior and ads, and prohibit making a substantially identical YouTube app. Review the current policy before publishing.
