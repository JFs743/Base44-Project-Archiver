
1. Open your Base44 project, go to the Code view
1. Open dev tools (F12), go to the console tab
1. Copy the code below (Main script) in the javascript console, follow the instructions if it asks to type "allow pasting"
1. Start opening each file sequentially, order doesn't matter, it's okay if you skip one and come back to it
1. keep an eye on the javascript console, there should be a message for each captured file, sometimes it takes a second or two to capture
1. when you're done, copy-paste the End script into the console
1. Save the "dump.json" file, it contains your whole project
1. Open step2.html, drag your dump.json file onto the upload button (or click it and select your dump file)
1. either download as a zip or write to disk, it will ask for a folder to write to, it's slower than the zip, leave it be for a minute

Main script:
```js
(() => {
  const SELECTOR = '#\\:r6r\\: > div.flex.items-center.justify-between.px-3.py-1\\.5.bg-white.border-b.border-slate-100';

  // Global store (persistent during session)
  window.__fileDump = window.__fileDump || {};

  function getPath() {
    const container = document.querySelector(SELECTOR);
    if (!container) return null;

    return Array.from(container.querySelectorAll('span'))
      .map(el => el.textContent.trim())
      .filter(Boolean)
      .join('');
  }

  function getContent() {
    try {
      return monaco.editor.getModels()[0].getValue();
    } catch {
      return null;
    }
  }

  function capture() {
    const path = getPath();
    const content = getContent();

    if (!path || !content) return;

    if (window.__fileDump[path] !== content) {
      window.__fileDump[path] = content;
      console.log('[Captured]', path);
    }
  }

  // Run every 100ms
  window.__fileDumpInterval = setInterval(capture, 100);

  // Download function
  window.downloadDump = () => {
    const dataStr = JSON.stringify(window.__fileDump, null, 2);
    const blob = new Blob([dataStr], { type: 'application/json' });

    const a = document.createElement('a');
    a.href = URL.createObjectURL(blob);
    a.download = 'dump.json';
    a.click();

    URL.revokeObjectURL(a.href);
  };

  console.log('📦 File dump capture started');
})();
```

End script:
```js
downloadDump()
clearInterval(window.__fileDumpInterval);
```
