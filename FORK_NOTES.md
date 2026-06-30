# Fork Notes — yeuleh/ipatool

This is a fork of [`majd/ipatool`](https://github.com/majd/ipatool) with a community fix applied to restore Apple App Store authentication.

## Why This Fork Exists

Apple moved the auth endpoint from `urlBag.authEndpoint` to a root-level `authenticateAccount` field in the Bag response (~May 2026). Upstream `v2.3.0` does not handle this change, causing all login attempts to fail. The upstream maintainer is unresponsive (7 PRs open since Jun 10, 2026, zero merges as of Jun 30, 2026).

This fork applies PR [#493](https://github.com/majd/ipatool/pull/493) ("Fix dynamic App Store auth endpoint discovery", 4 approvals) which fixes the root cause and adds resilience against future endpoint changes.

## Base Commit

- **Upstream**: `majd/ipatool@dcddce4` (main, 2026-05-26)
- **Applied patch**: PR #493 commit `a98f833` by @b-nnett
- **Fork tags**: see `git tag --list 'v2.3.1-fix-auth.*'` for available versions

## What PR #493 Does

1. Reads the new `authenticateAccount` field from the Bag response (root-level, not inside `urlBag`).
2. Normalizes the endpoint to `https://auth.itunes.apple.com/auth/v1/native/fast/`.
3. When plist decode fails (non-plist response), extracts the new endpoint URL from the response body and retries.

## Sync Procedure (When Upstream Merges the Fix)

When `majd/ipatool` merges an equivalent fix and tags a release:

1. In the dependent project (e.g., `ipa-manager`), remove the `replace` directive from `go.mod`.
2. Update the `require` to the upstream version with the fix (e.g., `v2.4.0`).
3. Run `go mod tidy`.
4. Verify `go build` + `go test` pass.
5. This fork can then be archived or deleted.

## Usage in Go Projects

```go
// go.mod — use the latest v2.3.1-fix-auth.* tag
replace github.com/majd/ipatool/v2 => github.com/yeuleh/ipatool/v2 v2.3.1-fix-auth.4
```

## License

MIT (same as upstream).
