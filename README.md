# GoMotion Video Project

This folder contains one GoMotion composition in index.html. It is a 16:9 motion-graphics video, not a conventional web application.

## Current composition

- Canvas: 1920 x 1080
- Frame rate: 30 FPS
- Length: 10 seconds
- Expected frame count: 300 frames
- Scenes:
  1. HTML in, video out
  2. Write HTML / Animate / Render
  3. 300 frames
- Transition: push-up 0.7s between scenes

The scene durations are 4.0s, 4.4s, and 3.0s. The two transitions overlap the scenes:

    4.0 + 4.4 + 3.0 - 0.7 - 0.7 = 10.0 seconds
    10.0 x 30 FPS = 300 frames

## What needs to be installed

### Required

1. Node.js LTS

   Node.js provides npm and npx. Install it from the official Node.js website if it is not already installed.

2. GoMotion

   GoMotion is downloaded through npx; it does not need to be manually copied into this folder. Internet access is required the first time it is used.

3. Chrome or headless Chrome

   GoMotion can download the rendering browser itself using gomotion install.

4. FFmpeg

   FFmpeg is used when frames are joined and encoded into MP4, WebM, MOV, GIF, or audio output. Run gomotion doctor to see whether it is already available. If it reports that FFmpeg is missing, install FFmpeg and add its bin folder to the Windows PATH.

### Optional but recommended

- Git for version history and safe rollback.
- A modern browser for previewing the HTML composition.
- Enough free disk space for rendered frames, cache files, and the final video.

Python, React, a database, and a web server are not required for this composition.

## Windows setup

Open PowerShell or Command Prompt and run:

    cd C:\Dev\VerityTrail\my-video

    npx.cmd gomotion doctor
    npx.cmd gomotion install

npx.cmd is used instead of npx because some Windows PowerShell configurations block the npx.ps1 script with an execution-policy error.

If Node.js is not installed, verify the installation with:

    node --version
    npm.cmd --version
    npx.cmd --version

## Validate the composition

Run the checker before rendering:

    cd C:\Dev\VerityTrail\my-video
    npx.cmd gomotion check index.html

Fix every error reported by the checker. Warnings should be reviewed individually; clipping, off-screen text, missing assets, and unsupported attributes should be fixed.

## Preview still frames

Create still images at the important beats:

    npx.cmd gomotion still index.html -t 1.6 -o snapshots/title.png
    npx.cmd gomotion still index.html -t 5.0 -o snapshots/cards.png
    npx.cmd gomotion still index.html -t 8.3 -o snapshots/count.png

Create a contact sheet of the whole video:

    npx.cmd gomotion snapshot index.html

Inspect the generated files in snapshots/. Check that:

- No text or card is cut off at the canvas edge.
- The headline is readable at phone size.
- The three cards have comfortable margins.
- The counter is centered and readable.
- The transitions do not show two competing text layers.
- The first frame contains a clear visual hook.

## Review against a brief

Create a brief.md beside index.html so GoMotion can review the important beats. A suitable starting brief is:

    # Video brief

    ## Goal

    Explain the GoMotion workflow in a short, polished motion-graphics video.

    ## Direction

    - Look: dark, technical, clean, and modern.
    - Type: large white headline text with blue-to-teal gradient emphasis.
    - Motion: fast readable entrances, gentle camera movement, and upward scene transitions.
    - Palette: dark navy background, light text, blue and teal accents.
    - Output: 1920x1080, 30 FPS, 10 seconds.

    ## Beats

    - 0.0-3.3: Show “HTML in, video out”. Establish the promise quickly.
    - 3.3-7.0: Show the three steps: write HTML, animate, render.
    - 7.0-10.0: Count from 0 to 300 frames and land on the final message.

    ## Acceptance criteria

    - All text is readable and inside the safe area.
    - The three cards enter one after another.
    - The counter reaches 300 before the video ends.
    - The final frame is held long enough to read.

