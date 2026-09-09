# Ura — Omarchy's clock in Slovenian

Omarchy's clock widget with Slovenian day and month names, and a Slovenian
calendar popup. Drop-in replacement: install it and it takes the built-in
clock's place in the bar, keeping its position and its format settings.

```bash
omarchy plugin add https://github.com/sbelcl/omarchy-clock-sl.git --enable
```

Removing it puts the built-in clock back.

## Why this is a whole plugin and not a setting

The bar label was English for a reason nobody chose. `Qt.formatDateTime(date,
format)` renders day and month names from the C locale whatever `LANG` says —
it prints `Tuesday` in a process whose `Qt.locale()` is already `sl_SI`.
`Date.toLocaleString(Qt.locale(), format)` takes the same format string and
reads the system locale, so `dddd HH:mm` becomes `torek 12:21`. That is the
whole fix, and it is one line.

The calendar popup was English by decision rather than by accident: upstream
pins its day names with `Qt.locale("en_US")` and says so in a comment — *"The
interface is English throughout"*. This build is not, so they follow the
system locale like everything else.

Neither change is reachable from outside the plugin, which is why this exists
as a fork rather than as a setting. `manifest.json` declares
`omarchy.clonedFrom: "omarchy.clock"`, and the shell uses that to route the
built-in's IPC here, take its slot in the bar with its settings intact, and
restore the built-in if this is removed.

## One translation note

*Start weeks on Wednesday* became **Začetek tedna: sreda** rather than
*Začni teden v sredo*. Qt hands out day names in the nominative, and the
preposition would need the accusative — `v ponedeljek` but `v sredo`, not
`v sreda`. Rephrasing to a label plus a colon needs no agreement at all,
which a string table could not have fixed.

## Keeping it current

This is a copy of Omarchy's clock at **v4.0.2**, so upstream fixes do not
reach it on their own. `upstream.diff` records everything this build changes —
six strings, two lines of behaviour. To re-sync after an Omarchy release:

```bash
cp /usr/share/omarchy/shell/plugins/panels/clock/{BarWidget.qml,Panel.qml,Model.js} .
patch -p0 < upstream.diff     # or re-apply by hand; it is a small diff
omarchy plugin validate .
```

Keep the diff small. Everything added here is a line that has to be carried
forward by hand at every release.

## License

MIT, as upstream. Derived from [Omarchy](https://github.com/omacom/omarchy).
