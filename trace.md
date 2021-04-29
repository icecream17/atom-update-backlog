# https://github.com/atom/atom/pull/21379

It errors! By timeout. This file attempts to figure out why there's a timeout

The only different is the update of apm. https://github.com/atom/apm/releases says 2.6.1 but everything else says 2.6.2. Sigh

Here the problem is called `freezing`

## \<root>

The error in question: https://github.visualstudio.com/Atom/_build/results?buildId=106648&view=logs&j=cc1319d8-c5d8-51ea-2303-6260ffcfeb03&t=f4caca9b-f6bc-559f-c5db-d0ac57d78fa0&l=210

```
Pane
  pending state
    it Pane pending state does not open file in pending state by default
      timeout: timed out after 120000 msec waiting for spec promise to resolve
```

### `pane-spec.js`

The test in question:
```js

    it('does not open file in pending state by default', async () => {
      await atom.workspace.open('sample.js');
      expect(pane.getPendingItem()).toBeNull();
    });

```

However there's more context:

```js

  describe('pending state', () => {
    let editor1, pane, eventCount;

    // This part is probably where it freezes
    beforeEach(async () => {
      editor1 = await atom.workspace.open('sample.txt', { pending: true });
      pane = atom.workspace.getActivePane();
      eventCount = 0;
      editor1.onDidTerminatePendingState(() => eventCount++);
    });

    // test in question
    it('does not open file in pending state by default', async () => {
      await atom.workspace.open('sample.js');
      expect(pane.getPendingItem()).toBeNull();
    });

```

(nested inside more context)

```js
const { extend } = require('underscore-plus');
const { Emitter } = require('event-kit');
const Grim = require('grim');
const Pane = require('../src/pane');
const PaneContainer = require('../src/pane-container');
const { conditionPromise, timeoutPromise } = require('./async-spec-helpers');

describe('Pane', () => {
  let confirm, showSaveDialog, deserializerDisposable;

  class Item {
    static deserialize({ name, uri }) {
      return new Item(name, uri);
    }

    constructor(name, uri) {
      this.name = name;
      this.uri = uri;
      this.emitter = new Emitter();
      this.destroyed = false;
    }

    getURI() {
      return this.uri;
    }
    getPath() {
      return this.path;
    }
    isEqual(other) {
      return this.name === (other && other.name);
    }
    isPermanentDockItem() {
      return false;
    }
    isDestroyed() {
      return this.destroyed;
    }

    serialize() {
      return { deserializer: 'Item', name: this.name, uri: this.uri };
    }

    copy() {
      return new Item(this.name, this.uri);
    }

    destroy() {
      this.destroyed = true;
      return this.emitter.emit('did-destroy');
    }

    onDidDestroy(fn) {
      return this.emitter.on('did-destroy', fn);
    }

    onDidTerminatePendingState(callback) {
      return this.emitter.on('terminate-pending-state', callback);
    }

    terminatePendingState() {
      return this.emitter.emit('terminate-pending-state');
    }
  }

  beforeEach(() => {
    confirm = spyOn(atom.applicationDelegate, 'confirm');
    showSaveDialog = spyOn(atom.applicationDelegate, 'showSaveDialog');
    deserializerDisposable = atom.deserializers.add(Item);
  });

  afterEach(() => {
    deserializerDisposable.dispose();
  });

```

Resulting test:

```js
// 1.
  () => {
    confirm = spyOn(atom.applicationDelegate, 'confirm');
    showSaveDialog = spyOn(atom.applicationDelegate, 'showSaveDialog');
    deserializerDisposable = atom.deserializers.add(Item);
  }
// 2.
    async () => {
      editor1 = await atom.workspace.open('sample.txt', { pending: true });
      pane = atom.workspace.getActivePane();
      eventCount = 0;
      editor1.onDidTerminatePendingState(() => eventCount++);
    }
// 3.
    async () => {
      await atom.workspace.open('sample.js');
      expect(pane.getPendingItem()).toBeNull();
    }
```

Since here it only matters whether the test finishes, the spyOns can be removed, since they can't freeze.

And the assignments probably don't do anything unless something's really crazy.

So that can also be removed

And I optimized it even more

```js
// 1.
  () => {
    atom.deserializers.add(Item);
  }
// 2.
    async () => {
      editor1 = await atom.workspace.open('sample.txt', { pending: true });
      pane = atom.workspace.getActivePane();
      eventCount = 0;
      editor1.onDidTerminatePendingState(() => eventCount++);
    }
// 3.
    async () => {
      await atom.workspace.open('sample.js');
      expect(pane.getPendingItem()).toBeNull();
    }
```

### 1. atom.deserializers.add

https://flight-manual.atom.io/api/v1.56.0/AtomEnvironment/#instance-deserializers

"A DeserializerManager instance"

I simplified the `.add` method:
```js
  // args = [ class Item {...} ]
  add(...deserializers) {
    this.deserializers["Item"] = args[0] // class Item {...}

    return <doesn't matter>
  }
```

### 2. atom.workspace.open

Already on step 2 !!!

atom.workspace is a Workspace instance...

