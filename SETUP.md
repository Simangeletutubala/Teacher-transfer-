# SA Teacher Transfers: Firebase setup

1. Go to https://console.firebase.google.com and create a project (Google Analytics not needed).
2. **Authentication** > Get started > enable **Email/Password** and **Google**.
3. **Firestore Database** > Create database (production mode). Choose a nearby region (e.g. europe-west or the closest available to South Africa).
4. **Project settings** > Your apps > add a **Web app**. Copy the config into `public/firebase-config.js`.
5. Install tools and deploy (needs Node.js):
   ```
   npm install -g firebase-tools
   firebase login
   firebase use --add        # pick your project
   firebase deploy
   ```
   Your site goes live at https://YOUR_PROJECT_ID.web.app
6. **Make yourself admin** (can remove listings and see reports): sign up on the site, then in Firestore create a collection `admins` with a document whose ID is your user ID (Authentication > Users > copy "User UID"). The document can be empty.
7. Custom domain: Hosting > Add custom domain. Add the domain under Authentication > Settings > Authorized domains.
8. Edit `public/privacy.html` and fill in your name and email before launch.

Netlify instead of Firebase Hosting: drag the `public` folder onto Netlify, then add the Netlify domain under Firebase Authentication > Authorized domains. Firebase still provides logins and the database.

## Messaging
In-site messaging uses Firestore (collection `conversations`), so no extra setup is needed beyond deploying the rules.
If you already deployed an earlier version, update the rules with: `firebase deploy --only firestore:rules` and redeploy hosting with `firebase deploy --only hosting`.
New-message email or push notifications are not included (they need Firebase Cloud Functions on a paid plan). Teachers see a badge on the Messages tab when they visit the site.

## Blocking, reports and moderation
- Users can **Block** or **Report** from inside any conversation. Blocking stops messages both ways and hides that person's listing; the blocked person is not told.
- Reporting a conversation sends the reason plus the last 30 messages to you. You cannot read conversations otherwise.
- Admins get a **Moderation** tab showing open reports with these actions: Remove listing, Ban user, Dismiss report.
- A ban creates a document in the `banned` collection. To lift one, delete that document in the Firestore console.
- **Redeploy the rules**: `firebase deploy --only firestore:rules`, then `firebase deploy --only hosting`.
- Check the Moderation tab regularly. There are no automatic alerts for new reports.
