# How to build atom...

...on gitpod.

1. fork atom
2. insert `gitpod.io/#` before `<link to your fork of atom>` and go
3. log in not described
4. open the terminal if it's not open already using <kbd>ctrl</kbd>+<kbd>`</kbd>
5. cancel the default gitpod installation in the terminal by clicking and <kbd>ctrl</kbd>+<kbd>c</kbd>
6. run the following commands (see https://flight-manual.atom.io/hacking-atom/sections/hacking-on-atom-core/#platform-linux and https://github.com/atom/flight-manual.atom.io/pull/683):
   ```shell
   clear
   nvm install 14
   sudo apt-get update
   sudo apt-get install build-essential git libsecret-1-dev fakeroot rpm libgtk-3-dev libx11-xcb-dev libxcb-dri3-dev libxkbfile-dev libxss-dev libxtst-dev
   ./script/build
   ```
