# PermissionFit

An Android sandbox for **granting and then revoking permissions** — specifically the Google Fit
case, where an app holds both an Android runtime permission and an OAuth grant, and the two are
revoked by different APIs that fail in different ways.

## What it does

- Requests `ACTIVITY_RECOGNITION` at runtime and reads step counts from the Google Fit History API
  (`TYPE_STEP_COUNT_DELTA` and its aggregate).
- Signs in through Google Sign-In with Fitness scopes, so the OAuth grant is separate from the
  runtime permission.
- Revokes each of them independently — `revokeSelfPermissionOnKill(ACTIVITY_RECOGNITION)` for the
  Android side, `GoogleSignInClient.revokeAccess()` for the OAuth side — and writes the outcome of
  each call into an on-screen log list rather than only Logcat.

The log records `isSuccessful`, `isComplete`, `result` and `exception` per attempt, because
`revokeAccess()` can complete without succeeding, and that difference is invisible from the UI
otherwise.

## Why

Revoking is the path that gets tested least. A permission the user turns off in Settings, an OAuth
scope revoked server-side, and `revokeSelfPermissionOnKill` — which only takes effect once the
process is killed — leave the app in three different states, and it has to keep working in each.

`Kotlin` · `Google Fit` · `Google Sign-In` · `Runtime permissions` · `Data binding`
