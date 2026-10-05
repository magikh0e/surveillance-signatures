# Grading

Every row in this list carries one of three confidence levels. This explains
what they mean, how they interact, what to do with them in your own code, and
how to pick one if you are contributing a row.

The short version: the grade answers "if this matches, how wrong could I be
about what the device is?" It is not a signal strength, a severity, or a
measure of how interesting the find is.

## The three levels

### Strong

The signature belongs to one vendor's product and is not plausibly anything
else. A Strong row is one you can put a vendor name next to in front of a
user.

What earns it:

- A whole six-byte address that is hardcoded into one product.
- A 128-bit service UUID. The vendor generated it for their own protocol, so
  a collision is not a thing that happens by accident.
- An address prefix belonging to a company that makes one kind of thing and
  sells it to operators rather than to consumers.
- A name or name fragment that is a product name, long enough that an
  unrelated device would not carry it.
- A manufacturer-data or service-data prefix that encodes a specific frame
  type, rather than just the company that made the radio.

### Likely

The signature fits, and could belong to something unrelated. A Likely row is
one you can show, provided you do not state it as fact.

What lands here:

- A vendor prefix where the vendor also sells unrelated hardware.
- A short or generic name fragment that is probably the product and could be
  somebody's hostname.
- A 16-bit service UUID that the vendor uses but did not get to themselves.
  The 16-bit space is allocated by the Bluetooth SIG and shared.

### Weak

Corroboration only. A Weak row on its own means close to nothing, and a
consumer of this list should not raise it to a user by itself.

Nearly all of these are contract manufacturers and module vendors: Liteon,
Espressif, Qualcomm Atheros. Their silicon is in an enormous amount of
ordinary consumer hardware. Twenty-three of the prefixes in this list are
Liteon blocks and two are Espressif, and the honest meaning of such a match
is "this device contains a common radio module", which is true of a doorbell,
a television and a dev board on a shelf.

They are in the list because they are worth something once something else
agrees with them. Not because they identify anything on their own.

## Corroboration

Two **differently labelled** signatures matching the same device promote
Likely to Strong.

The exact rule, as the reference implementation applies it:

1. A device accumulates matches over time, keyed on its address.
2. If a match arrives whose label differs from the one already recorded, the
   device is marked corroborated.
3. The highest confidence seen wins. A later match at the same level replaces
   the recorded kind and label; a lower one does not.
4. If the device is corroborated and its confidence has settled at Likely, it
   becomes Strong.

Two things follow that are easy to get wrong:

**Weak is never promoted.** Two contract-manufacturer hints agreeing are
still two contract-manufacturer hints. If Weak plus Weak made Strong, a
device with two common radios in it would be reported as surveillance
hardware, and the most common devices would produce the most confident wrong
answers.

**Same label twice is not corroboration.** Hearing "Flock Safety" from an
address prefix and again from an SSID is one claim observed twice, not two
claims agreeing. The rule compares labels for exactly this reason.

## Using these in your own project

### Order within a table is meaningful

The reference implementation stops at the first match in each table. Rows are
ordered most specific first, so a bare vendor name sits below the specific
product names that would otherwise be shadowed by it. If you reorder a table,
or match all rows instead of the first, you will get different and usually
worse answers.

### Do not match address prefixes against BLE unless the row is Strong

A WiFi scan sees devices that are probing for networks. A BLE scan hears
every advertiser in the room. Matching the whole prefix table against BLE
addresses will light up on every device that happens to contain a common
module.

The reference implementation matches prefixes against BLE only when the row
is Strong **and** the advertised address is public. A random or resolvable
private address is not a manufacturer claim at all, so matching a prefix
against one is matching against a number that was invented for privacy.

### The whole-address table is WiFi only

Every address in it is locally administered, which is what makes it a made-up
address rather than an allocation. The BLE path would never see one on a
public address, so matching there is dead code dressed as thoroughness.

### A match is about hardware, not behaviour

Everything here is broadcast unprompted. A row firing says a device of some
kind is in range. It does not say the device is recording, uploading,
tracking anybody, or working at all. Any wording you put in front of a user
should survive that distinction.

### A minimum sensible implementation

- Match the tables in the order given, first hit per table.
- Keep per-device state so corroboration can work. Without it, every Likely
  stays Likely and you lose the main thing the grades are for.
- Surface Strong by name. Surface Likely hedged. Keep Weak out of the user's
  way until something else agrees with it.
- Separate a device seen over WiFi from one seen over BLE even if the
  addresses look alike. They are different observations.

## Contributing a signature

Issues and pull requests are both fine. A wrong row matters more than a
missing one, because a wrong row names somebody, so corrections are more
welcome than additions.

### What to include

- **The signature itself**, in the form the matching table takes: a prefix,
  a name fragment, a UUID, a company ID plus the bytes after it.
- **Evidence.** A capture, a datasheet, a registry entry, a vendor's own
  documentation, a photograph of the hardware next to the scan that found it.
  A vendor name on its own is an assertion, not evidence.
- **Which kind it is**, and why that kind rather than a neighbouring one.
- **A proposed grade**, and the reasoning. Getting this wrong is not a
  problem; not thinking about it is.

### Choosing a grade

Ask what else could produce this exact signature.

- Nothing realistic, because the identifier is the vendor's own: **Strong**.
- Something could, but the odds favour this: **Likely**.
- Lots of things could, and the match only means something alongside another:
  **Weak**.

Then ask the opposite question, which is the one people skip: if this fires
on somebody's unrelated device, what does the user see? A Strong row that
misfires puts a vendor's name on an innocent object. That asymmetry is why
the default when unsure is Likely, not Strong.

### What tends to get turned down

- **A consumer product that is not surveillance.** A handheld action camera
  is a camera. The line here is hardware that observes other people as its
  purpose, not hardware that has a lens.
- **A broad vendor prefix proposed as Strong.** If the company also sells
  modules to other manufacturers, it is Weak however specific the product you
  found it on was.
- **A short name fragment matched anywhere.** A substring has no anchor, so a
  short needle hits far more than a short prefix does. Three or four
  characters in the middle of a name will find things you did not mean.
- **A 16-bit service UUID as Strong.** That space is shared and allocated by
  the SIG, so it identifies a protocol rather than a product.
- **A signature with no evidence behind it**, including one copied from
  another list. Somebody else's catalog is a lead worth checking, not a
  source worth citing.

### A note on what is already here

Almost nothing in this list has been confirmed against the hardware it names.
One Flock reader raised the row it should have, once. Everything else is
graded on the reasoning above and not on an observation, which is worth
remembering before treating any particular row as settled.
