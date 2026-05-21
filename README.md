# Tariff Doc Extractor Skill

A Claude skill for digitizing historical U.S. Schedule A tariff PDFs into structured Excel and CSV spreadsheets.

This repository packages an instruction file plus training data that teaches Claude how to read scanned Schedule A tariff documents and extract their tabular contents into a machine-readable output.

## Example

The `examples/` folder contains a sample input PDF and the spreadsheet Claude produces from it.

**Input:** `examples/1937_Schedule_A_Example.pdf` — 8 pages from Group 00 (Animals and Animal Products, Edible) of the 1937 Schedule A.

**Output:** `examples/1937_Schedule_A_Example.xlsx` — a `Tariff Data` sheet, one row per commodity. First rows:

| Economic Class | Schedule A Commodity Number | Commodity Description | Unit of Quantity | Rate of Duty | Tariff Paragraph |
|---|---|---|---|---|---|
| 2 | 0010 000 | Cattle: Weighing less than 175 pounds each | No......20; Lb.......1 | 2½¢ lb.; 1¼¢ lb. Canada | 701 |
| 2 | 0010 100 | Cattle: Weighing 175 pounds and less than 700 pounds each | No......20; Lb.......1 | 2½¢ lb. | 701 |
| 2 | 0012 000 | Sheep and lambs | No......20 | $3 each | 702 |

Hierarchical descriptions in the source PDF are flattened with `:` so each row is self-contained, and multiple values within a cell (rates, paragraphs) are joined with `;`.

## Requirements

- A Claude account with skills enabled. See the [Claude help center](https://support.claude.com/en/articles/12512180-use-skills-in-claude) for plan-specific setup.
- A Schedule A tariff PDF to digitize. The skill has been calibrated on the 1939 and 1950 editions and works on similar yearly schedules.

## Installation

1. Download `tariff-doc-skill.skill` from this repository.
2. In [claude.ai](https://claude.ai), go to **Customize → Skills** in the left sidebar and click **Add Skill** or **Upload Skill**.
3. Select the `.skill` file. It will appear in your skills list and become available in all conversations.

## Usage

1. Start a new conversation at [claude.ai](https://claude.ai).
2. Upload your tariff PDF via the attachment icon.
3. Ask Claude to extract it by using the `/tariff-doc-skill` command or with natural language.
4. Wait for processing — a few minutes, depending on page count.
5. Download the output files Claude presents.

**Tips:** Process one document per conversation for best results. For PDFs over 30 pages, split into smaller chunks. If you know the edition, tell Claude (e.g., *"This is a 1939 Schedule A"*). Always spot-check the output against the source PDF, which is included in the output for that purpose.

## Output Format

Each run produces:

- An Excel (`.xlsx`) workbook with three sheets: `Tariff Data`, `Metadata`, and `Footnotes`
- A matching CSV of the `Tariff Data` sheet
- A matching CSV of the `Metadata` sheet
- A copy of the original PDF for verification

### `Tariff Data` sheet

One row per commodity.

| Column | Description |
|---|---|
| Economic Class | Numeric code from the PDF's leftmost column |
| Schedule A Commodity Number | Normalized commodity number (e.g., `0010 000`) |
| Commodity Description | Full hierarchical description, flattened with `:` |
| Unit of Quantity | Reporting unit and code |
| Rate of Duty | One or more duty rates, joined with `;`; footnote markers preserved as superscript Unicode (¹, ², etc.) |
| Tariff Paragraph | One or more paragraph numbers, joined with `;` |

### `Metadata` sheet

A two-column key-value table (`Field`, `Value`) intended as a provenance record so downstream users can audit how the data was produced. Fields include:

- Source document identifiers (filename, title, edition, groups and sections covered)
- Processing statistics (pages processed, total rows extracted, extraction date, extraction method)
- Per-column transformation rules (e.g., how period notation was converted to the spaced `XXXX XXX` format)
- Notes on edge cases (bilateral bound rates, compound paragraph references, footnote-handling caveats, ALL CAPS section headers excluded as hierarchy resets)

### `Footnotes` sheet

Captures footnotes from the source PDF separately so they can be joined back to `Tariff Data` rows by marker.

| Column | Description |
|---|---|
| Page Number | Page in the source PDF where the footnote appears |
| Footnote Marker | Marker as printed in the source (e.g., `1`, `2`) |
| Footnote Text | Full text of the footnote |

The same marker may appear on multiple pages with different meanings, so joins typically need both `Page Number` and `Footnote Marker`.

## Troubleshooting

- **Claude doesn't recognize the skill** — Make sure you uploaded the `.skill` file (not just `SKILL.md`) through **Settings → Skills**. Refresh the page and start a new conversation.
- **Training data not found** — The training data must be inside the `.skill` file. If you edited and re-uploaded only `SKILL.md`, the training data was lost. Repackage the full `.skill` file with the `references/` folder.
- **Processing stops on a large document** — Split the PDF into 10–20 page chunks and process each in a separate conversation.

## Repository Structure

```
tariff-doc-skill/
├── LICENSE
├── README.md
├── SKILL.md
├── tariff-doc-skill.skill
├── examples/
│   ├── 1937_Schedule_A_Example.pdf
│   └── 1937_Schedule_A_Example.xlsx
└── references/
    ├── training_data.csv
    └── training_data.pdf
```

The `tariff-doc-skill.skill` file is a zip archive that bundles `SKILL.md` together with the `references/` folder — this is what users upload to Claude. The unpacked `SKILL.md` and `references/` are kept at the repository root so the canonical sources are easy to review and edit on GitHub.

The `references/` folder contains the training data Claude uses at runtime to calibrate extraction — description formats, commodity-number conventions, and tariff-paragraph styles. It must remain bundled inside the `.skill` file.

The `examples/` folder contains the sample input and output referenced in the [Example](#example) section.

## Modifying the Skill

To edit the skill manually:

1. Rename `.skill` to `.zip` and unzip it.
2. Edit `SKILL.md` in any text editor.
3. Re-zip the folder, keeping `references/` inside.
4. Rename the result back to `.skill`.
5. Upload the updated `.skill` file in **Settings → Skills** to apply the changes.

Alternatively, ask Claude to revise the skill in a conversation; it will produce an updated `SKILL.md` for download. You will still need to repackage the `.skill` file to include the `references/` folder.

## License

This project is licensed under the MIT License. See [LICENSE](LICENSE) for the full text.

## Credits

The historical Schedule A PDFs used as examples and training data are U.S. federal government works in the public domain (17 U.S.C. § 105). The example PDF was digitized by Google and made available via HathiTrust from a Penn State library copy.