# Debugging atom

Is there something wrong with atom? Here are some things that might help you.

## Resources

https://flight-manual.atom.io/ - "the one and only book on everything Atom"

https://github.com/atom/atom/discussions - general discussion

https://github.com/atom/atom/issues - discuss issues

https://flight-manual.atom.io/api - Atom api, might be outdated

https://github.com/atom - There are so many dependencies and stuff under the atom organization

## Open the command pallete

Usually the "keybinding" (Mac <kbd>cmd</kbd>+<kbd>shift</kbd>+<kbd>p</kbd>, windows/linux <kbd>ctrl</kbd>+<kbd>shift</kbd>+<kbd>p</kbd>) opens the command pallete.

You _could_ also do this with `View > Toggle Command Pallete` if that doesn't work

![image](https://user-images.githubusercontent.com/58114641/151628520-1bf8fa87-f181-406a-8aa3-6365bfeab425.png)

Also: https://flight-manual.atom.io/getting-started/sections/atom-basics/#command-palette

## Inspect element

Randomly described in places such as https://flight-manual.atom.io/using-atom/sections/basic-customization/#style-tweaks

Once you've opened devtools, either by keyboard or with `Window: Toggle Dev Tools` (see link), you can inspect element by clicking here:

![windows gets ctrl+shift+c, should be the same as in chrome so search online for other platforms](https://user-images.githubusercontent.com/58114641/151629864-3e60f3bc-487d-44e0-8535-2a44bbd0c366.png)

Then click whatever you want to inspect.

Example: https://github.com/atom/atom/discussions/23150#discussioncomment-1908618

## Opening atom in dev/safe mode

There are commands for that: `Application: Open Dev` and `Application: Open Safe`

<details>
  <summary>If you've added Atom to path</summary>

  General steps (google helps)

  1. Find where atom binaries are
     (eg: `C:\Users\[user]\AppData\Local\atom\bin` or `C:\Users\[user]\AppData\Local\atom-beta\bin`)
  2. Add to path

  ...then you can also run `atom --safe` or `atom --dev` or even `atom --dev --safe`

  Note: If you're using atom-beta, the commands are `atom-beta --safe`, `atom-beta --dev`, etc.
</details>

## Screenshots

In windows, if you don't know about <kbd>win</kbd>+<kbd>ctrl</kbd>+<kbd>s</kbd>, you're missing out.

Screenshots are so helpful and easy because you can just paste them directly into many places on github.

## Common errors

There are 410 issues with the text `keyboard-layout-manager.node` (https://github.com/atom/atom/search?q=keyboard-layout-manager.node&type=issues)