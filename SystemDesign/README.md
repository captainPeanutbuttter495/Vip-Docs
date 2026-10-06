# System Design

Architecture documentation for VIP: C4 model diagrams and system design decisions covering both `vip-web-frontend` and `vip-web-backend`.

---

## C4 Diagrams

The `C4-Diagrams/` subfolder follows the [C4 model](https://c4model.com/) (Context, Containers, Components, Code) by Simon Brown.

| Level | File | Description |
|-------|------|-------------|
| — | [00-C4-Overview](C4-Diagrams/00-C4-Overview.md) | Index: hierarchy, palette, key decisions |
| **0** | [01-SystemLandscape](C4-Diagrams/01-SystemLandscape.md) | The HSI Innovation Hub ecosystem: VIP site, future clients, AWS, CMS |
| **1** | [02-SystemContext](C4-Diagrams/02-SystemContext.md) | VIP, its users, and external systems |
| **2** | [03-Container](C4-Diagrams/03-Container.md) | Next.js site, Django API, PostgreSQL, planned AWS pieces |
| **3a** | [04-Component-Frontend](C4-Diagrams/04-Component-Frontend.md) | Routes, sections, content data, providers, hooks |
| **3b** | [05-Component-Backend](C4-Diagrams/05-Component-Backend.md) | URLconf, views, serializers, exception handler, spectacular |
| **4** | [06-Code](C4-Diagrams/06-Code.md) | Event schema, soft-delete lifecycle, request pipeline |
| **Dyn** | [07-Dynamic-Diagrams](C4-Diagrams/07-Dynamic-Diagrams.md) | Create/patch/delete event, health check, planned events page fetch |

All diagrams use **Mermaid**, which renders natively in Obsidian. `.obsidian/snippets/mermaid-fit.css` makes them scale to width.

---

## Related Notes

- [[Tech_Stack]]
- [[Project_Structure]]
- [[AWS_Architecture]]
