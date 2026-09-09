# Aster web-app file integration

Aster Web Files connects web applications to Aster's virtual files. It does not expose the physical host filesystem or circumvent iframe security.

## Routing

Settings → Apps → Web app files provides global defaults and per-application overrides for the integration master switch, open-file pickers, save-file pickers and writable handles, folder pickers, HTML file inputs, generated Blob/data downloads, drag-in, drag-out, and private app storage. Native device-file imports are separately controlled. Private app storage is opt-in.

Same-origin owned catalog applications can connect automatically. Opaque custom HTML applications receive a capability client before their own code while retaining their existing sandbox. Cross-origin applications must include the cooperative SDK and explicitly allow the parent origin. The connection panel distinguishes Connected from SDK required. Turning on a setting is not evidence that an unrelated cross-origin application has adopted the SDK.

Disabling a route invokes the application's saved native browser API where available. Existing Aster handles are revoked, not converted into host handles. Native API availability and user-activation requirements still apply.

## File interfaces

The client implements asynchronous FileSystemFileHandle-style and FileSystemDirectoryHandle-style interfaces: getFile, createWritable, write/seek/truncate/close/abort, permission queries, directory enumeration, child creation, removal, identity and relative resolution. Applications receive real browser File objects and HTML file inputs receive a FileList with input/change events. Only user-selected files or selected folder subtrees are granted. Handles are scoped to a live frame connection and are invalidated by revocation, navigation, closing the application, or policy changes.

Writers stage content and commit on close. Abort does not replace the original file. Commit rechecks the file revision and permission and rejects a competing newer write. Replacements and previous-version metadata use one IndexedDB transaction. The virtual path validator rejects traversal and protected/mounted roots while allowing project dotfiles inside a selected subtree.

Custom handles are not native, structured-cloneable browser handles. Applications that persist native handles in IndexedDB must reacquire Aster handles. Synchronous access handles, worker-owned OPFS, IndexedDB and localStorage are not intercepted or migrated. Main-thread navigator.storage.getDirectory redirection is explicit opt-in and grants only a per-app folder.

## Transfers

Explorer-to-app transfers use expiring unpredictable offers, then deliver real Files to the integrated drop target. App-to-Explorer transfers import actual bytes through the existing file-operation conflict workflow. Native dropped files and supported directory entries are read during the browser's permitted drop-event lifetime; nested paths and empty folders are retained. Folder import uses keep-both roots instead of merging or overwriting an existing tree.

The browser export shelf prepares byte-backed draggable files, browser DownloadURL metadata where supported, and ordinary download links. Native host-folder drag-out and cross-tab drag behavior vary by browser; the download link is the reliable fallback. Window-list or drag animations are not evidence of actual file transfer.

## Limits and security

Limits: 64 MiB per file, 128 MiB staged/transfer data, 2,048 entries or handles, 8 writers, 12 concurrent requests, 24 path components, bounded app overrides and expiring transfer offers. The host verifies the registered iframe source and expected origin before transferring a private MessagePort. Tokens do not grant authority to arbitrary parent-message senders. System/mounted host roots are not shareable. Applications already sharing Aster's browser origin are trusted same-origin code; the bridge does not manufacture an isolation boundary between those applications.

Test evidence must distinguish original protocol fixtures from unchanged deployed applications, injected memory-mode tests from HTTP/IndexedDB tests, and synthetic file-data tests from actual pointer gestures. Merge only after the file integration suite and inherited desktop/theme/Win32 suites pass on the exact proposed head.
