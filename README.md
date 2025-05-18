# Firebase Cloud Messaging (FCM) Demo

A Flutter application demonstrating Firebase Cloud Messaging integration for push notifications with custom notification handling.

## Demo Video  
[Link](https://drive.google.com/file/d/19ONF5ZFBC9_hwxzRL4czzGC7UHCWgLAa/view?usp=sharing)
## Setup Instructions  

### 1. Firebase Integration

1. **Create a Firebase Project**:
   - Go to [Firebase Console](https://console.firebase.google.com/)
   - Create a new project or use an existing one
   - Register your app (Android) with Firebase

2. **Download Configuration Files**:
   - For Android: Download `google-services.json` and place it in `android/app/`

3. **Update Android Configuration**:
   - Ensure the `com.google.gms.google-services` plugin is applied in your app-level `build.gradle.kts`
   - Verify Firebase dependencies are added

4. **Run the App**:
   - Execute `flutter run` to build and run the app
   - The app will generate and print a unique FCM token to the console
   - Copy this token for sending test notifications

### 2. Sending Push Notifications via REST API

To send push notifications, you'll need to:

1. **Generate Service Account Key**:
   - Go to Firebase Console > Project Settings > Service accounts
   - Generate new private key
   - Save the JSON file as `gcloud.json` in your project root

2. **Authenticate with Google Cloud Console SDK Shell**:
   ```bash
   gcloud auth activate-service-account --key-file=<your-path-to>/mad_fcm/gcloud.json
   ```

3. **Get Access Token**:
   ```bash
   gcloud auth print-access-token
   ```
   
4. **Send POST Request to FCM**:
   - Endpoint: `https://fcm.googleapis.com/v1/projects/flutter-fcm-79d4d/messages:send`
   - Headers:
     ```
     Accept: */*
     User-Agent: Thunder Client (https://www.thunderclient.com)
     Content-Type: application/json
     Authorization: Bearer <Access Token from the previous step>
     ```
   - Body:
     ```json
     {
       "message": {
         "token": "<DEVICE_TOKEN>",
         "notification": {
           "title": "Test Title",
           "body": "Test Message"
         },
         "data": {
           "redirect": "product",
           "message": "Test data"
         }
       }
     }
     ```

   > **Note**: Replace `<DEVICE_TOKEN>` with the FCM token printed in the console when running the app. This token will be different for every new build/compile.

### 3. Testing Special Notifications

To test the special warning notification, include "CAUTION" in the notification body:

```json
{
  "message": {
    "token": "<DEVICE_TOKEN>",
    "notification": {
      "title": "CAUTION",
      "body": "CAUTION: This is a warning message!"
    },
    "data": {
      "redirect": "product",
      "message": "CAUTION"
    }
  }
}
```

## Important Notes

1. The FCM token is unique to each app compile

2. The access token from gcloud is temporary (usually 1 hour) and will expire. You'll need to run the `gcloud auth print-access-token` command again to get a new token when it expires.

3. After first login with `gcloud auth activate-service-account`, you typically won't need to reference the JSON file again unless you're switching accounts or credentials.

## Troubleshooting

- **UNREGISTERED Error**: If you receive an "UNREGISTERED" error, it means the FCM token is no longer valid. Run the app again to get a new token.
- **Authentication Errors**: If you receive a 401 error, your access token has likely expired. Generate a new one.
