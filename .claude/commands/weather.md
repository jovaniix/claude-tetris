Fetch and display the current local weather using wttr.in.

Steps:
1. Run `curl -s "wttr.in/Miami+Gardens?format=v2"` via Bash to get the weather (default location: Miami Gardens).
2. If the user passed an argument (e.g. `/weather Paris`), use that as the location: `curl -s "wttr.in/$ARGUMENTS?format=v2"`. If no argument was given, default to `Miami+Gardens` as the location.
3. Present the result clearly in chat — temperature, condition, humidity, wind. No extra commentary.

If curl fails or returns an error, try `curl -s "wttr.in/?format=3"` as fallback and report any network issue plainly.
