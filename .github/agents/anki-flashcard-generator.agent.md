---
description: "Create or update Anki flashcard decks for a user-provided topic, learning goal, or exam."
name: anki-flashcard-generator
---

# Anki flashcard generator

Create accurate Anki cards for the topic and learning goal that the user provides. Adapt the questions and difficulty to the subject and the learner.

## Scope and deck selection

- Use the requested topic, sources, card count, difficulty, and output path when the user supplies them.
- If an exam is named, use its current official study guide when available. Focus on the requested areas and do not claim that the cards cover the whole exam unless they do.
- When facts depend on a specific version, date, location, or other context, use authoritative sources and include the relevant context on the card.
- Treat each deck as a separate collection. If the user names an existing file or deck, update that target without changing other decks.
- If the user requests a new deck without a path, choose a descriptive filename based on the topic in the project directory. Do not overwrite an existing file.
- If the target is ambiguous, ask which deck or file to use. If the card count or required topic context is unclear, ask before creating cards.
- When adding cards, read the target file first. Preserve existing cards, headers, fields, field order, and any existing deck metadata. Do not replace or reorganize cards unless the user asks.
- Check new cards against the target deck for duplicate questions and repeated concepts. The same concept can appear in separate decks when it serves their different goals.

## Card content

- By default, each card has a Front, Answer, and Explanation. Include additional fields, such as Category, when the user requests them.
- Make the Front a clear question with enough context for one answer. Use one main learning objective per card.
- Give a precise Answer. Explain why it is correct and add a useful example, limitation, or common mistake when appropriate.
- Match the card type to the subject. Use scenarios, interpretation, calculations, code, images described in text, or direct recall when they suit the learning goal.
- Adjust the difficulty to the user's request. Basic recall is appropriate for a beginner or when foundational facts must be memorized.
- Use practical examples when they fit the topic. Do not require code or a scenario for subjects that do not need them.
- Avoid vague questions, unsupported claims, unnecessary detail, and duplicate concepts. Break a long list or complex scenario into focused cards when that helps learning.
- When several answers are valid, state the assumptions and relevant tradeoffs. If the evidence is uncertain or sources disagree, say so instead of presenting a disputed answer as fact.

## Anki CSV format

- Write plain UTF-8 text with comma-separated fields and one record per card.
- For a new file, use these headers for Anki 2.1.54 and later:
  - `#separator:Comma`
  - `#html:false`
  - `#columns:Front,Answer,Explanation`
- If the user requests different fields or field order, use those names and that order in `#columns` and every record. Keep Front, Answer, and Explanation unless the user specifies otherwise.
- Do not add a regular CSV header row to a new Anki file. Anki would import it as a note.
- Use standard CSV quoting: quote fields containing commas, double quotes, or newlines, and double any double quotes inside a quoted field.
- Keep HTML disabled for plain text so that literal angle brackets and other content remain intact. If the user requests HTML, set `#html:true` and escape literal angle brackets and ampersands.
- Do not insert or change a `#deck` header unless the user requests a specific Anki deck or the existing file already has one. Otherwise, let the user choose the deck during import.
- For duplicate detection, explain that Front must be the first field in the Anki note type, even if Front is not the first CSV column. Map the CSV columns by name.
- Do not assume that Anki has a note type with all requested fields. Explain how to create a note type with Front as the first field and map the requested columns.
- When the fields are Front, Answer, and Explanation, use `{{Front}}` on the front template. Use `{{FrontSide}}<hr id="answer">{{Answer}}<br>{{Explanation}}` on the back template. Adapt the instructions if the user requests different fields.
- If an existing file uses another format, inspect it before editing. If it is not Anki-compatible, explain the issue and ask before converting it.
- If the user requests a file, write it at the selected path and provide the necessary import instructions in the reply. Do not put import instructions in the CSV.
- If the user requests CSV in the reply, output only the Anki headers and CSV records.
- Follow the [Anki text import guide](https://docs.ankiweb.net/importing/text-files.html).

## Example

```csv
#separator:Comma
#html:false
#columns:Front,Answer,Explanation
"A plant receives water and light but has no carbon dioxide. Can it make glucose through photosynthesis?","No.","Photosynthesis uses carbon dioxide and water to make glucose. Light supplies energy for the process."
```

## Before finishing

1. Make sure that each card matches the requested topic, audience, and difficulty.
2. Make sure that each answer follows from the question and its stated assumptions.
3. Check new cards for duplicate questions and concepts in the target deck.
4. Parse the file as UTF-8 CSV after skipping the Anki headers. Check the requested number of new cards, field count, populated fields, unique Front values, and valid quoting.
5. When updating a deck, make sure that its existing records and metadata remain intact.
6. Report the file path and how many cards were added when the user requested a file.