You can ignore the documentation
```js

  // Essential: Opens the given URI in Atom asynchronously.
  // If the URI is already open, the existing item for that URI will be
  // activated. If no URI is given, or no registered opener can open
  // the URI, a new empty {TextEditor} will be created.
  //
  // * `uri` (optional) A {String} containing a URI.
  // * `options` (optional) {Object}
  //   * `initialLine` A {Number} indicating which row to move the cursor to
  //     initially. Defaults to `0`.
  //   * `initialColumn` A {Number} indicating which column to move the cursor to
  //     initially. Defaults to `0`.
  //   * `split` Either 'left', 'right', 'up' or 'down'.
  //     If 'left', the item will be opened in leftmost pane of the current active pane's row.
  //     If 'right', the item will be opened in the rightmost pane of the current active pane's row. If only one pane exists in the row, a new pane will be created.
  //     If 'up', the item will be opened in topmost pane of the current active pane's column.
  //     If 'down', the item will be opened in the bottommost pane of the current active pane's column. If only one pane exists in the column, a new pane will be created.
  //   * `activatePane` A {Boolean} indicating whether to call {Pane::activate} on
  //     containing pane. Defaults to `true`.
  //   * `activateItem` A {Boolean} indicating whether to call {Pane::activateItem}
  //     on containing pane. Defaults to `true`.
  //   * `pending` A {Boolean} indicating whether or not the item should be opened
  //     in a pending state. Existing pending items in a pane are replaced with
  //     new pending items when they are opened.
  //   * `searchAllPanes` A {Boolean}. If `true`, the workspace will attempt to
  //     activate an existing item for the given URI on any pane.
  //     If `false`, only the active pane will be searched for
  //     an existing item for the same URI. Defaults to `false`.
  //   * `location` (optional) A {String} containing the name of the location
  //     in which this item should be opened (one of "left", "right", "bottom",
  //     or "center"). If omitted, Atom will fall back to the last location in
  //     which a user has placed an item with the same URI or, if this is a new
  //     URI, the default location specified by the item. NOTE: This option
  //     should almost always be omitted to honor user preference.
  //
  // Returns a {Promise} that resolves to the {TextEditor} for the file URI.


  
  // args = 
  //   itemOrURI: 'sample.txt'
  //   options: { pending: true }

  // reminder; simplified
  async open(itemOrURI, options = {}) {
    let uri = this.project.resolvePath(itemOrURI); // 2.a
    let item;
    let resolveItem = () => {};

    if (uri) {
      const incomingItem = this.incoming.get(uri);
      if (!incomingItem) {
        this.incoming.set(
          uri,
          new Promise(resolve => {
            resolveItem = resolve;
          })
        );
      } else {
        await incomingItem;
      }
    }

    try {
      if (!atom.config.get('core.allowPendingPaneItems')) {
        options.pending = false;
      }

      // Avoid adding URLs as recent documents to work-around this Spotlight crash:
      // https://github.com/atom/atom/issues/10071
      if (uri && (!url.parse(uri).protocol || process.platform === 'win32')) {
        this.applicationDelegate.addRecentDocument(uri);
      }

      let pane, itemExistsInWorkspace;

      // Try to find an existing item in the workspace.
      if (item || uri) {
        if (options.pane) {
          pane = options.pane;
        } else if (options.searchAllPanes) {
          pane = item ? this.paneForItem(item) : this.paneForURI(uri);
        } else {
          // If an item with the given URI is already in the workspace, assume
          // that item's pane container is the preferred location for that URI.
          let container;
          if (uri) container = this.paneContainerForURI(uri);
          if (!container) container = this.getActivePaneContainer();

          // The `split` option affects where we search for the item.
          pane = container.getActivePane();
          switch (options.split) {
            case 'left':
              pane = pane.findLeftmostSibling();
              break;
            case 'right':
              pane = pane.findRightmostSibling();
              break;
            case 'up':
              pane = pane.findTopmostSibling();
              break;
            case 'down':
              pane = pane.findBottommostSibling();
              break;
          }
        }

        if (pane) {
          if (item) {
            itemExistsInWorkspace = pane.getItems().includes(item);
          } else {
            item = pane.itemForURI(uri);
            itemExistsInWorkspace = item != null;
          }
        }
      }

      // If we already have an item at this stage, we won't need to do an async
      // lookup of the URI, so we yield the event loop to ensure this method
      // is consistently asynchronous.
      if (item) await Promise.resolve();

      if (!itemExistsInWorkspace) {
        item = item || (await this.createItemForURI(uri, options));
        if (!item) return;

        if (options.pane) {
          pane = options.pane;
        } else {
          let location = options.location;
          if (!location && !options.split && uri && this.enablePersistence) {
            location = await this.itemLocationStore.load(uri);
          }
          if (!location && typeof item.getDefaultLocation === 'function') {
            location = item.getDefaultLocation();
          }

          const allowedLocations =
            typeof item.getAllowedLocations === 'function'
              ? item.getAllowedLocations()
              : ALL_LOCATIONS;
          location = allowedLocations.includes(location)
            ? location
            : allowedLocations[0];

          const container = this.paneContainers[location] || this.getCenter();
          pane = container.getActivePane();
          switch (options.split) {
            case 'left':
              pane = pane.findLeftmostSibling();
              break;
            case 'right':
              pane = pane.findOrCreateRightmostSibling();
              break;
            case 'up':
              pane = pane.findTopmostSibling();
              break;
            case 'down':
              pane = pane.findOrCreateBottommostSibling();
              break;
          }
        }
      }

      if (!options.pending && pane.getPendingItem() === item) {
        pane.clearPendingItem();
      }

      this.itemOpened(item);

      if (options.activateItem === false) {
        pane.addItem(item, { pending: options.pending });
      } else {
        pane.activateItem(item, { pending: options.pending });
      }

      if (options.activatePane !== false) {
        pane.activate();
      }

      let initialColumn = 0;
      let initialLine = 0;
      if (!Number.isNaN(options.initialLine)) {
        initialLine = options.initialLine;
      }
      if (!Number.isNaN(options.initialColumn)) {
        initialColumn = options.initialColumn;
      }
      if (initialLine >= 0 || initialColumn >= 0) {
        if (typeof item.setCursorBufferPosition === 'function') {
          item.setCursorBufferPosition([initialLine, initialColumn]);
        }
        if (typeof item.unfoldBufferRow === 'function') {
          item.unfoldBufferRow(initialLine);
        }
        if (typeof item.scrollToBufferPosition === 'function') {
          item.scrollToBufferPosition([initialLine, initialColumn], {
            center: true
          });
        }
      }

      const index = pane.getActiveItemIndex();
      this.emitter.emit('did-open', { uri, pane, item, index });
      if (uri) {
        this.incoming.delete(uri);
      }
    } finally {
      resolveItem();
    }
    return item;
  }
```

