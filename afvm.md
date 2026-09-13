---
date: 2026-09-13
keywords: [spotify]
---
# Install Spotify as a Debian package

See <https://www.spotify.com/us/download/linux/>.

```shell
curl -sS https://download.spotify.com/debian/pubkey_5384CE82BA52C83A.asc | sudo gpg --dearmor --yes -o /etc/apt/trusted.gpg.d/spotify.gpg
echo "deb https://repository.spotify.com stable non-free" | sudo tee /etc/apt/sources.list.d/spotify.list
sudo apt-get update && sudo apt-get install spotify-client
```
