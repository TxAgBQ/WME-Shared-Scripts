# WME Segment Turnaround Assist

A Waze Map Editor userscript that finds "Turnaround" segments and makes them match the frontage road they belong to: road type, lock level and (when the frontage road shares one) the +1 routing type.

It never saves for you. You review a checklist, apply the rows you want, then save in WME yourself.

## What it checks

The script looks at segments in the part of the map you can see whose primary street is named **Turnaround**, in any state. Several Turnaround segments in a row are treated as one turnaround (one row, one checkbox).

For each turnaround it looks at the two outer ends and finds the **named** segments there. That is the frontage road, and its names may differ at each end (for example I-10 W Frontage Rd and I-10 E Frontage Rd). Unnamed segments at an end are treated as at-grade connectors and ignored.

A turnaround needs fixing when any of these differ from what it should be:

| Property | Rule |
| --- | --- |
| Road type | Copied from the frontage road. Only Ramp, Minor Highway and Major Highway count. If the two ends differ, the lowest type wins (Ramp, then Minor Hwy, then Major Hwy). A ramp that branches off the frontage road does not decide the type when a Minor or Major Highway is also at that end. |
| Lock | Raised to L5, or to your own maximum rank if that is lower. It never lowers a lock. |
| Routing (+1) | Copied only when every named frontage segment shares the same routing type. |
| Turn onto the turnaround | The turn from the frontage road into the first turnaround segment should be enabled and have its Voice prompt set to **U-turn**. The script sets it for you (see below). |

Turnarounds that connect street to street are left alone.

### The U-turn voice prompt

The WME SDK can read a turn's instruction but not change it, so the script sets it through WME's own Turn menu: it selects the frontage segment, hovers the turn arrow and sets **Voice prompt** to U-turn. The edit is a normal unsaved WME edit and can be undone. If several segments lead onto a turnaround and none is set to U-turn, the row is flagged for you to fix by hand. A turn that is not enabled is also flagged, not changed.

### Rows that need review

These are listed but not applied unless noted:

- Neither end has a Ramp, Minor Highway or Major Highway.
- Nothing is connected at one end, or the two ends can't be found.
- You don't have edit permission on one of the segments.
- A connected segment isn't loaded yet. Pan or zoom and it rescans.
- One end has only unnamed connectors, or no usable road type. These rows show what would be applied and have a checkbox to override. Check it only after you've looked at the spot yourself.

## Using it

1. Install the script (see below) and open WME. A round icon appears in a small card at the top of the map. It turns amber with a number when turnarounds need changing, and red with "!" when only review items are found.
2. Click the icon to open the list. Click a segment ID to select it and center it on the map.
3. Uncheck anything you don't want, then click **Apply checked**. Use **All** and **None** to change the selection quickly.
4. Save in WME as you normally do.

The card can be dragged by its grip and minimized to an icon in the stack of buttons on the right side of the map. The script rescans when you move or zoom the map, and after edits, undo and saves.

## Installing

1. Install a userscript manager such as [Tampermonkey](https://www.tampermonkey.net/).
2. Install the script from its [Greasy Fork page](https://greasyfork.org/en/scripts/604417-wme-segment-turnaround-assist).

It works in both production and beta WME.

## Changing it for another state or street name

The state and street name are set at the top of the script in the `CFG` block:

```js
streetName: 'turnaround',   // compared case-insensitively against the primary street name
targetLockRank: 4,          // zero-based: 4 = L5
```

Edit those to match your area's conventions. The type names and ranking are in the same block. If you change anything, the rules above may no longer fit your area, so test on a few turnarounds first.

## Technical notes

- Uses the WME SDK in async mode only. It does not use `W.*` or OpenLayers internals.
- The SDK's lock ranks are zero-based: 2 is L3, 3 is L4, 4 is L5.
- Only changed fields are sent to WME, because the SDK rejects an update that changes nothing.

## Changelog

- 2026.11.11.04: Added @updateURL and @downloadURL so Tampermonkey auto-updates from Greasy Fork.
- 2026.11.11.03: Sets the turn onto the turnaround to U-turn; flags turns that are not enabled.
- 2026.11.11.01: Works in any state, not just Texas.

## Feedback

Please report problems on the script's [Greasy Fork feedback page](https://greasyfork.org/en/scripts/604417-wme-segment-turnaround-assist/feedback). Include the segment IDs and a screenshot of the list if you can.

## License

MIT. See the [LICENSE](../LICENSE) file in the root of this repository.
