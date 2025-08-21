# Documentation Writing Guidelines

This file contains instructions for writing clear, readable documentation for all my projects. These guidelines are based on the principles in `writing-readable-docs.md`.

## Working relationship
- You can push back on ideas-this can lead to better documentation. Cite sources and explain your reasoning when you do so
- ALWAYS ask for clarification rather than making assumptions
- NEVER lie, guess, or make up information

## Project context
- Format: Markdown files with YAML frontmatter
- Config: content/docs/db.json for navigation

## Document Structure

### General guidelines
- Explain the topic a little before showcasing the code examples
- When describing a method, do explain the parameters it accepts
- Prioritize accuracy and usability of information
- Check existing patterns for consistency
- Search for existing information before adding new content. Avoid duplication unless it is done for a strategic reason
- Start by making the smallest reasonable changes
- Write an SEO friendly summary in the YAML frontend

Every document should be divided into these groups (not all docs need all groups):

### Explanations
- Most docs will start with an explanation. In certain cases these explanations can be super small.
- Explanations are text heavy
- Avoid code examples until the end
- Goal is comprehension, not copy-paste
- If there are installation instructions, they should be right after the explanation and before the basic example.

### Usage
- Most of our docs are focused on showcasing the usage of a module
- Its okay to explain a little before showcasing the usage code examples
- Code examples should be familiar but don't need to be copy-paste ready
- Can hide supplementary code details that users can reveal if needed
- The first usage example should be a basic example.
- Other usage examples can be around small topics or APIs within a module

### Tutorial or Step-by-Step Instructions
- Not every doc will have step-by-step instructions
- Include full working examples
- Focus on getting users to complete tasks with minimal reading
- Explain less, show more
- Make examples copy-paste ready

### Reference
- List separately when possible
- If included in a doc, cover under a higher-level topic

## Visual Structure Guidelines

### Headings
- Use headings freely to break content into digestible sections. However, the sections within an H3 tag should not be broken further.
- Divide docs into sub-topics with small, focused sections
- Avoid long linear sections without breaks

### Admonition Blocks
- Use sparingly - overuse reduces effectiveness
- Only for critical information that needs user attention
- If everything is critical, nothing is

### Rich Elements
- Don't use fancy widgets unnecessarily
- Avoid over-structuring textual information with complex elements
- Multiple competing visual elements (text, accordions, admonitions) fight for focus

### Code and Visual Placement
- Explain first, then show code/visuals
- Do not surround the code examples with before and after explanations of the same topic.

## Code Examples

### Diff Usage
- Avoid showing code examples as diffs from previous steps
- Don't use delete/insert blocks unless its part of step-by-step instructions or tutorials
- Treat related topics as separate usage examples, not incremental changes

### Highlighting
- Usage and reference examples rarely need line or word highlights
- Keep highlighting minimal and purposeful unless it's truly step-by-step instructions

## Other Elements

### Tables
- Use for displaying checklists
- Keep simple and scannable

### Definition Lists
- Use for documenting parameters, configuration options, and values
- Include label, data type, and description

### Keep It Simple
- No other rich elements needed
- Focus on clarity over visual complexity

## Writing Tone

- Write simple, declarative sentences. Brevity is a plus. Get to the point.
- Use present tense
- Focus on helping users accomplish their goals
- Prioritize understanding over comprehensive coverage
- Make examples feel familiar to developers
- Second-person voice ("you")
- Prefer wordings that avoid "you"s and "your"s.
- When using pronouns in reference to a hypothetical person, such as "a user with a session cookie", gender neutral pronouns (they/their/them) should be used
- Use American English
- Use the Oxford comma
