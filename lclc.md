---
date: 2026-07-17
keywords: [calibre]
---
# Delete calibre's config

I have had issues where I create a calibre library, decide I want to delete it,
and delete it. However, upon opening calibre again, it creates the same library
in the same location. This annoys me. To fix this, ensure calibre is closed,
delete the library in Finder, and then delete `~/Library/Preferences/calibre`.
This will reset calibre and opening it again will bring up the setup wizard.