Then run:

    npx.cmd gomotion review index.html

Review the overview, beat frames, and motion strips. Fix the five most important issues and review again.

## Render the final video

Render an MP4 using:

    npx.cmd gomotion render index.html -o out/video.mp4

For a faster draft while iterating:

    npx.cmd gomotion render index.html -o out/draft.mp4 --quality draft

For a sharper 4K version from the 1080p layout:

    npx.cmd gomotion render index.html -o out/video-4k.mp4 --scale 2

Do not use the 4K render until the normal 1080p render is correct.

## Recommended code improvements

These changes make the composition safer and easier to maintain.

### 1. Make the total duration explicit

Update the opening html tag in index.html:

    <html data-width="1920" data-height="1080" data-fps="30" data-duration="10">

The current duration is correctly inferred from the sequence, but declaring it explicitly protects the timing if a transition is changed later.

### 2. Give the cards more safe-area margin

The current cards are close to the canvas edges once their shadows are included. Consider:

    .cards { gap: 32px; }

    .card {
      width: 420px;
      padding: 40px;
    }

After changing this, render a still frame around the middle of the card scene and check the actual output.

### 3. Center the background orb explicitly

Use explicit positioning instead of relying on the grid static position:

    .orb {
      position: absolute;
      inset: 0;
      margin: auto;
      width: 900px;
      height: 900px;
    }

Keep the existing gradient and animation declarations when applying this change.

### 4. Use deterministic icons for production output

The current cards use emoji icons. Emoji glyphs can differ between Windows, macOS, Linux, and headless Chrome. For consistent renders, replace them with inline SVG icons or CSS shapes.

### 5. Add a direction comment to the HTML

Document the visual system near the top of index.html: palette, type sizes, motion language, scene timing, and the intended hand-off between scenes. This makes later edits less likely to break the visual hierarchy.

## Troubleshooting

### npx.ps1 cannot be loaded

Use the Windows command shim:

    npx.cmd gomotion doctor

Alternatively, run the commands from Command Prompt instead of PowerShell.

### ENOTCACHED or npm cannot download GoMotion

This means npm cannot reach the registry and GoMotion is not already cached. Check the internet connection, proxy, firewall, or npm registry configuration, then retry:

    npm.cmd config get registry
    npx.cmd gomotion doctor

The expected registry is normally:

    https://registry.npmjs.org/

### Chrome is missing

Run:

    npx.cmd gomotion install
    npx.cmd gomotion doctor

### FFmpeg is missing

Install FFmpeg, add its bin directory to PATH, reopen the terminal, and run:

    ffmpeg -version
    npx.cmd gomotion doctor

### The output looks different on another computer

Check for system-font differences, emoji differences, missing assets, and browser-version differences. Prefer GoMotion's installed renderer and replace emoji with SVG for production output.

### The video is clipped or text is off-screen

Run check, inspect still frames, and reduce the size of the affected element. Do not solve clipping by simply moving content outside the safe area; resize the layout and verify the actual rendered frame.

## Final acceptance checklist

Before sharing the video, confirm:

- [ ] gomotion doctor passes.
- [ ] gomotion check index.html has no unresolved errors.
- [ ] The 1920x1080 snapshot has no clipping.
- [ ] The title, cards, and final counter are readable.
- [ ] The counter reaches 300 frames.
- [ ] The final render is exactly 10 seconds at 30 FPS.
- [ ] The MP4 opens in a normal media player.
- [ ] The output has been checked at normal playback speed.
- [ ] Production icons do not depend on platform-specific emoji.
- [ ] The final file is stored in out/ and the source is backed up or committed to Git.

## Project files

- index.html: the complete GoMotion composition.
- brief.md: optional review brief and beat timings.
- snapshots/: generated stills and contact sheets.
- out/: generated video files.
- .agents/skills/gomotion/: project-specific GoMotion guidance.
