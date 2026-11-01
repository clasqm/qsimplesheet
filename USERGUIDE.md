# QSimpleSheet User Guide

QSimpleSheet is a small, single-sheet spreadsheet. You already know what a
spreadsheet is: a grid of cells arranged in numbered rows and lettered columns,
where cells can hold data and formulas that compute from other cells. This guide
explains how QSimpleSheet specifically expects data, labels and formulas to be
entered, which formula functions are built in, and what every menu item does.

Documents are saved as `.qsd` files. You can also pass a file on the command
line (`qsimplesheet mysheet.qsd`) to open it at startup.

---

## 1. Entering data

### Numbers

Click a cell (or move to it with the arrow keys), then double-click it — or
press **F2** — to edit it. Type the number and press **Enter** to commit, or
**Esc** to cancel.

There is no separate "number" type: every cell simply stores the text you
typed, and it is displayed exactly as you entered it (no automatic rounding or
thousands separators). Numbers are recognized automatically wherever formulas
need them, so typing `42` in A1 and `3.5` in A2 is all it takes for
`=SUM(A1:A2)` to return `45.5`.

Valid number forms inside formulas: `123`, `123.123`, `123E+123`,
`-42`, `+7`. Plain cell text like `12abc` is treated as zero when a formula
reads it.

### Text labels

Type any text and press **Enter**. Text that does not start with `=` is stored
and displayed literally — no quoting is needed. Cells are left-aligned,
black-on-white by default (see Format menu to change this).

### Formulas

A cell contains a formula if its text starts with **`=`**:

```
=A1+B1
=SUM(A2:A10)
=B4*12.5
=IF-free expressions only — see the function list below
```

The cell *displays* the computed result; the *formula* stays visible in the
wide box of the formula toolbar (second toolbar row) whenever the cell is
current. To edit the formula itself, edit the cell directly (double-click /
F2) — editing always shows the raw text, not the result.

Key rules:

- **Everything recalculates automatically.** Whenever any cell changes, every
  formula that depends on it is recomputed on the next redraw, so results
  update live as you work.
- **Formulas are not adjusted when copied or pasted.** A pasted formula is
  taken verbatim, so `=A1+B1` pasted elsewhere still refers to A1 and B1.
- **To display a formula as literal text** (for documentation, say), prefix it
  with `/=` instead of `=`: typing `/=SUM(A1:A5)` shows the text
  `=SUM(A1:A5)` in the cell instead of computing it.
- **Errors are shown in the cell itself**, e.g. `Syntax error at position 7`,
  `Syntax error: Missing parenthesis`, `Error: Divide by zero`,
  `unknown function FOO`, or `Error: This formula makes an infinite loop`
  (raised when a formula refers back to itself through another cell).

### The formula toolbar

Below the main toolbar is a second toolbar with two boxes:

- **Current location** (left): shows the selection as a reference such as `B3`
  or `A1:C4`. Type a reference and press **Enter** to jump the cursor there (or
  select that range).
- **Formula** (right): shows the current cell's raw content. Type anything and
  press **Enter** to set the current cell's content — this is an alternative to
  editing the cell in place, and it is undoable like any other edit.

The **status bar** at the bottom shows `Sum = <result>` for the current cell
when it holds a formula (and `Sum = 0` otherwise).

### Data formats (Format → Data Format)

By default every cell uses the plain text-line editor. The Data Format submenu
switches the *editor* used when you edit a cell in the selection:

| Format | Editor you get when editing | Stored as |
|---|---|---|
| Text Line (default) | plain text field | exactly what you type |
| Time | time spin box (`h:mm:ss`) | `hh:mm:ss` text |
| Date | date picker with calendar popup (`dd/MM/yyyy`) | `dd/MM/yyyy` text |
| Integer | integer spin box (±9999999) | the number |
| Decimal | decimal spin box (3 decimals, step 0.1) | the number |

This changes how you *enter* the value, not how it is displayed. Date/time
text is not numeric — a formula reading it yields 0.

---

## 2. Formulas in detail

### Syntax

- Operators: `+` `-` `*` `/`, plus parentheses `(` `)` for grouping.
- Function and cell names are **case-insensitive** (`=sum(a1:a5)` works).
- Spaces are ignored inside formulas.
- Cell references: column letters then row number — `A1`, `Z9`, `AA12`.
- Ranges: `A1:B5` (endpoints in either order are accepted). A range is meant
  to be used as a function argument; used bare as a value, it evaluates to its
  top-left cell.
