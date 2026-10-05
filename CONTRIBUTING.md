# Contributing

Corrections are worth more than additions here. A missing row means somebody
does not get told about a camera. A wrong row puts a vendor's name on an
innocent object, in front of somebody who has no way to check it. If you only
ever send one thing, send a correction.

[GRADING.md](GRADING.md) covers how to choose a confidence level and what
tends to get turned down. This covers everything else.

## Read this first: where the data actually lives

The three data files here are generated from one extraction, so they cannot
disagree with each other, and the source of that extraction is
`ESP32-DIV/SpotterSignatures.h` in
[magikh0e/pueo](https://github.com/magikh0e/pueo).

**A pull request that edits `README.md`, `signatures.csv` or
`signatures.json` will be overwritten by the next regeneration.** That is not
a judgement on the row, it is just what happens. Open an issue instead. An
accepted signature is applied at the source and arrives here on the next
snapshot, with your name on the change.

Pull requests are welcome for everything that is not generated: `GRADING.md`,
this file, the extraction's own correctness, anything wrong in the prose.

## Open an issue

One signature per issue. Several unrelated vendors in one thread get muddled
and half of them get lost.

Include:

- **The signature**, in the form its table takes. A three-byte prefix, a name
  fragment, a UUID, a company ID plus the bytes after it.
- **Which table** you think it belongs in, or a description of where you saw
  it if you are not sure. Not knowing is fine and is not a reason to hold the
  report back.
- **The kind**: plate reader, body camera, fixed camera, smart glasses, item
  tracker, vehicle module, pentest hardware, mesh node, accessory.
- **A proposed confidence**, with your reasoning. Getting it wrong is not a
  problem. Not having thought about it is the thing that makes a row hard to
  review.
- **Evidence.** See below.
- **What it should be labelled**, which is the text a user ends up reading.
  Short, the vendor or the product, no adjectives.

## Evidence

A vendor name on its own is an assertion. These are the things that settle it:

- A capture. A pcap, a BLE advertisement dump, a screenshot of a scanner with
  the address and the name both visible.
- The vendor's own documentation, a datasheet, an FCC filing, a support page
  that names the SSID or the Bluetooth name the product uses.
- An IEEE registry entry, for a prefix. The registry says who bought the
  block, which is often a contract manufacturer and not the brand on the box.
  That distinction is most of what the grading is about.
- A photograph of the hardware next to the scan that found it. Unglamorous
  and frequently the most convincing thing in the thread.

Redact anything that identifies a person or a place: your own addresses, GPS
coordinates, the network names of homes, faces, plates. None of it is needed
to evaluate a signature and this repository is not the place for it.

**Another catalogue is a lead, not a source.** If you found a signature in
somebody else's list, say so, and then go and check it. A row copied between
lists acquires confidence it never earned, and the fifth list to carry a
mistake looks like corroboration.

## Scope

In: hardware whose purpose is observing other people, and which announces
itself over WiFi or Bluetooth Low Energy without being asked. Plate readers,
fixed and body-worn cameras, smart glasses, item trackers, fleet and
telematics modules, tyre sensors, pentest kit.

Also in, deliberately: a small number of things that are not surveillance at
all but are worth knowing about when they are nearby, such as a mesh radio
node. They get a kind of their own rather than being filed as something they
are not.

Out:

- **Consumer hardware that merely has a camera.** A handheld action camera is
  a camera. The line is purpose, not optics.
- **Anything requiring a connection.** Everything here is broadcast. If you
  have to associate, pair, authenticate or probe to see it, it does not
  belong in a passive list.
- **Individual devices.** See the next section, it is the one hard rule here.

## The one hard rule: no individual devices

Signatures identify **products**, not units.

A full six-byte address is only acceptable where that exact address is
hardcoded into every unit of the product, which makes it a product identifier
that happens to be address-shaped. There is one such row in the list and it
exists because the address is a joke the project chose and ships to everyone.

Do not submit:

- The address of a specific device you saw.
- The address of a neighbour's camera, doorbell, car or phone.
- A network name that is somebody's name, address or household.

A signature that matches exactly one physical object in the world is not a
signature, it is a report about a person, and it will be closed. If you think
you have found an exception, describe the product and why the address is
fixed across units, and leave the address out of the issue until that is
agreed.

## Licence

This repository is GPL-3.0-or-later. Contributing means you are content for
your contribution to be published under it.

Do not paste in data from a source whose licence does not permit it.
Permissively licensed catalogues are fine and get credited. Scraped
proprietary databases are not, however freely they are circulating.

## What happens next

Issues get read. Some get accepted straight away, some get questions, and
some get turned down with a reason. A row rejected for lack of evidence is
not rejected forever, and reopening it later with a capture attached is
exactly the right thing to do.

Nothing here is settled. Almost none of the list has been confirmed against
the hardware it names, so a well-evidenced correction to an existing row is
the single most useful thing anybody can send.
