# References

Source index, glossary, and reading list for the metric pages in this repo.

## Scope

- Kernel versions: **6.18** (the floor, an LTS release) and **7.x**. The 7.x tag checked is **v7.3**.
- Older kernels are excluded unless their behaviour is unchanged in 6.18 and 7.x.
- Every link here was checked to resolve. The kernel paths were checked to exist in the `v6.18` and `v7.3` tags. The claims themselves are not yet verified against the source. That is the next step.

## Pinned versions

| Tag | Role | Commit |
|---|---|---|
| `v6.18` | Floor | `7d0a66e4` |
| `v7.3` | Current 7.x | `c03c1a14` |

Tags are at `https://github.com/torvalds/linux/tree/<tag>`.

## Files

| File | Contents |
|---|---|
| [kernel-sources.md](kernel-sources.md) | Kernel source and `Documentation/` paths, with Elixir and kernel.org links pinned per tag |
| [tools-and-man-pages.md](tools-and-man-pages.md) | Man pages and the userspace tool projects that read `/proc`, `/sys`, and netlink |
| [glossary.md](glossary.md) | Definitions for terms used in the metric pages, each pointing to where the term is defined |
| [books.md](books.md) | Reading list, with notes on which parts still hold for 6.18 and 7.x |

## Rules for citing

1. Cite a path with its tag, for example `fs/proc/stat.c` at `v7.3`.
2. Prefer `Documentation/` for stated behaviour and source for exact semantics.
3. Man pages and books are secondary. Confirm field-level details against the kernel source.
4. Record the tag next to each verification result, as the root [README](../README.md) asks.
