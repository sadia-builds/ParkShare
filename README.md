# Smart Park 🚗

Smart Park is a mobile app that connects drivers looking for parking with people who have unused parking space (in homes, offices, societies) to share. Drivers can search, book, and pay for a spot. Space owners can list their spot and earn from it.

This README explains **everything a beginner needs** to get the project running on their own laptop. Read it top to bottom once — you won't need to again.

---

## 1. What tech are we using, and why

| Piece | Tool | In plain English |
|---|---|---|
| App (what users see) | **Flutter** | A framework that lets us write one app that runs on both Android and iPhone |
| Backend (where data lives) | **Firebase Firestore** | A database in the cloud — no server to set up ourselves |
| Login | **Firebase Auth** | Handles OTP/phone and email login for us, securely |
| Photos (parking spot images) | **Firebase Storage** | Cloud storage for images |
| Notifications | **Firebase Cloud Messaging (FCM)** | Sends push notifications ("Your booking is confirmed") |
| Server-side logic | **Cloud Functions** | Small pieces of backend code (e.g. "when a booking is made, calculate the price") that run in the cloud, no server to manage |

We picked Flutter + Firebase because none of us need to build or host a separate backend server — Firebase handles the database, login, storage and notifications for us. That means more time on features, less time on infrastructure.

---

## 2. One-time setup (every team member does this once)

### Step 1 — Install Flutter
1. Go to https://docs.flutter.dev/get-started/install and download Flutter for your OS (Windows/Mac/Linux).
2. Unzip it somewhere permanent (not Downloads folder) — e.g. `C:\flutter` or `~/flutter`.
3. Add Flutter to your system PATH (the install guide shows exactly how for your OS).
4. Open a terminal and run:
   ```
   flutter doctor
   ```
   This checks your setup and tells you what's missing (e.g. Android Studio). Fix anything marked with a red ✗ before moving on.

### Step 2 — Install Android Studio (needed even if you plan to test on iPhone later)
- Download from https://developer.android.com/studio
- During setup, make sure the **Android SDK** and an **emulator (virtual phone)** are installed — Android Studio's setup wizard offers this automatically.

### Step 3 — Install VS Code (recommended editor)
- Download from https://code.visualstudio.com
- Install the **Flutter** and **Dart** extensions (search in the Extensions panel, the little squares icon on the left sidebar).

### Step 4 — Install Git
- Download from https://git-scm.com/downloads
- This is the tool that lets us all share code through GitHub. If you've never used Git before, read `CONTRIBUTING.md` in this repo — it explains every command step by step.

### Step 5 — Get the project onto your laptop
```
git clone <your-repo-url>
cd smart-park
flutter pub get
```
`flutter pub get` downloads all the packages/libraries the app depends on.

### Step 6 — Run the app
1. Open Android Studio → open the **Device Manager** → start a virtual phone (emulator).
2. In your terminal, inside the project folder, run:
   ```
   flutter run
   ```
3. The app should build and launch on the emulator. First build can take a few minutes — that's normal.

If you get stuck on any step, don't struggle alone for more than ~20 minutes — post in the group chat. Setup issues are the #1 time-waster for beginner teams.

---

## 3. Firebase setup (one person does this, then shares access)

One team member should:
1. Go to https://console.firebase.google.com → **Add project** → name it `smart-park`.
2. Enable these in the left sidebar:
   - **Authentication** → enable Phone and/or Email sign-in
   - **Firestore Database** → create database (start in **test mode** for now — we'll lock it down with security rules later)
   - **Storage** → create default bucket
3. Go to **Project settings → Your apps → Add app → Flutter**, and follow Firebase's own wizard — it generates a `firebase_options.dart` file for you.
4. Go to **Project settings → Users and permissions → Add member**, and add the other 3 teammates' Google accounts so everyone can see the Firebase console.
5. Commit the generated config files to the repo (Firebase will tell you which ones) so everyone's local app connects to the same project.

---

## 4. How the data is organized (Firestore schema)

Firestore stores data in **collections** (like folders) of **documents** (like individual records). Here's our plan:

```
users/{userId}
  - name, phone, email, role ("driver" | "owner" | "both"), rating

spaces/{spaceId}            (a parking spot listed by an owner)
  - ownerId, title, address, lat, lng, pricePerHour
  - photos: [url1, url2]
  - type: "covered" | "open", vehicleType: "2-wheeler" | "4-wheeler" | "both"
  - availability: { days, startTime, endTime }
  - accessible: true/false   (accessibility filter)

bookings/{bookingId}
  - spaceId, driverId, startTime, endTime, status ("pending"|"confirmed"|"completed"|"cancelled")
  - amount, paymentStatus

reviews/{reviewId}
  - bookingId, fromUserId, toUserId, rating, comment
```

Don't change this structure without telling the whole team — everyone's code depends on these exact field names.

---

## 5. Project folder structure (inside `lib/`)

```
lib/
  main.dart              # app entry point
  models/                # data classes (User, Space, Booking...)
  services/              # Firebase calls live here (e.g. booking_service.dart)
  screens/
    driver/               # search, booking, payment screens
    owner/                 # list space, calendar, earnings screens
    admin/                 # admin dashboard
    auth/                   # login/signup
  widgets/                # reusable UI pieces (buttons, cards)
```

Keeping Firebase calls inside `services/` (not scattered inside screens) makes it much easier for 4 people to work without conflicts.

---

## 6. Team & who owns what

See `CONTRIBUTING.md` for the full workflow, and the GitHub Issues board for the task list. Quick summary:

| Member | Area |
|---|---|
| A | Auth + driver search & booking |
| B | Owner listing, calendar, earnings dashboard |
| C | Payments, maps/navigation, notifications, SOS |
| D | Admin panel, QR check-in, reviews, accessibility filter, Firestore schema owner |

## 7. Timeline
- Week 1: Group setup (this README's steps) + finalize schema together
- Week 2: Each person builds their own module
- Week 3: Connect everything into one working flow
- Week 4: Admin panel, QR, polish
- Week 5: Testing, fixing bugs, demo prep
