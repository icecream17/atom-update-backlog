# How to build atom...

...on gitpod.

1. fork atom
2. gitpod.io/#<your fork of atom>
3. open the terminal if it's not open already using <kbd>ctrl</kbd>+<kbd>`</kbd>
4. cancel the default gitpod installation in the terminal by clicking and <kbd>ctrl</kbd>+<kbd>c</kbd>
5. run the following commands (see https://flight-manual.atom.io/hacking-atom/sections/hacking-on-atom-core/#platform-linux):
   ```shell
   clear
   nvm install 14
   sudo apt-get install build-essential git libsecret-1-dev fakeroot rpm libx11-dev libxkbfile-dev
   ./script/build
   ```
