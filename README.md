# Product Lens

A [Claude Code](https://claude.com/claude-code) skill that drops a **lens** into any repo: one
git-tracked JSON graph of what you know about your product, built into a single self-contained,
commentable `index.html`.

The lens holds:

- the **purpose** and the **core loop** the product runs on;
- **avatars** (persona map + journey map), **interviewees** and the **evidence** they gave;
- every statement as a **claim** labelled by where it comes from: **Heard / seen** (someone said or
  did it, with a timestamp), **Owner's view**, or **Untested**;
- up to three **focus hypotheses** with a confidence meter computed from supporting and
  contradicting evidence (never set by hand);
- **surfaces**: what has shipped (with screenshots, code and decision-record links) versus ideas;
- **alternatives** and named **competitors**, with pricing, pitch and their top ads;
- **market insights**, **content pillars** and **decisions**;
- a **Next conversation** list: the specific question to ask for every weak claim.

A build validates the graph against integrity rules (every observed claim cites a source, every
reference resolves, no orphans, ids stay stable so comments never detach) and refuses to write the
page if anything is wrong. `diff.mjs` highlights what changed since any git revision, in place.

No dependencies, no `package.json`: Node 20+ and git.

## Install

Copy the skill into your repo (or into `~/.claude/skills/` to use it everywhere):

```bash
git clone https://github.com/oliverswitzer/product-lens /tmp/product-lens
mkdir -p .claude/skills && cp -R /tmp/product-lens/skills/lens .claude/skills/lens
```

Then ask Claude Code:

> Set up a product lens for this repo.

It copies `skills/lens/template/` into `docs/lens/` (or a folder you pick), interviews you to
replace the starter model, builds the page and runs the tests.

## Use

```bash
node docs/lens/build.mjs                    # validate model.json, write index.html
node --test docs/lens/tests/*.test.mjs      # component tests + your model
node docs/lens/diff.mjs HEAD~3              # show what changed since a revision
python3 -m http.server 8767 --bind 127.0.0.1 && open http://127.0.0.1:8767/docs/lens/index.html
```

Comments are stored in your browser (per origin) and export as JSON or Markdown an agent can act on.

Day to day, ask Claude Code to "add this interview to the lens", "update the quick-capture surface
after this change", "add this brainstorm idea to the lens", "add <competitor> to the lens" or "show
me what changed in the lens". `skills/lens/SKILL.md` has the rules it follows.

## Layout

```
skills/lens/
  SKILL.md            how an agent scaffolds and maintains a lens
  competitors.md      the competitor + ad-library research procedure
  scripts/            add-product.py, apply-staged-ads.py, smoke-ads.mjs (needs Playwright)
  template/           copied into your repo
    model.json        a tiny fictional starter ("Acme Notes")
    build.mjs         validate + inline everything into index.html
    diff.mjs          review layer for what changed since a git revision
    template.html     the page shell
    components/       pure modules (schema, validation, views); app.js is the only DOM code
    tests/            node:test suites
    README.md         node kinds, relations, claim shape, integrity rules
```

## Try the starter without installing

```bash
node skills/lens/template/build.mjs && open skills/lens/template/index.html
```

## License

MIT © Oliver Switzer
