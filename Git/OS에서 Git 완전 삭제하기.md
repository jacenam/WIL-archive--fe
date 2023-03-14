- `which git`
- `/usr/local/git/uninstall.sh`
- `rm -rf /usr/local/git  rm /etc/paths.d/git  rm /etc/manpaths.d/git`

The /usr/bin directory is restricted by SIP. You first need to temporarily disable it by booting to recovery, starting Terminal, and executing csrutil disable (then csrutil enable to re-enable`).

Once you have SIP disabled, you can remove git from /usr/bin.

***

### 참고
- [About SIP(System Integrity Protection) on your Mac](https://support.apple.com/en-us/HT204899)
- 
