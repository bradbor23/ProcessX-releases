# Testing ProcessX

For people trying ProcessX on their own Mac. Everything here takes about twenty minutes. You don't need to be a
developer, and nothing asks you to build anything.

**What we most need to learn:** whether the privileged helper installs and works on a Mac that has never run a
development build of ProcessX. It cannot be tested on the machine ProcessX is built on: macOS remembers which
certificate first approved a background item, and on that Mac the record was made by development builds.

**What ProcessX sends anywhere:** nothing, except checking the update feed on GitHub for a newer version. No
analytics, no crash reporting, no account.

**You need:** macOS 26 or later, and about twenty minutes. Both Apple silicon and Intel Macs are wanted, and
laptops as well as desktops. Please use the latest release.

---

## 1. Download and first launch

1. Open <https://github.com/bradbor23/ProcessX-releases/releases/latest> and download `ProcessX-<version>.dmg`.
2. Open the DMG and drag ProcessX to Applications.
3. Open ProcessX from Applications.

**Expected:** it opens. macOS may say it was downloaded from the internet; Open is enough. You should **not** see
"cannot be opened because the developer cannot be verified", and you should never have to right-click → Open.

**Tell us:** anything macOS said before the app opened, word for word, and whether a guide window appeared.

## 2. The numbers are true

1. Open Activity Monitor next to ProcessX.
2. Compare the CPU and memory of two or three busy processes, and the totals.

**Expected:** the same processes near the top, similar figures. ProcessX shows "—" where macOS won't tell it
something; hover over any "—" and it explains why. It should never show a plausible-looking wrong number.

**Tell us:** anything that disagrees with Activity Monitor by more than a little, and any "—" whose explanation
doesn't make sense.

## 3. The privileged helper (the important one)

On the Processes page, processes belonging to root and other users show "Access denied" until the helper is set up.

1. Settings → Privileged helper → **Install…**
2. Read what it says will happen, then continue.
3. System Settings opens at Login Items & Extensions. Turn ProcessX on under "Allow in the Background".
4. macOS asks for your password or Touch ID.
5. Go back to ProcessX and look at the Processes page.

**Expected:** the card says On, and rows for root processes (`kernel_task`, `mds`, `WindowServer`) fill in with real
memory and disk figures instead of "Access denied".

**Tell us:** whether it reached On, how long it took, and whether root processes filled in. If they didn't, please
also send the output of section 7.

Then try **Settings → Privileged helper → Uninstall…**: the root rows should go back to "Access denied".

## 4. Permissions and login

1. Settings → **Detect apps that aren't responding** → set it up, allow ProcessX in Privacy & Security →
   Accessibility. Force an app to hang if you can; its Status should read "Not responding".
2. Settings → **Open at login** → turn on. Restart your Mac.

**Expected after the restart:** ProcessX is running in the menu bar with no window open. Its App history page
records the time you were logged out, and Startup apps shows an impact rating for items that ran at login.

**Tell us:** whether it started hidden rather than opening a window, and whether Startup apps shows impact ratings
(High/Medium/Low) rather than "—" an hour later.

## 5. Getting around

1. From another app, press **Control + Shift + Escape**. ProcessX should come forward; press it again to hide it.
2. Menu bar icon: it should show live CPU and memory and the busiest apps.
3. Performance page: double-click a graph. It should open a small window that floats above other apps.
4. Right-click rows on Processes and Details; try End task on something harmless, like a text editor you opened.
5. Users page → **Log off** on another account, if you have one. macOS asks for permission to control the login
   window the first time. **Cancel it** — we only need to know the prompt appears.

**Tell us:** anything that did nothing, or did something you didn't expect.

## 6. Battery, temperature and stutter

1. Performance page → Power (laptops) and Thermals.
2. Leave ProcessX open on the Processes page for a few minutes while you work.

**Expected:** battery health close to what System Settings → Battery says; fan speeds and a chip temperature on
Apple silicon; "—" with a reason where your Mac doesn't report something. The window shouldn't stutter while
scrolling or sorting.

**Tell us:** your battery health figure from ProcessX and from System Settings, and whether anything felt jerky.

## 7. If something went wrong

Run this in Terminal and send the output. It prints ProcessX's own log lines from the last hour and the record
macOS keeps of its background items — no personal files, no other apps' data:

```bash
/usr/bin/log show --last 1h --predicate 'subsystem == "com.threegeekssoftware.processx"' --style compact | tail -100
```

```bash
sfltool dumpbtm | grep -A 12 -i processx
```

Screenshots of anything odd help too.

## 8. On an Intel Mac

ProcessX runs on both kinds of Mac, but nobody has yet used it on an Intel one. Several readings come from Apple
silicon-only sources, and those should say so rather than show a wrong number or a zero. If your Mac has an Intel
processor, please check these.

**Should work normally:** the process list and its CPU, memory, disk and network figures; the Performance page's CPU,
memory, disk and network graphs; GPU totals; battery; startup items; services; users.

**Should read "—" with an explanation when you hover:** CPU frequency (GHz) on the Performance page, per-process GPU
on the Processes page, and chip temperature on Thermals.

**Worth a close look:**
1. **Details page → Architecture column.** Your own processes should read **Intel**, not "Apple silicon". Anything
   running under Rosetta reads "Intel (Rosetta)" — on an Intel Mac there should be none.
2. **Performance → CPU.** Right-click the graph → Change graph to → Logical processors. Intel Macs have one kind of
   core, so there are no performance/efficiency labels; the grid should otherwise look right.
3. **Performance → Thermals.** Fan speeds should be real. Chip temperature is expected to be "—" on Intel; tell us if
   it shows a number, since that would mean we can read more than we thought.
4. **Performance → NPU.** Intel Macs have no Neural Engine, so the tile should say so rather than graph zero.

**Tell us:** which Intel Mac and macOS version, what each of the four above showed, and anything that reads as a
number where you'd expect "—", which is the mistake we most want to avoid.

## 9. What to send back

- Mac model, chip (Apple silicon or Intel), macOS version, ProcessX version (ProcessX → About ProcessX).
- For each section above: worked, or what happened instead.
- The two most annoying things you noticed. Small annoyances count.

Report at <https://github.com/bradbor23/ProcessX-releases/issues>, or reply to whoever sent you this link.
Thank you — every report saves a user from meeting the same problem.