### 2.a: atom.workspace.project.resolvePath

atom.workspace.project === atom.project, no surprise === Project

Assumed everything is simplified
```js
  // args:
  //   uri: 'sample.txt'
  resolvePath(uri) {
      let projectPath = this.getPaths()[0] // 2.a.1
      if (projectPath) {
        return this.defaultDirectoryProvider.normalizePath(
          fs.resolveHome(path.join(projectPath, uri))
        );
      } else {
        return undefined;
      }
    }
  }
```

### 2.a.1: atom.project.getPaths

```js
    try {
      return this.rootDirectories.map(rootDirectory => rootDirectory.getPath()); // 2.a.1.a
    } catch (e) {
      atom.notifications.addError(
        "Please clear Atom's window state with: atom --clear-window-state"
      );
    }
```

### 2.a.1.a: atom.project.rootDirectories

```js
initial: []
```

It's only modified by addPath:

```js
  addPath(projectPath, options = {}) {
    const directory = this.getDirectoryForProjectPath(projectPath); // 2.a.1.a.1
    let ok = true;
    if (options.exact === true) {
      ok = directory.getPath() === projectPath;
    }
    ok = ok && directory.existsSync();

    if (!ok) {
      if (options.mustExist === true) {
        const err = new Error(`Project directory ${directory} does not exist`);
        err.missingProjectPaths = [projectPath];
        throw err;
      } else {
        return;
      }
    }

    for (let existingDirectory of this.getDirectories()) {
      if (existingDirectory.getPath() === directory.getPath()) {
        return;
      }
    }

    this.rootDirectories.push(directory);
```

### 2.a.1.a.1

So much function calls

```js
  getDirectoryForProjectPath(projectPath) {
    let directory = this.getProvidedDirectoryForProjectPath(projectPath); // 2.a.1.a.1.a
    if (directory == null) {
      directory = this.defaultDirectoryProvider.directoryForURISync(
        projectPath
      );
    }
    return directory;
  }
```

#### 2.a.1.a.1.a

```js
  getProvidedDirectoryForProjectPath(projectPath) {
    for (let provider of this.directoryProviders) {
      if (typeof provider.directoryForURISync === 'function') {
        const directory = provider.directoryForURISync(projectPath);
        if (directory) {
          return directory;
        }
      }
    }
    return null;
  }
```

#### 2.a.1.a.1.a.1 atom.project.directoryProviders

```js
starts as:
  []


changed by:
  consumeServices({ serviceHub }) {
    serviceHub.consume('atom.directory-provider', '^0.1.0', provider => {
      this.directoryProviders.unshift(provider); // here
      return new Disposable(function that removes the provider);
    });
```

The function is called with atom.packages - not showing the trace

#### 2.a.1.a.1.a.1.a: atom.packages.serviceHub.consume

```
const ServiceHub = require('service-hub')

...
this.serviceHub = new ServiceHub()
```

#### 2.a.1.a.1.a.1.a.1: (npm) service-hub.consume

Main repo: https://github.com/atom/service-hub
Npm page: https://www.npmjs.com/package/service-hub

The readme is great since I don't have to check the code.

'atom.directory-provider' is probably this: https://github.com/atom/atom/blob/master/src/default-directory-provider.coffee

It returns `directory` which is a `new Directory()`, where `Directory = require 'pathwatcher'`

#### 2................a: https://github.com/atom/node-pathwatcher

I'm not sure how this works



