# Wedding Photo Challenge

Guest page for Kevin & Godley's wedding. A guest enters their name, opens a challenge, takes or chooses photos, and uploads them.

## Open it

Open `index.html` in a browser, or serve this folder and visit the local address:

```bash
python3 -m http.server 8080
```

Then go to [http://localhost:8080](http://localhost:8080).

## What was matched

This is the same page as the live Apps Script app: layout, copy, challenge list, photo limits, resizing, upload retries, and offline behavior.

Uploads use that app's `google.script.run.uploadPhoto` call, with the same payload, so photos still go to the same Apps Script when this page is running inside it. Google injects `google.script.run` only on that hosted web app. Opened on its own, this page still queues, retries, and shows the offline state; a completed save happens once that bridge is present.

There is no separate backend in this repo. The live script does not expose an HTTP `doPost` for uploads.

### Challenges, in order

1. **Capture a moment at the Church** — A photo during the wedding ceremony
2. **Capture a moment at the Venue** — A photo of the reception or venue
3. **Group Photo with Friends** — A group picture with your friends
4. **Photo with the Bride and Groom** — A photo with the newlyweds
5. **Fun / Candid Moment** — A fun or memorable candid moment from the wedding

### Limits, resize, retry, and offline

- Up to 10 photos can be staged at once. After Upload, more can be added. Extra files past 10 are left out, with a message to upload and then add the rest.
- The name is trimmed, extra spaces are collapsed, and it is cut to 40 characters. An empty name stays on the first step.
- Each photo is drawn on a white canvas and scaled so the long side is at most 1920 pixels. Smaller photos are not enlarged. The result is a JPEG starting at quality 0.82. If it is still over 1,228,800 bytes, quality drops by 0.1 until it fits or quality is no longer above 0.5.
- If the photo cannot be decoded, the original file is kept only when it is jpeg, png, webp, heic, or heif and at most 5 MB. Otherwise it is removed and an error is shown.
- Two uploads run at a time. Each request times out after 90 seconds.
- A failed send is tried up to 4 times. The waits are about 2s, then 4s, then 8s (never more than 30s), plus up to a second of random delay. The thumb says it is retrying. After the last try it shows Retry. Two or more failures also show “Retry failed photos”.
- Offline, a banner is shown and queued photos wait. Back online, sending continues. Closing the page while a send is in progress asks you to confirm.
