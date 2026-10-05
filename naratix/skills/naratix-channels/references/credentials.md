# Connector credentials

What the shop's owner has ready before the form, and how to read the form's answer. Credentials are never typed in the chat: [SKILL.md](../SKILL.md), *Credentials*.

Before you open the form, tell the owner what to have ready, and that what they type goes straight to Naratix, encrypted, and you never see it:

- **VTEX**: the store's API address, `https://{account}.vtexcommercestable.com.br/api`, and an app key and app token from VTEX Admin's application keys, with access to read and write the catalogue.
- **Mirakl**: the marketplace's API address, `https://{your-marketplace}.mirakl.net/api`, and its two keys. The shop key reads the category tree and attributes, sends products and reads the marketplace reports; the API key only brings products in. Either one saves; optionally the Mirakl shop id.
- **API connector**: the partner's endpoint, its OAuth token URL, a client id and a client secret.

An address with another path is refused with the shape to use; a bare address gets `/api` added. A launch whose key is missing names that key and starts nothing, before any card: a Mirakl push needs the shop key unless it builds the files only (`upload: false`); tell the user which task that key is for, and open the form for that connector.

Each save of a Mirakl or VTEX connector tries its keys on a call that changes nothing. The form answers you with which connector was saved and what that check found — "Connected" with the categories it sees, which key was refused, or that the address did not answer — never with what was typed. A refusal means the owner corrects that key or the address and saves again; a check that says the keys were not checked, means saving again in a few minutes; one that says the channel could not be reached right now means checking the address, or saving again in a few minutes. "Connected" with the API key alone still needs the shop key before products are sent. Where the app shows no form, the result says so and links the connector's page, where the same list applies: wait for the owner's word that it is saved, then read it back with `list-connectors`. A partner's inbound credentials are generated on the API connector's page in the app.

**Completion:** the form's answer reads Connected (with the shop key when products will be sent), or the owner knows what to correct, or, with no form, `list-connectors` shows the saved connector.
