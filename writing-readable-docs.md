# Writing readable technical docs

What makes docs readable? Is it the colors, the typography, spacing, or the layout? Well, these elements do contribute to a great reading experience. However, most of the technical documentation lacks strategic writing.

## Dividing a doc into groups
A document refers to a single web-page a user is reading related to a given topic. When writing a doc, you must divide it into the following groups (*Not all docs will have elements from all the groups*).

- **Tutorial or step by step instructions**: These should include full working examples. Explain less and focus on getting the user get their job done without reading too much.
- **Explanations**: These have to be text heavy and try to avoid sharing code examples until the end. The goal here is to not copy-paste code, instead understand how something works, or how do I use it. **Ideally, the start of every doc should be explanation**.
- **Reference**: Should be listed separately most of the times. If its part of a doc, then it must be covered under some other high level topic.
- **Usage**: Usage of a feature, module or package.
  - Should be as terse as possible.
  - The code examples does not have to be copy-paste ready. However, they must be written in a familiar manner.
  - We can hide away parts of code examples that makes the full example and let the user reveal them (if needed). Let's call them suplements.
  - Most of our docs are usage docs on a given topic.

## Visual structure

- **Use headings freely**. It is okay to divide a doc into further new sub-topics and share their usage in small sections. No need to write big linear sections that does not break at all.
- **Use admonition block sparingly**. If every 2nd section needs an admonition block, then it won't stay effective at all. Remember their goal is to divert user attention towards something crictical and if everything is critical, then nothing is.
- **Don't use fancy widgets**. The rise of MDX made it possible to embed rich HTML widgets into a doc. While, using these widgets are helpful in certain scanerios like feature rich codeblocks, codegroups, interactive playarounds. Using them to over structure the textual information will distract the reader's focus. For example, if the viewport display some text, an accordion and an admonition block, then all these elements will fight for focus at the same time.
- **Do not sorround codeblocks or images with explanation**. Either explain first and then show the codeblock or visuals. Or display them first and then explain (*and be consistent with the pattern*). A codeblock/visual sorrounded with explaination on both the ends breaks the reading flow and the user will have to carry the information in their mind longer until the entire section concludes.

## Codeblocks

Unless a tutorial or a step by step instruction, avoid sharing code examples as diff from the previous step. For example, when explaining Dependency injection and then method injection, there is no need to wrap constructor dependencies inside `delete` block and method dependencies inside `insert` block. Even though these topics builds on top of each other, they should be treated as step by step instructions.

Similarly, usage examples or reference examples should barely need line or world highlights.

## Tables

Use them to display checklists

## Definition lists

Use them to document params, config, and other values that needs a label, data-type and description.

## Nothing else

No need for any other rich elements