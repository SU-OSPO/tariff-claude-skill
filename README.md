# Tariff Doc Extractor Skill

## Claude

Claude is an AI assistant powered by Anthropic. Users interact with it via a chat interface, where documents can be uploaded, questions can be asked, and files can be received back.

Skills are instruction files that teach Claude how to perform specific tasks. This particular skill teaches Claude how to read tariff document PDFs and extract their tabular data.

A Pro, Team, or Enterprise (available through Syracuse University Microsoft accounts) subscription is needed to use the skill feature, as the free tier does not support them.

## Purpose

The purpose of this Claude skill is to digitize scanned Tariff Documents, specifically Schedule A PDF pages, into structured Excel or CSV spreadsheets. This skill supports multiple yearly schedule editions and produces a machine-readable dataframe of the source documents' tabular data.

## Skill Layout

```
tariff-doc-skill/
├── SKILL.md
└── references/
    ├── training_data.csv
    └── training_data.pdf
```

The `references/` folder contains training data that Claude uses at runtime to calibrate its extraction, matching description formats, commodity numbers conventions, and tariff paragraph styles. The training data is bundled in the `.skill` file.

## Installation

- Download `tariff-doc-skill.skill` from this repository.
- In [Claude.ai](https://claude.ai), go to the left sidebar → Customize → Skills and click **Add Skill** or **Upload Skill**.
- Select the `.skill` file you downloaded. It should appear in your skills list and is now available in all conversations.

## Usage

- Start a new conversation at [claude.ai](https://claude.ai).
- Upload your tariff PDF using the attachment icon.
- Ask Claude to extract it (*"Digitize this Schedule A PDF"* or *"Process this 1950 tariff document."*).
- Wait for processing (a few minutes, depending on page count).
- Download the output files Claude presents.

**Tips:** Process one document per conversation for best results. For PDFs over 30 pages, split into smaller chunks. Tell Claude the edition if you know it (e.g., *"This is a 1939 Schedule A"*). Always spot-check the output against the source PDF, which is included in the output files for that purpose.

## Output Format

- Downloadable Excel (`.xlsx`) and CSV spreadsheets with one row per commodity
- A CSV containing the metadata
- A PDF of the original file for verification

## Editing the Skill

The skill zip file includes the packaged `.skill` file along with a `SKILL.md`.

To edit the skill via Claude: Ask Claude to revise the skill in a conversation, which will produce an updated `SKILL.md` to download.

To edit manually: Rename `.skill` to `.zip`, unzip, edit `SKILL.md` in any text editor, re-zip the folder, and rename it back to `.skill`. Make sure the `references/` folder stays in the zip (without it, Claude loses access to the training data).

Upload the updated `.skill` file to Claude's skill settings to apply changes.

## Troubleshooting

- **Claude doesn't recognize the skill** — Make sure you uploaded the `.skill` file (not just the `SKILL.md`) through Settings → Skills. Refresh the page and start a new conversation.
- **Training data not found** — The training data must be inside the `.skill` file. If you edited and re-uploaded only the `SKILL.md`, the training data was lost. Repackage the full `.skill` file with the `references/` folder.
- **Processing stops on a large document** — Split your PDF into 10–20 page chunks and process each in a separate conversation.

## License and Credits