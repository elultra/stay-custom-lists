# Stay Custom Lists

A personal repository of Safari Content Blocker subscription lists formatted for the [Stay](https://github.com/shenruisi/Stay) browser extension.

Each file under [`lists/`](./lists) is a standalone plain-text ruleset designed to be subscribed to via its raw HTTPS URL, allowing independent toggling and automatic updates per list.

---

## Repository Structure

```text
stay-custom-lists/
├── .gitattributes          # Enforces LF line endings across rule files
├── .gitignore              # Ignored OS, editor, and temporary files
├── AGENTS.md               # Authoring guidelines and Stay compiler constraints
├── LICENSE                 # MIT License
├── README.md               # Repository overview and subscription guide
└── lists/                  # Standalone Stay subscription lists (*.txt)
    └── <list-name>.txt
```

---

## How to Subscribe in Stay

1. **Enable Stay Content Extensions (iOS / iPadOS)**:
   - Open **Settings → Apps → Safari → Extensions** *(or **Settings → Safari → Extensions**)*.
   - Enable **Stay Content Subscribe** and ensure Safari website permissions are granted.
2. **Copy the Raw List URL**:
   - Open any `.txt` file inside [`lists/`](./lists) on GitHub.
   - Click **Raw** and copy the direct URL (`https://raw.githubusercontent.com/elultra/stay-custom-lists/main/lists/<list-name>.txt`).
3. **Add the Subscription in Stay**:
   - Open the **Stay** app and go to the **Content Blocker** tab.
   - Tap **`+`** (**New Subscription**).
   - Enter a **Title**, paste the raw URL into **Link**, and confirm.

---

## List File Format

Lists use the Adblock Plus plain-text format subset compiled by Stay into Safari Content Blocker JSON rules:

```adblock
[Adblock Plus 2.0]
! Title: Example List
! Version: 1.0.0
! Expires: 4 days

||cdn.example.com^
||*.cdn.example.com^
```

See [AGENTS.md](./AGENTS.md) for Stay-specific parser behavior, limits, and authoring rules.

---

## License

[MIT](./LICENSE)
