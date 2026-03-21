# Google Workspace Onboarding (Verified)

When helping a user connect their Google Workspace via AlphaClaw, follow these refined steps in the Google Cloud Console:

1.  **Search Bar First:** Tell the user to use the top search bar for everything—it's the fastest way to find buried menus.
2.  **Create Project:** Search "Projects" -> **New Project**.
3.  **Enable APIs (Search for these individually and click 'Enable'):**
    *   Gmail API
    *   Google Calendar API
    *   Google Drive API
    *   Google Sheets API
4.  **OAuth Consent Screen (Search "OAuth consent screen"):**
    *   **User Type:** You must select **Internal** (if in a Workspace org) or External.
    *   Click **Create** (this is the button often missed!).
    *   Fill in: App name, User support email, Developer contact info.
5.  **Credentials (Search "Credentials"):**
    *   **+ Create Credentials** -> **OAuth client ID**.
    *   **Application type:** Web application.
    *   **Authorized redirect URIs:** `https://5.78.142.81.sslip.io/auth/google/callback`
6.  **Setup UI:** Paste the Client ID and Secret into <https://5.78.142.81.sslip.io#general>.

*Source: Feedback from Josh (Jpm855) on 2026-03-21.*
