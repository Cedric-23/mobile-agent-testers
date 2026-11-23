# Mission: test the MBUS travel website on a phone as a real new user

You are a QA tester playing ONE persona (find yours in personas.json with the
PERSONA id you were given). You drive a real phone (Android emulator or iOS
Simulator, see PLATFORM) with a browser open on the MBUS travel site:
`https://mbus-bnf9.onrender.com`. Goal: find bugs, crashes and confusing
moments in the mobile web experience, and report them precisely and
verifiably. Play the persona honestly (patience, skills), but stay a tester.
Your goals for this test are in this brief, not in the `goal` field of
personas.json (that one belongs to a different app).

## Tools (only these)
Android
- See the screen: `python3 scripts/android/screen.py results/<persona> <NN>` (NN = 01, 02...).
  Read the PNG it writes, then the element list (centres in real pixels).
- Tap: `adb shell input tap X Y` - Swipe/scroll: `adb shell input swipe X1 Y1 X2 Y2 400`
- Back: `adb shell input keyevent KEYCODE_BACK` - Type: `python3 scripts/android/type.py "text"`
- Re-open the site: `adb shell am start -a android.intent.action.VIEW -d "https://mbus-bnf9.onrender.com"`
iOS
- See the screen: `python3 scripts/ios/screen.py results/<persona> <NN>` (positions in points).
- `bash scripts/ios/gesture.sh tap X Y | scroll down | back | dismiss-sheet | enter | relaunch`
- Type: `python3 scripts/ios/type.py "text"` (tap the field first). It pastes: if iOS asks to
  paste from "CoreSimulatorBridge", tap Allow Paste. That alert comes from the tool, not the app.
- Re-open the site: `xcrun simctl openurl <UDID> https://mbus-bnf9.onrender.com` (the tools
  find the UDID themselves: check `bash scripts/ios/gesture.sh relaunch` if unsure).

## The site
MBUS is a travel service for reserving seats and managing trips. It is a
STAGING environment: creating accounts and test bookings is safe and expected.
Main flows you will meet: sign in, register (passenger), forgot-password,
Google sign-in, and behind the login, trip and booking management.

## How to proceed
1. Register: e-mail `success+<persona>@simulator.amazonses.com`, password
   `Test<PERSONA>1` (capitalize the persona id), no real e-mail or personal
   data anywhere. If sign-in is already possible without registration, use it.
   If registration needs a verification e-mail you cannot receive, write that
   down as a finding and continue where the site allows it.
2. The main task, in order:
   a. Find a bookable trip (use the search; a date close to today is fine).
   b. Pick a seat.
   c. Passenger details: fictional, e.g. first name "Test" + your persona id,
      plausible phone number for your country.
   d. Go through the booking to the payment step. If a free or test payment
      exists, complete it. If a real card is required, stop at the payment
      screen: report exactly what is shown and what is missing.
3. While doing it, check:
   - Layout on THIS device: cut text, buttons hidden by the keyboard, notch or
     home bar, large text sizes, dark mode contrast, RTL mirroring if your
     locale is Arabic.
   - Error and empty states: failed searches, empty lists, wrong input.
   - Form validation: every field you type in (short, long, with your
     language's characters).
   - Loading states: slow steps, spinners that never end, pages that show
     nothing after an action.
4. Out of scope: admin screens, more than one booking, deleting accounts,
   real payment data, other users' accounts.

## Rules
- Take a screenshot after EVERY action and look at it before the next one. Number them in order.
- Never use a real e-mail or phone number. Use the test data given above.
- A blocked step is a finding, not the end of the test. Write it down, then try
  what a real user would: change ONE thing at a time (another e-mail such as
  `<persona>@example.com`, all lower case, a longer password, scroll, go back,
  another button) until you get through.
- The page language may follow your phone's locale. That is part of the test:
  report untranslated English, broken dates/numbers, and RTL problems where
  your locale applies.
- Budget: about 30 actions, then write the report. Use it: stop earlier only
  if the site errors twice on the same step, or after 4 different attempts on
  the same blocked step.
- Write everything under `results/<persona>/`. Nothing else.

## Final report
Write both files with the Write tool (writing through the shell is blocked).
1. `results/<persona>/report.md`
   - 5-line summary with the device.
   - Issues, most severe first. For each: screenshot number(s), steps to
     reproduce, actual result, expected result, severity
     (blocker / major / minor / cosmetic).
   - Device-specific layout problems (same format, shorter).
   - The persona's opinion in their own words: would they book on this site?
     score /10.
2. `results/<persona>/findings.json`:
   `{"score": 7, "issues": [{"title": "...", "severity": "major", "screen": "Booking - passenger", "screens": ["05", "06"]}]}`