- Results are shown with default number formatting (about six significant
  digits).
- Circular references are refused with the "infinite loop" error rather than
  freezing the program.

### Built-in functions

All of these are available (also listed inside the program under
**Help → Function List**, F1). Trigonometry takes **degrees**.

**Aggregates** — arguments may be numbers or ranges like `A1:B1`; non-numeric
cells inside a range count as 0:

| Function | Meaning |
|---|---|
| `SUM(value,...)` | sum of all values/ranges |
| `PRODUCT(value,...)` | product of all values/ranges |
| `MIN(value,...)` | smallest value |
| `MAX(value,...)` | largest value |
| `AVERAGE(value,...)` | arithmetic mean |
| `MEDIAN(value,...)` | median |
| `MODE(value,...)` | most frequent value (ties → smallest) |

**Single-value functions:**

| Function | Meaning |
|---|---|
| `FABS(value)` | absolute value |
| `SQRT(value)` | square root (`nan` if negative) |
| `POW(value1, value2)` | value1 raised to value2 |
| `LOG(value)` | natural logarithm |
| `LOG10(value)` | base-10 logarithm |
| `SIN(value)` / `COS(value)` / `TAN(value)` / `COT(value)` | trig, in degrees |

**Information functions:**

| Function | Meaning |
|---|---|
| `PI()` | the value of π |
| `ROW()` | number (1-based) of this cell's row |
| `COLUMN()` | number (1-based) of this cell's column |

Examples:

```
=C7+D7
=SUM(C1:C24)
=AVERAGE(B2:B10)*1.1
=POW(SQRT(2), 2)
```

There is no conditional (IF) or string logic — QSimpleSheet is deliberately a
calculator over numbers.

---

## 3. Menus

Two toolbars exist: the **main toolbar** (top row) and the **formula
toolbar** (second row). The last column below says whether a menu item also
appears on the main toolbar.

### File

| Item | Function | On toolbar? |
|---|---|---|
| New (Ctrl+N) | Start a new sheet; asks for its size (rows × columns, default 10×10) | **Yes** |
| Open (Ctrl+O) | Open a `.qsd` file (or any file) | **Yes** |
| Save (Ctrl+S) | Save to the current filename; Save As if there is none | **Yes** |
| Save As (Ctrl+Shift+S) | Save under a new name; `.qsd` is appended if missing | **Yes** |
| Exit (Ctrl+Q) | Close the application (prompts to save unsaved changes) | No |

If the document has unsaved changes, the title bar marks it with `*` and you
are prompted (Yes/No/Cancel) before losing them.

### Edit

| Item | Function | On toolbar? |
|---|---|---|
| Undo (Ctrl+Z) | Undo the last operation (edits, cuts, pastes, inserts, formatting, sorts, image moves…) | **Yes** |
| Redo (Ctrl+Y) | Redo the undone operation | **Yes** |
| Copy (Ctrl+C) | Copy the selected cells (formatting included) to the clipboard | **Yes** |
| Cut (Ctrl+X) | Copy the selection and clear it | **Yes** |
| Paste (Ctrl+V) | Paste at the current cell — refuses if it would run past the sheet's edge | **Yes** |
| Find… (Ctrl+F) | Open the Find dialog (see below) | No |
| Find Next (F3) | Find the next occurrence | No |
| Find Previous (Shift+F3) | Find the previous occurrence | No |
| Delete (Del) | Clear the contents of the selection (rows/columns stay) | No |
| Delete Entire Row(s) (Ctrl+Del) | Remove the selected rows entirely; lower rows shift up | No |
| Delete Entire Column(s) (Ctrl+Shift+Del) | Remove the selected columns entirely; right columns shift left | No |
| Select All (Ctrl+A) | Select the whole sheet | No |
| Select Current Row | Select the row of the cursor | No |
| Select Current Column | Select the column of the cursor | No |

Notes on clipboard behavior: copying between QSimpleSheet cells preserves
their formatting and formulas; pasting *plain text* copied from another
program splits it on tabs and newlines into a block of cells. Formulas are
pasted as-is (references are not renumbered). Cutting or deleting a selected
image behaves the same way as for cells and is also undoable.

**Find dialog:** enter the text, and choose *Case sensitive*, *Find
backwards*, and/or *Find in formula*. "Find in formula" searches the raw cell
text (so you can find `SUM(` even inside a cell that displays a number);
otherwise the search matches what the cell displays. The search wraps around
the whole sheet.

