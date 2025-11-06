# Step I — RTL Linting

The **RTL Linting** stage is the first step of the LibreLane flow and is dedicated to validating the **quality and consistency** of the RTL design before synthesis.  
It checks for syntax errors, unsupported Verilog constructs, potential timing issues, and structural inconsistencies that could cause synthesis or simulation failures later in the flow.

Running linting early ensures that the input design is clean and portable across tools and PDKs, saving debugging time in later stages.

## Tools Involved

This stage mainly uses:

- **[Verilator](../../tools/verilator.md)** — performs static analysis and linting on the RTL files, detecting syntax and semantic issues.  
- **[Checker](../../tools/checker.md)** — interprets Verilator’s output and classifies messages as errors, warnings, or timing-related constructs.

LibreLane integrates these tools automatically through its internal step sequence, so linting runs as part of the project initialization.

## Sequence of Steps

The RTL linting stage in LibreLane includes the following sub-steps:

1. **Verilator.Lint** — runs the Verilator linter to analyze all RTL source files.  
2. **Checker.LintTimingConstructs** — identifies timing constructs such as delays (`#`) and event controls that may not synthesize correctly.  
3. **Checker.LintErrors** — reports syntax or structural errors detected in the RTL.  
4. **Checker.LintWarning** — summarizes warnings (e.g., unused signals, mismatched widths).

These checks ensure the RTL design is syntactically valid and structurally ready for synthesis.

## Configuration Overview

Linting configuration options are defined in the project’s `config.json` file.  
A typical configuration block might look like this:

```json
{
  "RTL_TOP_MODULE": "top",
  "RTL_INCLUDE_DIRS": ["src/includes"],
  "RTL_LINT_STRICT_MODE": true,
  "RTL_LINT_WARN_AS_ERROR": false
}
```

Explanation:

- RTL_TOP_MODULE — defines the top-level module to be analyzed.
- RTL_INCLUDE_DIRS — specifies directories where header and include files are located.
- RTL_LINT_STRICT_MODE — enables stricter Verilator checking rules.
- RTL_LINT_WARN_AS_ERROR — treats warnings as errors if set to true.

LibreLane automatically collects all RTL files listed in the project and passes them to Verilator with the appropriate configuration.

## Example: Running Only the RTL Linting Step

To run only the linting stage:
```
librelane run -d my_design -f rtl-linting
```

This command will:

1. Load all RTL files specified in the configuration.
2. Run Verilator linting with the configured flags.
3. Summarize results through the Checker module.
4. Generate lint and warning reports for review.

All logs and reports will be placed under:
```
reports/rtl-linting/
logs/rtl-linting/
```
## Expected Outputs

- Lint report: reports/rtl-linting/verilator_lint.rpt
- Warning summary: reports/rtl-linting/lint_warnings.rpt
- Error summary: reports/rtl-linting/lint_errors.rpt
- Logs: logs/rtl-linting/verilator.log

## Common Issues and Checks

- Unsupported Verilog constructs: avoid delays (#), system tasks ($display), and non-synthesizable loops.
- Width mismatches: ensure signal widths match between module ports and assignments.
- Unused signals or modules: remove or connect unused signals to prevent synthesis warnings.
- Include path issues: check that all include directories are defined correctly in the configuration.
- Warnings treated as errors: if strict mode is active, review all Verilator warnings before proceeding.

Fixing issues at this stage ensures a smooth synthesis process in Step II.