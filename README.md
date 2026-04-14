# nanorc for Stata

Syntax highlighting for Stata (`.do`, `.ado`, `.mata`) files in [GNU nano](https://www.nano-editor.org/).

As far as I can tell, no nanorc file for Stata existed publicly before this one, so I'm sharing mine for Public use.

## What it highlights

- **Control flow and program structure**: `if`, `else`, `forvalues`, `foreach`, `while`, `program`, `syntax`, `args`, `capture`, `quietly`, `mata`, etc.
- **Built-in commands**: data management (`use`, `merge`, `reshape`, `egen`, `gen`, ...), estimation (`regress`, `xtreg`, `ivregress`, `logit`, `probit`, `poisson`, `stcox`, `arima`, ...), postestimation (`margins`, `test`, `lincom`, `predict`, ...), and tests/plots (`ttest`, `correlate`, `twoway`, ...).
- **Popular user-written commands**: `reghdfe`, `ivreghdfe`, `ivreg2`, `ppmlhdfe`, the `gtools`/`ftools` family, `esttab`/`estout`/`eststo`, `coefplot`, `binscatter`/`binsreg`, the `rdrobust` family, DID tools (`csdid`, `did_multiplegt`, `eventdd`, `honestdid`, `bacondecomp`, `sdid`), matching (`psmatch2`, `teffects`, `kmatch`, `ebalance`), and reporting (`outreg2`, `asdoc`, `dataex`, `texsave`).
- **Abbreviations**: common short forms alongside full names (`reg`, `gen`, `sum`, `su`, `tab`, `qui`, `cap`, `noi`, `di`, `bys`, ...).
- **Functions**: math, string, date/time, and random-number generators.
- **Locals and globals**: backtick-apostrophe locals (`` `x' ``), `$global`, `${global}`, and returned results (`r()`, `e()`, `s()`, `c()`).
- **Strings, numbers, operators.**
- **Comments**: `*` (line-leading), `//`, `///`, and `/* ... */` blocks.

## Installation

### Prerequisites

You need a reasonably modern version of nano (2.9+ recommended). Check with:

```bash
nano --version
```

**macOS users**: the system nano at `/usr/bin/nano` is ancient (2.0.6) and will not work with this file. Install a modern version via [Homebrew](https://brew.sh):

```bash
brew install nano
```

Then make sure Homebrew's `bin` directory is ahead of `/usr/bin` in your `PATH`. Add this to `~/.bash_profile` (bash) or `~/.zshrc` (zsh):

```bash
export PATH="/opt/homebrew/bin:$PATH"   # Apple Silicon
# or
export PATH="/usr/local/bin:$PATH"      # Intel
```

Reload your shell (`source ~/.bash_profile` or open a new terminal) and confirm `which nano` points to the Homebrew version.

**Linux users**: most distributions ship a recent enough nano. If yours doesn't, install via your package manager (`apt install nano`, `dnf install nano`, etc.).

### 1. Download the file

```bash
mkdir -p ~/.nano
curl -o ~/.nano/stata.nanorc https://raw.githubusercontent.com/njjenkins/stata-nanorc/main/stata.nanorc
```

Or clone the repo:

```bash
git clone https://github.com/njjenkins/stata-nanorc.git ~/.nano/stata-nanorc
```

### 2. Enable it in your `~/.nanorc`

Add this line (create the file if it doesn't exist):

```
include "~/.nano/stata.nanorc"
```

If you cloned the repo, point at that path instead:

```
include "~/.nano/stata-nanorc/stata.nanorc"
```

If highlighting doesn't appear after the steps below, try replacing `~` with the absolute path to your home directory — some older versions of nano don't expand `~` inside `include` directives:

```
include "/Users/yourusername/.nano/stata.nanorc"
```

### 3. Test it

```bash
nano example.do
```

You should see keywords, strings, comments, and macros colored.

## Compatibility

Developed against nano 2.9+, but works on older versions with one or two tweaks.

### nano < 2.9

The `comment "*"` directive (used by `M-3` to toggle comments) was added in 2.9. On older nano you'll get:

```
Error in ~/.nano/stata.nanorc on line 15: Command "comment" not understood
```

Fix: delete or comment out that line.

```bash
sed -i '/^comment /d' ~/.nano/stata.nanorc
```

### nano 2.3.x and earlier

If POSIX character classes like `[[:space:]]` cause errors, replace them with literal character classes (a space and a tab inside brackets). Only one line in the file uses this (the line-leading `*` comment rule).

Check your version with:

```bash
nano --version
```

## Customization

### Colors

The file uses the standard 8 nano colors: `brightblue` for control flow, `brightgreen` for built-in commands, `brightcyan` for user-written commands, `yellow` for functions, `brightyellow` for macros, `brightred` for strings, `green` for comments, and `magenta` for numbers and operators. Edit any `color <name>` line to change them.

### Adding commands

If a command you use isn't highlighted, add it to the appropriate `color` line. User-written commands go on the `brightcyan` line; built-in commands go on one of the `brightgreen` lines. Word boundaries use `\<` and `\>`:

```
color brightcyan "\<(existing|commands|here|yournewcommand)\>"
```

Pull requests welcome, especially for commonly-used community commands I missed.

## Known limitations

- **Mid-line `*` comments**: Stata treats `*` as a comment character at the start of a line, but `*` is also multiplication. Nano's line-based regex can't reliably tell these apart, so only line-leading `*` comments are highlighted. Mid-line uses of `*` are treated as operators. This matches how most other editors handle Stata.
- **Nested block comments**: `/* ... */` regions spanning many lines work, but certain edge cases may confuse the highlighter.
- **Nested locals**: `` `x' `` highlights correctly; `` ``x'' `` highlights only to one level.
- **Abbreviation coverage is not exhaustive**: Stata allows abbreviations down to a minimum length for most commands (e.g., `regress` can be `reg`, `regr`, `regre`, `regres`, or `regress`). This file highlights the full form and the most common short forms only (for now).
- **Command list is not complete**: Stata has hundreds of commands and there are thousands of user-written packages on SSC. If something you use isn't highlighted, add it (see above) or open an issue.

## Contributing

Issues and PRs welcome. If you're adding commands, please group them with similar commands on the appropriate color line rather than adding new lines, and keep the alphabetical-ish grouping where it exists.

## License

GNU GPL. See `LICENSE`.

## Acknowledgments

Inspired by the [scopatz/nanorc](https://github.com/scopatz/nanorc) collection, which covers many languages but not Stata.