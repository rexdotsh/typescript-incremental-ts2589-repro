# TypeScript 7 incremental TS2589 reproduction

An unchanged, self-contained type-checking example passes when checked from a fresh build cache, then emits a locationless TS2589 after a comment-only edit on the incremental path.

## Reproduce

With Node.js and npm installed, run these commands in this directory:

```sh
npm ci
rm -f tsconfig.tsbuildinfo
npm run typecheck
printf '\n// comment-only edit\n' >> repro.ts
npm run typecheck
```

The first check passes. The second check exits with:

```text
error TS2589: Type instantiation is excessively deep and possibly infinite.
```

For a control, remove the build cache and check the *same edited source* again:

```sh
rm -f tsconfig.tsbuildinfo
npm run typecheck
```

This fresh check passes. The repro uses TypeScript 7.0.2 and `--checkers 1`; it has no application or third-party type dependencies. `Json` and `Parsed<T>` represent the recursive JSON type and JSON response type that exposed the issue in a larger project.
