# 🕯️ Loomlight — A graphical branching narrative editor, compatible with [Twine](https://github.com/klembot/twinejs)'s Twee format

_Loomlight_ is a single-file, browser-based editor for writing, mapping out, and playing branching narrative stories: interactive fiction, choose-your-own-adventure stories, visual-novel plots, TTRPG campaign arcs, or any story that forks.

A user can outline their story as a map of connected scene nodes. Each scene is a card on an infinite canvas; arrows between cards are the choices the reader can take. There's a persistent worldbuilding library (characters, places, items, variables, lore, narrative voices), then click Play to walk it exactly as a reader would — with live variables, conditions, and inventory.

Everything runs locally in the browser. No install, no server, no account, no cloud — your story never leaves your machine.

1 · Setup
 
 1. Get the file — Loomlight is one self-contained .html file (e.g. loomlight.html).
 2. Open it — double-click it, or open it via your browser's File → Open. Any modern browser works (Chrome, Edge, Firefox, Safari).
 3. That's it. A small sample story loads on first run so you can explore immediately. Edit or delete it and start your own.
**Where is my data? — and how is it protected? **Your story **autosaves to your browser's local storage first **after every change — the ✓ autosave chip confirms each save; it warns ⚠ autosave off if storage is blocked or full, and shows ⏸ autosave paused while another tab owns the session. Writes are debounced (~300 ms), so a burst of typing costs one save, not one per keystroke; closing or hiding the tab always flushes immediately.
    • Versioned save format — saves carry a format version (v2), so future Loomlight updates can migrate old saves instead of losing them.
    • Safety backup ring — every 20th save, a story-only snapshot rotates into five backup slots (loomlight.backup.1 … .5). If the primary save (loomlight.project.v1) is ever unreadable, Loomlight recovers your story from the newest backup and re-saves it automatically.
    • Multi-tab lock — opening the story in two tabs is the classic approach in which a browser-based editor’s work gets overwritten: The first tab owns a session lock (loomlight.lock.v1); a second tab is warned at startup, and only the lock holder writes. A stale lock (crashed tab) is reclaimed automatically after ~6 seconds.
    • Save on hide — changes persist not only on exit, but the moment the tab is hidden (mobile browsers can kill tabs without warning).
Layout, zoom level, and the snap toggle are restored on reload (theme lives in loomlight.theme). Local storage is still tied to this browser on this machine — export a .json backup regularly, especially before switching browsers or clearing site data.
Recommended rhythm: sketch → ✓ Check → play-test → export.

2 · The workspace at a glance
Area	What it does
Toolbar (top)	Story title & author, undo/redo, ＋ Node, ✧ Tidy (auto-layout), ✓ Check, ▶ Play, ⇩ Export, ⇧ Import, 📂 Open, 💾 Save, 💾⭳ Save as…, ? Help, view tabs ▦⏱⊞◫▤▥📇📊, ◐ theme, zoom controls
Sidebar (left)	☰ Scenes — every scene in the story, searchable · 📚 Library — the worldbuilding database · 🗺 Arcs — named scene groups, each listed in timeline order, with add / rename / delete and an optional owning character
Map (center)	The free-form canvas: scene cards, arrows, and labels
Inspector (right)	Fields of whatever is selected — scene, library entry, or connection
Status bar (bottom)	Node/link counts, word count, autosave chip

