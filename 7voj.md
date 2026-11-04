---
date: 2026-11-03
keywords: []
---
# How to install `.deb` files

````ai
Here are the common ways to install a `.deb` file on Ubuntu.

**1. With apt (recommended)**

It resolves and installs dependencies automatically:

```bash
sudo apt install ./package.deb
```

Keep the `./` (or give a full path). Without it, apt looks for a package of that name in the repositories.

**2. With dpkg**

```bash
sudo dpkg -i package.deb
```

If it reports missing dependencies, fix them with:

```bash
sudo apt install -f
```

**3. With the GUI**

Double-click the file and choose **Open with Software Install** (or "App Center" on newer releases), then click **Install**.

**Other useful commands**

- Remove the package: `sudo apt remove packagename`
- List the contents of a .deb before installing: `dpkg -c package.deb`
- Show its metadata: `dpkg -I package.deb`

Only install `.deb` files from sources you trust, because they run install scripts as root.
````

I sometimes get this error:

```text
Notice: Download is performed unsandboxed as root as file '/home/ionlipsiuc/Downloads/ProtonMail-desktop-beta.deb' couldn't be accessed by user '_apt'. - pkgAcquire::Run (13: Permission denied)
```

It's nothing to worry about though:

````ai
That message is a harmless notice, not an error. The install most likely still worked.

**Why it happens:** apt does its downloads as a sandboxed user called `_apt`. That user can't read files in your home directory (`/home/ionlipsiuc/Downloads/` is private to you). So apt falls back to doing it as root and tells you so.

**Check that it worked:**

```bash
dpkg -l | grep -i proton
```

If you see the package listed with `ii` at the start of the line, it's installed.

**To make the notice go away, use either of these:**

1. Copy the file somewhere `_apt` can read it, then install from there:
   ```bash
   cp ~/Downloads/ProtonMail-desktop-beta.deb /tmp/
   sudo apt install /tmp/ProtonMail-desktop-beta.deb
   ```

2. Or use dpkg, which doesn't use the sandbox:
   ```bash
   sudo dpkg -i ~/Downloads/ProtonMail-desktop-beta.deb
   sudo apt install -f   # only if it complains about dependencies
   ```

If the install is actually failing, with a message other than this notice, paste the full output and I'll look at it.
````
