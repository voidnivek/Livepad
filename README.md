# Livepad

A small browser tool where you write HTML, CSS and JS on one side and watch the page run on the other. Think of a tiny CodePen with no account, no backend and no build step. It's one `index.html` file.

I made it because I was asked to show what I can do, and I figured a tool I'd actually use said more than a todo app would.

## Running it

Open `index.html` in a browser. That's the whole setup.

To put it online, drag the file onto Netlify Drop, or put it in a folder and run `vercel`. Since it's a single static file there's nothing to configure.

## How it works

The preview is an `<iframe>` and I set its `srcdoc` to the page you wrote. The iframe is sandboxed without `allow-same-origin`, so your code can run scripts but can't read Livepad's own page or its storage.

You write in three tabs, but a browser only wants one document. So on every update I stitch them together: the CSS goes in a `<style>` just before `</head>`, the JS goes in a `<script>` just before `</body>`. "Download code" and "New tab" use the same merged page, so what you download is exactly what you were looking at.

The console panel was the fun part. The iframe can't share variables with the parent, so before your code runs I inject a tiny script that wraps `console.log/info/warn/error`, plus listeners for `error` and `unhandledrejection`. Each message gets sent to the parent with `postMessage`, and the parent prints it. The parent only trusts messages where `event.source` is that iframe.

Error line numbers were a small headache. The browser reports a line in the *merged* document, which means nothing to someone looking at the JS tab. I record which line the `<script>` starts on when I merge, then subtract it, so you see "JS line 6" and it matches the tab.

The examples are stored as normal full HTML pages. When you load one, a little splitter pulls the `<style>` and `<script>` blocks out into the CSS and JS tabs.

Everything you type is saved in `localStorage` on every keystroke. Nothing leaves your browser.

## A bug I hit

Loading an example used to call `confirm("Replace your current code?")`. In some previewers and sandboxed frames, `confirm()` is blocked and silently returns `false`, so picking an example did nothing at all. I replaced it with an Undo link that shows up after loading, which turned out nicer anyway.

## What it doesn't do (yet)

- No syntax highlighting. The editors are plain `<textarea>`s. I'd rather ship something small that works than pull in a big editor library for a first version. CodeMirror would be the obvious next step.
- No autocomplete or auto-closing tags.
- No sharing by link.
- Only one file per tab, so no extra pages or imports.
- The two fonts load from Google Fonts. If they fail, it falls back to system fonts and still works.

## Files

```
index.html   the whole app
README.md    this
```

## Who made this

Nivek. I build things for the web and I'm looking for a team to join. If you want to talk, I'm on Telegram: https://t.me/Nivekversehub
