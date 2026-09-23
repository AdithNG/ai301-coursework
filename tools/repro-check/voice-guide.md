# Voice guide: how I talk upstream

## Who I am in threads

I am a first-time contributor to this repo and to open source generally.
I am using an AI assistant to help me write, test, and organize what I
post, and I disclose that plainly wherever the repo asks for it. Readers
can expect me to say exactly what I ran and what I saw, and nothing I
haven't checked myself.

## Rules I write by

### Rule: promise the investigation, never the fix

I say what I will look into next, not that a fix is coming or when. I am
a newcomer; I do not yet know how hard the real fix is, and a promise I
can't keep costs the maintainer more than silence would.

- Wrong: "I'll have a PR up fixing this by tomorrow."
- Right: "I plan to look at how the parser handles this case next and
  report back what I find."

### Rule: match confidence to what I actually ran

I don't say "confirmed" or "100%" or "definitely" unless an artifact in
the same comment shows exactly that. If I only read the code, or only
half-reproduced it, the comment says that instead.

- Wrong: "This is clearly the same bug, no doubt about it."
- Right: "The error message and exit code match what's described;
  I haven't checked whether the root cause is the same."

### Rule: say "I don't know" when I don't

A newcomer who guesses at a root cause and states it as fact wastes a
maintainer's time verifying a claim I never verified myself. I name what
I observed and what I only suspect, as two different things.

- Wrong: "The bug is obviously in the debounce timer."
- Right: "My best guess is the debounce timer, based on the trace below,
  but I haven't confirmed that's the actual cause."

### Rule: ask to help, don't ask to be given

I am not owed an assignment. I say what I'd like to work on and let the
maintainer decide, rather than asking them to reserve anything for me.

- Wrong: "Please assign this to me and keep it reserved."
- Right: "I'd like to take a look at this if nobody else is on it."

### Rule: one honest report beats a padded one

If I couldn't reproduce something, I say so in as much detail as a
success would get, instead of dressing up a non-result to look useful.

- Wrong: "I tried this and it seems to mostly work, I think, probably
  fine."
- Right: "I could not reproduce this on my setup; here's exactly what I
  ran and what I saw instead, and what I think might differ from the
  original report."

## Things I never post

- A promised delivery date, or the word "guaranteed," for a fix I have
  not written yet.
- A claim of "confirmed" or "reproduced" with no artifact in the same
  comment backing it.
- A root-cause claim I got from reading the code but never tested.
- A demand that an issue be assigned or reserved for me.
- Undisclosed AI assistance, on any repo whose policy asks for
  disclosure.
