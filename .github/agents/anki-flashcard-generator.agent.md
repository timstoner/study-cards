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
- Use a separate CSV for each note type when a deck contains both question-and-answer and Cloze cards. Import both files into the same Anki deck. Do not append Cloze notes to a question-and-answer CSV or change its note type.
- Check new cards against the target deck for duplicate questions and repeated concepts. The same concept can appear in separate decks when it serves their different goals.

## Card content

- By default, use question-and-answer cards with Front, Answer, and Explanation fields. Include additional fields, such as Category, when the user requests them.
- When the user requests Cloze cards, write a contextual statement with a short, meaningful part hidden using `{{c1::answer}}`. Put the explanation in the Cloze note's back field.
- Use one `c1` deletion per note by default so that one note creates one card. Different deletion numbers create separate cards. Use them only when the requested number of cards accounts for that behavior.
- For question-and-answer cards, make the Front a clear question with enough context for one answer. Use one main learning objective per card.
- Give a precise Answer. Explain why it is correct and add a useful example, limitation, or common mistake when appropriate.
- Match the card type to the subject. Use scenarios, interpretation, calculations, code, images described in text, or direct recall when they suit the learning goal.
- Adjust the difficulty to the user's request. Basic recall is appropriate for a beginner or when foundational facts must be memorized.
- Use practical examples when they fit the topic. Do not require code or a scenario for subjects that do not need them.
- Avoid vague questions, unsupported claims, unnecessary detail, and duplicate concepts. Break a long list or complex scenario into focused cards when that helps learning.
- When several answers are valid, state the assumptions and relevant tradeoffs. If the evidence is uncertain or sources disagree, say so instead of presenting a disputed answer as fact.

## Anki CSV format

- Write plain UTF-8 text with comma-separated fields and one record per card.
- For a new question-and-answer file, use these headers for Anki 2.1.54 and later:
  - `#separator:Comma`
  - `#html:false`
  - `#columns:Front,Answer,Explanation`
- For a new Cloze file, use `#separator:Comma`, `#html:false`, `#notetype:Cloze`, and `#columns:Text,Back Extra` on separate lines. Each record contains the Cloze text and its explanation. If the user's Cloze note type uses different field names, map the columns to its text and back fields during import.
- If the user requests different fields or field order, use those names and that order in `#columns` and every record. Preserve the required Cloze text field for Cloze notes.
- Do not add a regular CSV header row to a new Anki file. Anki would import it as a note.
- Use standard CSV quoting: quote fields containing commas, double quotes, or newlines, and double any double quotes inside a quoted field.
- Keep HTML disabled for plain text so that literal angle brackets and other content remain intact. If the user requests HTML, set `#html:true` and escape literal angle brackets and ampersands.
- Do not insert or change a `#deck` header unless the user requests a specific Anki deck or the existing file already has one. Otherwise, let the user choose the deck during import.
- For duplicate detection, use Front as the first note field for question-and-answer cards and Text as the first note field for Cloze cards. Map the CSV columns by name.
- Do not assume that Anki has a note type with all requested fields. Explain how to create a question-and-answer note type with Front first and map the requested columns.
- When the fields are Front, Answer, and Explanation, use `{{Front}}` on the front template. Use `{{FrontSide}}<hr id="answer">{{Answer}}<br>{{Explanation}}` on the back template. Adapt the instructions if the user requests different fields.
- For Cloze cards, select Anki's built-in Cloze note type during import and map Text and Back Extra to its corresponding fields. Select the intended deck during import. If the built-in type is renamed or localized, select it manually.
- If a custom Cloze note type is needed, clone Anki's Cloze type. Do not use a regular question-and-answer note type or template for Cloze notes.
- If an existing file uses another format, inspect it before editing. If it is not Anki-compatible, explain the issue and ask before converting it.
- If the user requests a file, write it at the selected path and provide the necessary import instructions in the reply. Do not put import instructions in the CSV.
- If the user requests CSV in the reply, output only the Anki headers and CSV records.
- Follow the [Anki text import guide](https://docs.ankiweb.net/importing/text-files.html).

## Examples

```csv
#separator:Comma
#html:false
#columns:Front,Answer,Explanation
"A plant receives water and light but has no carbon dioxide. Can it make glucose through photosynthesis?","No.","Photosynthesis uses carbon dioxide and water to make glucose. Light supplies energy for the process."
```

```csv
#separator:Comma
#html:false
#notetype:Cloze
#columns:Text,Back Extra
"Plants use {{c1::carbon dioxide}} and water to make glucose through photosynthesis.","Light supplies energy for this process."
```

## Before finishing

1. Make sure that each card matches the requested topic, audience, and difficulty.
2. Make sure that each answer follows from the question and its stated assumptions.
3. Check new cards for duplicate questions and concepts in the target deck.
4. Parse the file as UTF-8 CSV after skipping the Anki headers. Check the requested number of new cards, field count, populated fields, unique first-field values, and valid quoting. For Cloze notes, check the deletion syntax and the number of cards implied by the distinct deletion numbers.
5. When updating a deck, make sure that its existing records and metadata remain intact.
6. Report the file path and how many cards were added when the user requested a file.