3 · The map — core gestures
Gesture	Effect
Drag empty space	Pan the map (mouse wheel zooms toward the cursor)
Zoom out far	Zoom-adaptive cards — past ~55% zoom every card collapses into a compact capsule (color dot + title + type icon) so dense story forests read at a glance, edge labels fold away, and edges/arc frames re-anchor to the collapsed shape; zoom back in and full detail returns
Double-click empty space	Create a new scene there
Drag a node header	Move the node (toggle the snap switch bottom-left to align to grid)
Drag the round port (node's right edge)	Draw a connection — drop it on another node, or on empty space to spawn a new, already-linked scene
Click an edge or its label chip	Select the connection — then label, condition, reverse, or delete it
Double-click an edge / its chip	Inline label editor floating on the curve (Markdown supported) — Enter saves, Esc cancels
Right-click a node / the map	Context menu: set as start, duplicate, add connected scene, play from here, delete, and more
Minimap (bottom-right)	Bird's-eye dot view of the whole map — the accent rectangle is your current viewport. Click anywhere on it to jump there, or drag to sweep across the story. Hidden on narrow screens
Ctrl/Shift + click nodes	Multi-select — build a selection of any size, then drag to move them all, apply one color, or delete them in one step
Shift + drag empty space	Rubber-band selection box (nodes light up as the box covers them) — Ctrl + drag extends the current selection instead of replacing it

Renaming a scene automatically updates every [[link]] that points at it — all link syntaxes ([[Target]], [[label->Target]], [[Target<-label]], [[label|Target]]) are rewritten in every body that references the old title.

4 · Node types
Every scene is one of 12 types. Types are defaults and looks — all nodes share the same fields, and only Play mode treats them differently.
Type	Use
Narrative	The workhorse — prose scenes with exits
● Player	Narrator text shown to the reader; inline [[links]] become plain text, so onward paths are drawn connections · has the ⚡ Action sub-type
💬 NPC	Character dialogue — pick a speaker from the library; their color tints the header and play page
⊞ Choice	Decision hub — each choice leads to another scene
◈ Hub	Pure branching/merging point — many links in, many out; act labels (e.g. INCITING INCIDENT) show as styled chips while its body is empty
⚡ Action	Modifies state — e.g. gold += 10, or rolls dice into a variable (roll = 2d6 🎲) — then follows the first exit
⚙ Condition	if/else branch — set an expression like has_key == true, then pick where each outcome goes
↪ Jump	Continue at another scene — by tag, id or exact title (the inspector's “jump by name” field takes #tag or a title, and renaming a scene rewrites title-based jump targets). Chapter transitions, fast-forwards
◆ Ending	Terminal outcome — Play mode marks it "The End"
✎ Note	Author-only comments — kept out of the word count and the reader flow
▮ Text	Ordinary free-form prose — [[links]] work as choices, same as Narrative
▤ Exposition	Narrator descriptions, stage directions, environmental storytelling — [[links]] work as choices; assign a Narrative Voice to tint and label the scene · has the ⚔ Encounter sub-type

Type accents. Every card carries a structural cue: ✎ Note looks like a sticky amber Post-it (slightly tilted — it straightens when you hover it), ● NPC and ◈ Choice have a 4px left stripe in the speaker's / type color, and ▶ Player has a 4px top stripe plus a serif prose body that mirrors Play mode. A custom color — or an NPC's speaker color — re-tints the stripe automatically.
Sub-types — per-type extra fields
Some types offer an optional sub-type in the inspector: a template that attaches extra fields (pre-filled with defaults) and a default tag, both fully rewritable. Picking one never overwrites values you've already entered.

Sub-type	Extra fields	Default tag

⚡ Action (on Player)	Danger level — Low / Moderate / High / Deadly (defaults Moderate) · Stakes — free text (what the player risks) · Opposition — free text (who or what stands in the way)	danger

⚔ Encounter (on Exposition)	Threat — None / Low / Moderate / High (defaults Low) · Exploration focus — free text (what the reader can find, notice, or learn) · Reward — free text (discovery, loot, insight, ally)	exploration

The node card shows a sub-type chip (⚔ Encounter / ⚡ Action) whose tooltip lists the current field values, and tags appear as chips on the card foot. Tags and field values persist with the save and round-trip through Twee export/import — as passage metadata and Twine-style [tags] alike.

5 · Common fields & the inspector
Select a scene to edit it in the inspector:

• Title — the scene headline

• Tags — one or more single-word slugs, space-separated (e.g. ch3-tavern dream), exported as a Twee 3 tag block [ch3-tavern dream]. The first tag is the primary: unique per story and used as a jump/choice target so links survive retitles; the tags after it are free grouping labels. All of them show as #chips on the card head and in the Timeline, and the Sheet view's Tag column edits the whole space-separated list

• Cover image — optional scene art: an image URL (hosted, keeps the project file small) or a local file picked via 📁 (embedded as a data:URI — fine for a few, heavy for many). Renders as a banner under the scene title on the map, a backdrop in Play mode and exported HTML, and a thumbnail in the Arc & Timeline views. Never ships into the playable text; remove with ✕.
    
A single-file app can't bundle binaries, so images live as URLs/data-URIs inside the JSON.
    
• Arc — a named scene group picked from a drop-down that sits right below the Markdown text; scenes sharing an arc are listed together in the 🗺 Arcs sidebar tab (in timeline order) and carry a colored ⌒ chip on cards and in the arc view. Create, rename or delete arcs in that tab — and optionally give an arc an owning character (their color tints its dot), turning it into that character's personal arc

⌒ Arc frames — grouping containers on the map
Inspired by how code editors frame related blocks on one canvas: every arc with two or more scenes is wrapped in a tinted frame directly on the Graph view, so the map reads structurally at a glance instead of only via ⌒ chips.

• The frame is tinted and titled with the arc's name, scene count and color (the owning character's color, if the arc has one).

• Drag the frame (its border or title bar) to move all of its scenes as a group — one undo step, edges follow live, snap-to-grid respected.

• － collapses the arc into a compact pill (its scenes tuck away, their edges hide — decluttering big maps); ＋ expands it back. Collapsed arcs stay collapsed across reloads; jump to one of its scenes from anywhere (sidebar, search, Char view) and it auto-expands.

• The ⌒ Frames toolbar button hides or shows all frames; frames render behind nodes and never intercept node clicks, edge clicks or ports.

• Collapsible per arc also from the 🗺 Arcs sidebar tab (the －/＋ button on each arc row).

• Body — Markdown prose (the authoring surface; see §8)

• Character — who the scene belongs to; sits directly below the Markdown text, and matching a library character tints the header in their color — a 

Character details row underneath edits their location & temperature
• Date / time — free-form; the Timeline view sorts by it (years 1800–2099 auto-detected)

• Beat — the story beat on the classic dramatic arc: a dropdown of canonical beats (Opening, Inciting incident, Rising action, Midpoint, Crisis, Climax, 

Falling action, Resolution, Denouement, Backstory) or free text. A colored dot previews it next to the field, and the beat shows as a color-coded chip on the card foot (colored by the canonical palette — custom beats get a stable generated color). The Sheet view's Beat column uses the same dropdown
    
• Entry requirement ⚔ — optional condition on any scene: the reader can only arrive while it holds. Every entrance — choices, [[links]], drawn connections, jumps — is gated at once, and the card carries a ⚔ chip. Unmet, the choice shows locked with its requirement instead of vanishing; the Reading order view prints ⚔ requires: on the page. Same syntax as connection conditions; empty = open to all
    
• Choices — Player · NPC · Choice · Hub · Action · Condition · Text · Exposition all get a choices list, rendered right below the Markdown text (after Arc, above Character): each row is a label, a target (tag, id or exact title) and an optional ⚙ condition (the choice is offered only while it holds), drawn as dashed arrows and offered in Play mode before map connections

• Choice tones 🎨 — every choice row also has an optional tone field: a free-text word for the option's personality (kind, warm, witty, bold, threat, cold — or any word, which gets its own stable color). It colors a dot on the Play-mode button and on the dashed arrow, so a branch's feel reads at a glance, Broken-Roads-moral-compass style; empty = no dot


• Location — where the scene takes place; picking a 📍 library location tints its chip and the Play-mode line

• Color — custom override of the type default, right below Location

• Scene fields — beat, act, pov, location, tension, status, chapter — editable in the Sheet view; they drive the Timeline / Arc / Beat / Stats views. location links a scene to a 📍 library location (also set in the Inspector): its color tints the card's 📍 chip and the Play-mode line, and renaming the location follows the scene. Location fields (scene Inspector, character Inspector / library panel, new-character form) show a suggestion dropdown of existing locations and auto-add committed new names to the 📍 Locations library

• Narrative Voice (Exposition) — pick or quick-add a 🎙 voice; its color tints the card and labels the scene in Play mode

• Sub-type + Tags (Player / Exposition) — see §4

Connections can each carry a Markdown label (double-click the edge) and an optional condition — the reader only gets that path while the condition holds. Variable conditions show a ⚙ badge on their chip; @Character Attribute skill checks (e.g. @The Doorkeeper Patience >= 7) show a ⚔ badge and read the character's attribute grid; dice conditions (e.g. playerStr > d20) show a 🎲 badge and re-roll on every evaluation. Two optional play-mode flags (per connection, in the inspector) complement conditions:

• ↻ Persist — the choice stays available after the reader takes it, marked “↻ visited”. Meant for “exit to lobby” navigation links.

• ⚡ Once — the choice fires only once per playthrough, then hides itself. Meant for one-shot secrets.

Inline [[links]] can be gated in the prose itself: append {{when:cond}} inside the brackets — [[Speak kindly->Target {{when:has_key}}]] is offered as a choice only while the condition holds (same syntax as connection conditions, incl. ⚔ skill checks). Author views mark gated links with a dashed ⚔ chip, and renaming the target scene rewrites the link while keeping its gate.

Both reset on every new playthrough, show as ↻ / ⚡ badges on the map chip, and round-trip through .json export/import. When several scenes are selected, the inspector becomes a batch panel — apply one color to all of them, delete, or clear the selection.

6 · The Library 📚
The persistent database for worldbuilding: independent of any scene, it travels with saves and exports.
Category	What it holds

💬 Characters	Name, color, role, portrait initials, description, goals, flaw, abilities, an attribute grid (per-character stats like Courage: 8, readable from any condition as @Name Attribute), traits (cross-character tags — see below), a Main / Secondary / Casual importance category, and an Encounter field that can @mention other entities

📍 Locations - Places — name, color, free-form links to other places

🎒 Items - Player-inventory objects — the present flag (default on) controls whether {{when:Item name}} blocks see the item; untick to remove it from the starting inventory

🔢 Variables - Numeric or string values seeded into every playthrough — they drive Condition nodes, connection conditions, and {{when:}} blocks. Each variable also carries a human label (what it means, shown in the library) and an optional group ("Main story", "Side quests", "Secrets"…): grouped variables appear under their own folder headers in the library, with the rest listed directly below the section head. Labels and groups are authoring aids only — play logic always reads the technical name. A row of type chips (All · Number · Text · Boolean · Empty, with live counts) filters the variable list by inferred value type

🌍 Worldbuilding - Free-form named entries (history, geography, culture, magic…) with renameable, reorderable name/value field pairs

🎙 Narrative Voices - The "who is telling" — narrators, chroniclers, in-world documents. Assign one in an Exposition scene; its color tints the card and labels the scene in Play mode

Select any entry to edit it in the inspector — or right-click it for Rename (F2), ⧉ Duplicate and ✕ Delete (see §10). The 📇 Char view prints every character profile as a card — handy for TTRPG session handouts.

7 · Ten views — to see one story
Switch with the toolbar tabs or their keys. All views read the same scene fields — every change applies everywhere.

View	Key	What it shows
▦ Graph	G	The free-form canvas (default). Arcs with 2+ scenes are wrapped in a tinted, titled frame (see § Arc frames)

⏱ Timeline	T	Chronology rows auto-arranged into eras by year; ↑/↓ buttons reorder within a year

⊞ Sheet	S	Every field of every scene in one spreadsheet — click any cell to edit; ⇩ CSV / ⇩ JSON export the rows

◫ Outline	O	Radial mind-map of the library — characters inner ring, locations/items/variables outer; drag cards, positions persist

▤ Arc	A	Chapters × scenes on a beat-colored strip with detail cards

▥ Beat	B	Per-scene table: # / Title / Beat / Act / POV / Date / Tension (0–10 bar) / Words / Status

📇 Char	C	Printable character profile cards, two per row — each card lists the scenes the character takes part in (speaker, POV, or @mention), clickable to jump to the map

📜 Script	R	A linear, read-only read of the whole story in timeline order — body text, spoken dialogue cues, screenplay quotes, choice lists (▸ label → target) and Go to: jumps, all in one scroll. Click any scene card to open it on the map

📖 Reading order	E	The story as the reader will meet it — a numbered, gamebook-style walk from the start scene, through every exit (choices, connections, [[links]], jumps, condition branches); each page cross-references the page its exits lead to, and scenes no exit reaches are listed at the bottom. ▶ Run plays from the start; ⇅ Re-sort restamps the timeline order with this reading order

📊 Stats	P	Summary cards, cumulative words-over-time line chart, words-per-scene bar chart

🏷 Traits — a cross-character vocabulary
Besides the per-character attribute grid, every character has a Traits list in the inspector: free-text tags like shy, energetic, loyal, angry. Traits form a shared vocabulary across the whole character cast — typing the same trait on several characters automatically links them for easier filtering:

• The 📇 Char view shows a Trait groups bar above the cards: every trait used anywhere, with the number of characters carrying it.

• Click a tag to filter the profile grid down to everyone who shares that trait (matching is case-insensitive, so Shy and shy group together); click it again — or the highlighted tag — to clear.
• Each card also shows its own trait chips; clicking one filters by that trait straight from the card.

• The inspector's trait input offers auto complete from traits already used by other characters, keeping spelling consistent; Enter or ＋ adds, clicking a chip removes it.
Attributes stay exactly what they were — a private label → value stat table per character. Traits are the associative layer: free words that group characters instead of measuring them.

8 · Writing syntax
Markdown
Syntax	Result
# H1 … ###### H6	Headings
**bold** · *italic* · ~~strike~~	Emphasis
code · triple backticks	Inline / fenced code
* item · 1. item	Lists
> quote	Blockquote
---	Horizontal rule

Links (Twine-style) — these are the branches
Syntax	Result
[[Target]]	Choice leading to scene Target
[[Speak kindly->Target]]	Labelled choice to Target
[[Speak kindly\|Target]]	Same, pipe syntax
[[Target<-Speak kindly]]	Same, backlink syntax (target first)
[[Speak kindly->Target {{when:has_key}}]]	Gated choice — offered only while the condition holds

Text links and hand-drawn map connections are both offered as choices when playing. On Player nodes, inline links render as plain text — only drawn connections lead onward.
Mentions & conditional prose
Syntax	Result
@The Doorkeeper	A color-tinted chip for any library character, location, or item
{{when:has_key == true}}…{{/when}}	Inline conditional block — shown to the reader only while the condition holds (author views always display it, with a dashed outline marking the gate)
{{said:tag}} / {{said:tag\|fallback}}	Inventory echo — prints the label of the choice the reader took to reach that scene, looked up by its tag, id or title; until that choice is taken, the optional \|fallback text shows instead (author views mark the token with an amber dashed outline)

Story variables (Action & Condition nodes)

Syntax	Result
gold += 10 / gold -= 5	Add / subtract (concatenates for text)
has_key = true · set hp = 3	Assign — true, false, numbers, "quoted text"
has_key == true · gold >= 10 · !has_key	Conditions: == != > < >= <=, bare truthiness, not
@The Doorkeeper Patience >= 7	Skill check — reads a character's attribute grid (case-insensitive names; a missing character or attribute reads as unmet). Works in connection conditions, Condition nodes, {{when:}} blocks, entry requirements, per-choice gates and gated [[links]]
present(The Doorkeeper) · here(Name)	Presence check — is this character on stage right now? Arriving at a scene marks its Character (or an NPC scene's speaker) present; Action effects can set or clear it (below). Unknown names read as unmet, and the Problems panel warns about them
present(The Doorkeeper) = true / = false	Presence effect (Action nodes; set prefix works too) — put a character on stage or take them off, for later present() gates. The trace logs 👤 +Name / 👤 −Name
roll = 2d6 · set roll = d20+3 · hp -= d4	Dice roll — Action-node effects roll NdM notation and store the result (trace shows ⚡ roll=7 🎲)
playerStr > d20 · d20 >= 15 · 2d6+1 > roll	Chance conditions — dice roll fresh on every evaluation (each gated edge check, Condition node pass or {{when:}} render is a new roll); malformed notation reads as unmet and the Problems panel warns
Item name in a condition	Tests inventory presence (library Items' present flag)

Screenplay formatting
Wrap a block in <screenplay>…</screenplay> (e.g. at the top of an NPC body) to render it as formatted screenplay — INT. / EXT. slug lines are bolded.
Quote-style dialogue
Wrap a line in triple quotes to mark spoken dialogue — '''KILLER: Shana?'''. The text before the first colon becomes the speaker, shown as a small uppercase accent above the quote; when the name matches a library character, the accent is tinted with that character's color. A colonless line ('''Is anyone there?''') renders as a bare quote. Consecutive quote lines group into one block, and the usual inline syntax — [[links]], {{when:}}, {{said:}}, @mentions — works inside the spoken text.

9 · Play mode ▶
Press ▶ Play or Ctrl+P to start from the story's start node (set one via the right-click context menu — Play won't start without it). Right-click any node to play from it instead.
    • Choices appear as clickable links; both [[text links]] and drawn connections are offered (list choices first). A ↻ persist connection stays available after you take it (marked “↻ visited”, dashed border); a ⚡ once connection disappears after its single use. Choices into a scene whose ⚔ entry requirement fails render locked with the requirement shown; a gated scene's own page prints ⚔ requires: … with its met status
    • Every taken choice is remembered — later {{said:tag}} tokens in scene text echo back the exact label the reader picked to reach that scene (see §8), so the prose can reflect the reader's own words
    • Live state chips under the page show the current variables 🧩, the characters on stage 👤 (a roster of everyone marked present so far — named by their scenes, testable with present(Name)) and a route trace ⚙ effects, ⚑ conditions, ↪ jumps, 🎲 dice rolls (Condition nodes log each roll, e.g. 🎲 d20 → 14)
    • Choice tones carry into Play mode — a choice with a 🎨 tone shows a colored dot before its label, so tone-coded branches stay color-coded for the reader too
    • Screenplay dressing — each page opens with a SCENE slug (location + date in caps, e.g. SCENE: THE HALL — SPRING 1893, tinted with the location's library color) and the scene's character appears as a colored name pill above the prose. The exported HTML twin renders the same slug and pill
    • ⚡ Action nodes silently apply effects; ⚙ Condition nodes route by their expression; ◆ Endings mark "The End"; ↻ Restart clears variables, visited flags and choice echoes
    • Close with the ✕ button or Esc

10 · The shortcuts
Key	Action
N	New node
F	Fit map to view
Arrow keys	Nudge the selected scene(s) — Shift = 10px
Ctrl/Shift + click	Add / remove a node from the selection
Shift + drag empty space	Rubber-band selection · Ctrl + drag extends it
+ / −	Zoom in / out
G T S O A B C P	Jump to Graph / Timeline / Sheet / Outline / Arc / Beat / Char / Stats view — press the same key again to return to Graph
Ctrl+P	Play from start
Ctrl+F	Find scenes — live search over title, body, tag, beat, POV and character; non-matching cards dim away, Enter / Shift+Enter cycles matches, Esc clears
Ctrl+S	Save the project to disk — into the opened file, or a Save-as dialog with rename (Chromium); elsewhere downloads Project .json
Ctrl+O	Open a project file from disk (Chromium); elsewhere the classic import picker
Ctrl+Z / Ctrl+Shift+Z (or Ctrl+Y)	Undo / redo — 60 steps, rapid typing coalesced
Del	Delete the selected connection, the selected node (press Delete again to confirm), the whole multi-selection in one confirmed step, or the selected library entry (also confirmed twice)
F2	Rename the selected library entry — focuses its name field in the inspector
Esc	Close, in cascade: context menu → cancel link draft → close modal → close player → return to Graph from any view → clear selection

While typing in any input, single-letter shortcuts are suspended.
Library right-click menu
Right-click a library entry for ✎ Rename (F2) — focuses its name field, ⧉ Duplicate — a deep copy with a fresh id and " (copy)" name, and ✕ Delete (press twice to confirm, matching node deletes). Right-click a section header (e.g. Characters) to add a new entry of that kind. All three actions integrate with undo.

11 · Saving, export & import

📂 Open / 💾 Save / 💾⭳ Save as… work directly with a project file anywhere on your disk (Chromium browsers, via the File System Access API): Open picks a .loomlight.json, Save writes straight back into the opened file, and Save-as opens the OS dialog with a pre-filled name you can rename before choosing the folder. The opened file is remembered across sessions; in Firefox/Safari (and the preview sandbox) the buttons gracefully fall back to downloads and the import picker. ⇩ Export stays available for the other formats:
Format	File	What it's for
Project .json	story.loomlight.json	Full backup — round-trips perfectly via Import
Playable story .html	story.html	A single self-contained file with the play engine baked in — hand it to readers, host it anywhere, no Loomlight needed
Outline .md	story.md	Pandoc/Obsidian-friendly Markdown outline of the story tree
Twee	story.twee	Twine / Tweego format — node types, positions, colors, scene characters and locations, speakers, voices, sub-types and field values are preserved as passage metadata (:: headers), with scene tags exported as Twee 3 tag blocks ([a b c], space-separated — the first tag is the primary jump/choice slug, the rest are free labels, and all of them read back on import).

StoryData always carries an IFID — the Twine world's story fingerprint: a v4 UUID in uppercase (per the Twee 3 spec's compiler rule), minted on the first export and kept stable afterwards, so Twine / Tweego recognize the story across exports. The whole library (characters with their attribute grids and traits, locations, items, variables, worldbuilding, voices) and every drawn connection — with its condition, label and ↻/⚡ flags, plus each scene's ⚔ entry requirement, per-choice ⚙ gates and 🎨 tones — ride along in StoryData / passage metadata, so a Twee interchangeability round-trip keeps the story's logic intact (scene references use a tag only when exactly one scene carries it, otherwise the title). Conditions and effects use Loomlight syntax (has_key == true, @Character Attribute >= 7, roll = 2d6) and are stored as metadata, not Twine macros — Twine opens the file cleanly and other tools ignore the extra keys
Map image .png	story-map.png	Snapshot of the canvas — cards, bezier edges, choice labels and the start star, rasterized crisp (2× for small maps, capped at 4096 px) — for pitches, wikis and session notes
Map image .svg	story-map.svg	The same snapshot as an editable vector file
⧉ Copy project JSON	clipboard	Quick paste-anywhere backup

⇧ Import accepts both formats (detected automatically): .json (Loomlight project or bare story JSON) and .twee — Twee import resolves or creates characters and narrative voices, maps type aliases, and re-seeds sub-type field defaults. Import replaces the current story (one undo step can bring it back).

The ◐ Theme toggle switches dark/light and remembers your choice.
12 · Tools & good practice
 
 • ✧ Tidy — auto-layout the map in clean layers, starting from the start node
 
 • ✓ Check — consistency report: unreachable scenes, dead ends, broken links, unknown @mentions — each finding has a Show button that jumps to the culprit
 
 • Problems panel — the status-bar chip (bottom-left) shows a live error/warning count and opens a persistent, filterable problems list; click any finding to jump straight to the offending scene, and the list refreshes itself as you edit
   
 • Red problem, or warning dots — scenes with open problems carry a small red dot on the card corner, so you can spot trouble on the map at a glance
 
 • Undo is your safety net — 60 steps of everything (structure, text, library, colors)
 
 • Write → Check → Play → fix is the intended loop: run ✓ Check before trusting a chapter, and play-test every branch you add
 
Always version your drafts with Save-as on disk or .json export backups; keep the latest also as a playable .html for readers

Loomlight keeps everything on your local device: stories live in your browser's storage, exports are plain files for easy portability and backup, but nothing is ever uploaded anywhere by the app.
