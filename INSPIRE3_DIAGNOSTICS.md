# Inspire 3 / RC Plus diagnostic build

This branch keeps `app/src/main/assets/profiles/fcc.json` unchanged from FreeFCC v1.5.5.
The manual Enable FCC button writes the same 21 commands in two rounds. Auto-FCC and
boot activation are disabled in this build; keep the keepalive switch off during
the first test.

## Build and install

On the `inspire3-diagnostics` branch, open GitHub **Actions → build**. Download
the `freefcc-inspire3-diagnostics-apk` artifact from the successful branch run,
unzip it, then install `app-debug.apk` with `adb install -r app-debug.apk`.

The debug application ID is `com.freefcc.app.diagnostics`, so it can be
installed alongside an existing release-signed FreeFCC app. It has separate
preferences and an icon labeled **FreeFCC Diagnostics**.

## Capture one manual run

With the RC Plus and Inspire 3 linked and the propellers removed, connect ADB.
In one terminal start a local log capture:

```sh
adb logcat -s FreeFCC-Diag:I '*:S' > inspire3-fcc-diagnostics.txt
```

On the controller open **FreeFCC Diagnostics** and tap **Check DUML Proxy**.
This checks the controller's local service only; it cannot confirm the aircraft
link. Confirm that DJI Pilot 2 shows the live aircraft before tapping
**Enable FCC Mode** once. Stop logcat with Ctrl-C. The log contains BEGIN/END
markers and one line per send: round, frame index, port, outcome, raw TX/RX
hexadecimal, and any matching response payload. The app's Log tab also shows
a shorter summary of each frame.

`VALID_MATCHING_REPLY` means DUML framing, CRC, routing, sequence number,
command set, and command ID match the sent frame. It does **not** establish
that the command was accepted by the radio or that RF power/region changed.
`NO_REPLY` and `PARTIAL_REPLY` can also result from the original profile's
short receive timeout. The summary distinguishes successful socket writes
from matching replies.

Keep the diagnostic log private if it contains information about your controller.