### Insert

| Item | Function | On toolbar? |
|---|---|---|
| Insert Rows (Ctrl+Shift+R) | Insert a number of rows at once, before or after the current row | No |
| Insert Columns (Ctrl+Shift+C) | Insert a number of columns at once, before or after the current column | No |
| Insert Image… (Ctrl+I) | Load an image file and drop it onto the sheet at the current cell | No |

**Working with images:** drag an image to move it; drag its edge or corner to
resize it; double-click it (or right-click → **Properties…**) to set its size
exactly in pixels or percent, restore the original size, or lock the aspect
ratio. Right-clicking an image also offers Copy/Cut/Paste/Delete. Images float
above the grid at fixed pixel positions, are saved inside the `.qsd` file, and
the **Delete** key removes the selected image.

### Format

| Item | Function | On toolbar? |
|---|---|---|
| Data Format ▸ Text Line (default) | Plain text editor for the selected cells | No |
| Data Format ▸ Time | Time spin box editor | No |
| Data Format ▸ Date | Date editor with calendar popup | No |
| Data Format ▸ Integer | Integer spin box editor | No |
| Data Format ▸ Decimal | Decimal spin box editor (3 decimals) | No |
| Font… | Change the font of the selected cells (font dialog) | **Yes** |
| Align Left | Left-align the selection (exclusive; shows current alignment as checked) | **Yes** |
| Align Center | Center the selection | **Yes** |
| Align Right | Right-align the selection | **Yes** |
| Background Color | Fill the selected cells with a color | **Yes** |
| Text Color | Recolor the text of the selected cells | **Yes** |

Alignment is vertical-center + horizontal-left/center/right. For a
multi-cell selection the alignment checkmarks are cleared until a single cell
is selected again.

### Tools

| Item | Function | On toolbar? |
|---|---|---|
| Sort | Sort the selection (see below) | No |

**Sort dialog:** available when the selection spans at least two rows or
columns. Choose up to three sort keys (*Sort by* / *Then by* / *Then by*),
each ascending or descending — columns when sorting rows, rows when sorting
columns. Options: *Sort Rows* vs *Sort Columns*, *Case Sensitive*, and *Use
custom order*, which sorts by your own comma-separated list instead of
alphabetically; presets include weekdays, months, and number words
(`Zero, One, Two, …`). Sorting is stable (equal keys keep their order) and
undoable.

### Options

| Item | Function | On toolbar? |
|---|---|---|
| Show Grid (Ctrl+G) | Toggle the cell grid lines | No |

### Help

| Item | Function | On toolbar? |
|---|---|---|
| Function List (F1) | Open the built-in reference for all formula functions | No |
| About QSimpleSheet | Version, license (GPL) and author credits | No |
| About Qt | The standard Qt about box | No |

---

## 4. Right-click context menus

**On the grid:** a *Cell Format* submenu (the whole Format menu), Undo, Redo,
Copy, Cut, Paste, Delete, Delete Entire Row(s), Delete Entire Column(s), and
Show Grid.

**On an image:** Copy, Cut, Paste, Delete, and Properties…

---

## 5. Keyboard shortcut summary

| Shortcut | Action |
|---|---|
| Ctrl+N / Ctrl+O | New / Open |
| Ctrl+S / Ctrl+Shift+S | Save / Save As |
| Ctrl+Q | Exit |
| Ctrl+Z / Ctrl+Y | Undo / Redo |
| Ctrl+X / Ctrl+C / Ctrl+V | Cut / Copy / Paste |
| Del | Delete contents (or selected image) |
| Ctrl+Del / Ctrl+Shift+Del | Delete entire row(s) / column(s) |
| Ctrl+F, F3, Shift+F3 | Find, Find Next, Find Previous |
| Ctrl+A | Select all |
| Ctrl+Shift+R / Ctrl+Shift+C | Insert row(s) / column(s) |
| Ctrl+I | Insert image |
| Ctrl+G | Toggle grid |
| F1 | Function list |
| F2 | Edit the current cell |

---

## 6. Files at a glance

- Format: native `.qsd` (binary); documents saved by older QSimpleSheet
  versions open unchanged.
- New/Open/Save all prompt before discarding unsaved changes.
- Sheet size is fixed when the sheet is created (New dialog); inserting
  past the edge is simply not possible, so paste failures mean the target
  would not fit.
- Every structural change — content edits, row/column insert and delete,
  formatting, sorts, image placement — is undoable with Ctrl+Z.
