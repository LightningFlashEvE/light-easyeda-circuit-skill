---
name: light-easyeda-circuit
description: "Light's personal EasyEDA schematic and PCB workflow. Use for inspecting, designing, modifying, validating, or syncing circuits in EasyEDA when the work should follow Light's component-reuse, BOM, topology, visual-quality, and verification conventions."
metadata:
  author: "LightningFlashEvE"
  version: "0.1.0"
  homepage: "https://github.com/LightningFlashEvE/light-easyeda-circuit-skill"
---

# Light EasyEDA Circuit Designer

Requires EasyEDA with the official API bridge and/or the easyeda-agent CLI,
daemon, and connector. It expects the installed `easyeda-api`, `easyeda-agent`,
and `easyeda-schematic-net-fanout` skills as capability providers.

Apply Light's personal decision rules on top of the installed EasyEDA capability
skills. Use the official EasyEDA API skill for documented `eda.*` operations and
the enhanced/community workflow for structured schematic and PCB inspection,
gates, layout, and artifact checks. Do not copy or fork those upstream skills
into this skill; keep them independently upgradeable.

## Personal component policy

Before importing or placing any ordinary passive or other reusable part, inspect
the current schematic and project for a suitable existing component.

1. Prefer reusing an existing exact part when its electrical rating, package,
   manufacturer part number, supplier part number, symbol, and footprint fit the
   new use.
2. For resistors, compare resistance, tolerance, package, power, voltage rating,
   and any application-specific requirement. For capacitors, compare capacitance,
   tolerance, package, rated voltage, dielectric, temperature behavior, ESR, and
   polarity where relevant. Apply equally careful criteria to inductors, diodes,
   connectors, and IC support parts.
3. Reuse means duplicate the existing device identity or place the same exact
   library item, then give the new physical instance its own designator. The BOM
   should increase the quantity of the existing material instead of introducing
   another supplier/manufacturer part line.
4. A physical circuit addition still needs a new reference designator and raises
   quantity. Never pretend that reusing a SKU eliminates the required component.
5. Do not reuse a part merely because the nominal value matches. If any required
   rating is insufficient or uncertain, select a suitable new part and explain
   why a new BOM material is justified.
6. After placement and save, re-read the new instance and compare it with the
   source component. Verify value, footprint, manufacturer part, supplier part,
   BOM inclusion, and PCB inclusion. Repair blank or degraded properties before
   declaring completion.

## Working method

1. Verify the active EasyEDA window, project, document type, connector/bridge
   health, and version compatibility before mutation.
2. Inspect the relevant components, pins, nets, existing reusable parts, nearby
   geometry, and the pre-change DRC baseline.
3. Preserve the intended pin-to-net topology. Use explicit net names for intent
   that must be reconciled later.
4. Make the smallest sufficient change, save it, visually inspect the edited
   region, export/read back the netlist, and rerun DRC or the applicable strict
   gate. A screenshot supports readability review but never replaces structural
   and netlist checks.
5. Compare post-change findings with the baseline. Do not attribute pre-existing
   warnings to the new work, and do not conceal newly introduced warnings.
6. For schematic-to-PCB changes, explicitly report every new or replaced
   footprint and the PCB placement, board-edge, mechanical, and routing work that
   remains. Do not silently sync or rearrange a PCB unless the request includes it.

## EasyEDA 3.2 safeguards

- On versions where programmatic NC-marker changes are unreliable, verify pin
  coordinates and `noConnected` by readback. Use the EasyEDA UI to place the NC
  marker when the API does not persist it, then save and rerun DRC.
- A library-placement update prompt is not permission to update all existing
  project devices. Keep the current project-library version unless Light asked
  for a library update.
- Saving can expose blank value or supplier fields on a newly placed project
  instance. Always perform the post-save component identity comparison required
  by the personal component policy.

## Design decisions and authorization

- Ask before destructive clears, bulk replacements, changing board stackup, or
  making a mechanical choice that affects connector edge, orientation, enclosure,
  mounting, or fabrication.
- For an ambiguous electrical choice, present the relevant alternatives and a
  recommendation. A safe conventional default may be applied only when it is
  well supported and does not remove a user-visible capability.
- For edge connectors and user-facing controls, do not finalize PCB placement
  without confirming the required edge and facing direction.

## Completion report

State the changed designators, exact manufacturer and LCSC part numbers, reused
source components, pin-to-net proof, save status, DRC/gate result, and any manual
or PCB work still required. When a reused item is involved, say whether the BOM
adds only quantity or introduces a new material type.
