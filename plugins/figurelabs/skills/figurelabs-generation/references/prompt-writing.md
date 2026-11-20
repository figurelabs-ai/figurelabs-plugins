# Writing FigureLabs prompts

A good prompt reads like a brief to a scientific illustrator. Include what applies:

1. **Figure type and purpose**: "graphical abstract for a journal submission", "schematic for a lab-meeting slide".
2. **Components**: every entity that must appear, with the exact label text (genes, proteins, cell types, instruments, groups).
3. **Relations**: arrows and what they mean (activates, inhibits, binds, flows to), order of steps, grouping into panels or compartments.
4. **Layout**: reading direction (left to right, top to bottom, circular), panels (A/B/C), what is central and what is peripheral.
5. **Emphasis**: the one message the figure must make obvious.
6. **Style and format**: color constraints (colorblind-safe, journal palette), mood, a journal's look. The drawing style and the aspect ratio have their own parameters (`style`, `ratio`); see the settings table in the skill.
7. **Reference files**: when the call carries a sketch or a reference image, what to do with it: follow its layout, match its style, or redraw it cleanly.
8. **Exclusions**: elements to leave out, text not to add.

Keep it factual. Do not add claims, results or numbers that are not in the user's material.

## From an abstract

Map the abstract to three to five panels: background or problem → approach or intervention → main finding → implication. Name the key entities and the one comparison or mechanism that carries the result.

Example:
> Graphical abstract, left to right in four panels. (1) "Chronic inflammation": macrophages releasing IL-6 in adipose tissue. (2) "Compound X": small molecule binding the IL-6 receptor, marked with an inhibition symbol. (3) "Reduced STAT3 signaling": pathway JAK → STAT3 with STAT3 faded. (4) "Improved insulin sensitivity": glucose uptake arrows into a muscle cell. Emphasize panel 3. No extra text.

With `style: "Flat"`, `ratio: "16:9"` and `language: "en"`.

## From Methods (flowchart)

List the steps in order with the actual parameters the user gave (durations, temperatures, n per group), mark decision points and branches, and name the endpoints. When the user has given the exact steps and connections, also pass them in `structure` (`nodes` with `id` and `label`, `edges` with `from`, `to` and an optional `label` such as "yes" or "no").

## For a data plot

Set `chart_style` when the chart type is one of its values, and state in the prompt which column goes on each axis, grouping and color by group, error measure (SD, SEM, CI) only if it is in the data, axis titles with units, and any requested ordering. Do not ask for statistics the data cannot support.
