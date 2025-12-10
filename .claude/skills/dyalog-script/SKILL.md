---
name: dyalog-script
description: This Skill executes APL code via the `dyalogscript` interpreter.
---

# Dyalog-Script

## Instructions

`dyalogscript` is an interpreter that can execute a textfile containing APL code from the commandline, without relying on a full, running Dyalog session. `dyalogscript` is executed as follows:

```bash
/usr/local/bin/dyalogscript my_apl_program.apls
```

Notes: 

1. You MUST store your code in a file; you cannot feed programs as strings or via stdin.
2. Do remove ephemeral test scripts following evaluation.
3. Unlike the normal Dyalog session, you MUST use "quad-gets" (`⎕ ← ...`) to dump values to `stdout`.
4. **CRUCIAL**: your script files MUST have a trailing newline!
5. Always use the full path to `/usr/local/bin/dyalogscript`.
6. Ensure that your temporary script files have unique names.

## Examples

User: Evaluate `(+⌿÷≢)⍳100` as Dyalog APL

Claude creates the file `eval-xyz.apls` with the content:

```apl
⎕←(+⌿÷≢)⍳100 ⍝ Note ⎕← to print to stdout, and trailing newline

```

Claude executes `/usr/local/dyalogscript eval-xyz.apls`:

```bash
50.5
```
