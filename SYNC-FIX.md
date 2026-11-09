# Admin -> User real-time sync fix (v27)

Cause: if Firebase did not load while the admin logged in (slow network / blocked), admin.html silently fell back to a
local password. In that mode every change was saved ONLY in the admin's own browser (still showing "Saved ✓"),
so the user site never received it.

What changed
- admin.html: that silent local fallback is removed; login requires a real Firebase admin session.
- admin.html: green/red badge (bottom-left) shows "Live — synced" or "NOT LIVE" after every save.
- index.html: listeners auto-reconnect, re-check every 20s and on focus/online, plus a REST backup path.

Firebase check: admin.html and index.html use the SAME project (blood-society-3727a) and the SAME Realtime Database.
Make sure _setup/firebase/database.rules.json is published in Firebase console -> Realtime Database -> Rules,
and your admin UID exists at admins/<UID> = true.

## v25: support button logo
The "Live Support" button sometimes lost the society logo after a reload (a live-sync repaint reset it to a plain icon).
Now it is repainted with the logo + text after every page repaint, on load, and when the language changes.

## v26: Vercel deploy error fixed
Error was: The pattern "api/*.js" defined in `functions` doesn't match any Serverless Functions (unused_function).
vercel.json no longer has a `functions` block, so the deploy succeeds whether or not the api folder is uploaded.
api/support.js is already inside this zip. Upload the whole extracted folder (including the api folder) to GitHub.

## v27: sign-up is mandatory
- A new visitor (or anyone opening a shared link) lands on the SIGN-UP page first (was: login page).
- Removed a hard-coded demo login that let anyone in without signing up.
- Sign-up now needs a real Firebase account; if it cannot be created (no internet / blocked) the user is NOT let in.
- Old browser-only accounts can log in only after they link to a real Firebase account.
- Everything stays hidden behind the sign-up/login screen until a session exists (existing behaviour, unchanged).

## v28
- Welcome pop-up now shows once per login session (not on reload); shows again after logout + login.
- database.rules.json updated (extra public keys); firestore.rules stays locked (Firestore is not used).
- IMPORTANT: publish _setup/firebase/database.rules.json in Firebase console -> Realtime Database -> Rules, otherwise Block / Verify fail.

## v29
- Admin: status badge is now a bordered card; tap it to run the Admin <-> User sync check (login, connection, admin UID, save test, block/remove rules, public read).
- Admin: Members filter now has "Receivers only" (receivers + donors are all listed).

## v30+: loading
- Images stay inside index.html exactly as before (no separate image files).
- Added preconnect hints for the Firebase database host.

- v31: database rules: admin is allowed to edit any donor/request record (validate bypass), so Verify works for donors without a login account.

- v33: blue tick: admin Verify now marks every donor record of that member; the tick also shows next to names in the Recent donors list on the home page (donor list + donor page already showed it).

- v34: (1) Admin live-support: "Close chat" button in every chat -> user gets an automatic "chat closed" message, the old chat moves to the user's chat history (clock icon in the chat header), and the suggestions start fresh. (2) Admin can Delete one chat or "Delete all closed chats" from the Live support list. (3) The APK install bar at the top now shows once per visit and not on every reload; the Welcome pop-up has an extra once-per-tab guard. database.rules.json is unchanged (no new publish needed).

- v35: auto logout — if the site is not used for 15 minutes (IDLE_MIN in the first inline script of index.html) the session ends and the sign-in page is shown again; last-activity time is kept in localStorage (rb_last).

- v36: speed. Large images moved out of index.html into the new img/ folder (index.html 1.7 MB -> 0.97 MB, images cached for a year). UPLOAD THE img FOLDER TOGETHER WITH index.html. The render-blocking Google Fonts link was removed (fonts still load, but no longer hold up the first screen). Shared links (share button and donor-card links) are now always the clean site address; anyone without a login session lands on the sign-up/login page first. Open Graph tags added so a shared link shows a proper preview.

- v37: login is Email + Password only (Google, Phone OTP and Anonymous sign-in removed from index.html). Firebase Console -> Authentication -> Sign-in method: keep only Email/Password enabled. Forgot Password now sends a real reset email. A session guard sends the user back to sign-in if there is no real Firebase session. admin.html now uses its own Firebase session (separate from the user site), retries a denied save once after refreshing the token, checks the admin session every 2 minutes and shows the login page if it is lost. After deploying, sign in to /admin once again.

- v37: images are embedded inside index.html again (no img folder needed). Upload the whole folder as before.

- v37b: Live chat: admin list has a "Close" button on every chat; Close deletes the chat for BOTH admin and user (the user's device clears it too and can start a new chat). Users also have a trash button in the chat header to delete their own chat and start fresh. Blue verified badge already shows next to names in Find Blood (needs admin Verify to be saved successfully).
