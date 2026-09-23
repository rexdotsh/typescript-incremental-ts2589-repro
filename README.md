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

## Version comparison

The same source and `tsconfig.json` were checked with TypeScript 6.0.3 (invoking `tsc -p tsconfig.json` directly, since `--checkers` is a TypeScript 7 option): cold, edited incremental, and edited fresh checks **all passed**. With TypeScript 7.0.2, the results were pass, locationless TS2589, and pass, respectively.

The `typescript@next` nightly, **7.1.0-dev.20260922.1**, was also checked with the same `--checkers 1` script: cold pass, locationless TS2589 on the comment-only incremental run, fresh pass. The nightly has not fixed the issue as of this version.
