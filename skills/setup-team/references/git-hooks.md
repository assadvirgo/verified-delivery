# Git hooks

Write each block below to the named file in the repository, exactly as shown, then make the two hooks executable: `chmod +x .claude/git-hooks/commit-msg .claude/git-hooks/pre-push`.

The pattern list comes from `wording-patterns.txt` in this folder; copy it to `.claude/git-hooks/wording-patterns.txt`.

## `.claude/git-hooks/commit-msg`

```bash
#!/usr/bin/env bash
# Rejects commit messages, authors and committers that mention authoring tools or carry generated trailers.
set -euo pipefail
root="$(git rev-parse --show-toplevel)"
patterns="$root/.claude/git-hooks/wording-patterns.txt"
active=$(grep -v '^#' "$patterns" | grep -v '^$')
msg="$1"

hits=$(grep -n -i -E -f <(printf '%s\n' "$active") "$msg" || true)
if [ -n "$hits" ]; then
  echo "Commit message rejected by wording check:" >&2
  echo "$hits" >&2
  exit 1
fi

# The identities the commit is about to be recorded under.
for ident in AUTHOR COMMITTER; do
  value=$(git var "GIT_${ident}_IDENT" 2>/dev/null || true)
  who=$(printf '%s\n' "$value" | sed -E 's/ [0-9]+ [+-][0-9]{4}$//')
  hits=$(printf '%s\n' "$who" | grep -i -E -f <(printf '%s\n' "$active") || true)
  if [ -n "$hits" ]; then
    echo "Commit rejected by wording check: the $(printf '%s' "$ident" | tr 'A-Z' 'a-z') is $hits" >&2
    exit 1
  fi
done

# Optional: the project's own commit-message linter runs after the wording check.
if ls "$root"/commitlint.config.* >/dev/null 2>&1 && command -v npx >/dev/null 2>&1; then
  cd "$root" && npx --no-install commitlint --edit "$msg"
fi
```

## `.claude/git-hooks/pre-push`

```bash
#!/usr/bin/env bash
# Rejects a push whose new commits add lines, file names, messages or identities that mention authoring tools.
# The team configuration folder and the project guide are exempt.
set -euo pipefail
root="$(git rev-parse --show-toplevel)"
patterns="$root/.claude/git-hooks/wording-patterns.txt"
active=$(grep -v '^#' "$patterns" | grep -v '^$')
zero=0000000000000000000000000000000000000000
status=0
while read -r local_ref local_sha remote_ref remote_sha; do
  [ "$local_sha" = "$zero" ] && continue
  # Only commits no remote ref holds yet, so history already pushed is never re-judged.
  exclude=""
  if [ "$remote_sha" != "$zero" ] && git cat-file -e "$remote_sha^{commit}" 2>/dev/null; then
    exclude="$remote_sha"
  fi
  range=$(git rev-list "$local_sha" --not --remotes $exclude)
  for commit in $range; do
    idents=$(git show --no-patch --format='author: %an <%ae>%ncommitter: %cn <%ce>' "$commit")
    ident_hits=$(printf '%s\n' "$idents" | grep -i -E -f <(printf '%s\n' "$active") || true)
    if [ -n "$ident_hits" ]; then
      echo "Push rejected by wording check in the identity of commit ${commit:0:10}:" >&2
      echo "$ident_hits" >&2
      status=1
    fi
    added=$(git show --format='%B' --unified=0 --no-color "$commit" -- . ':(exclude).claude' ':(exclude)CLAUDE.md' | grep -E '^(\+[^+]|\+\+\+ b/|[^+@ -])' || true)
    hits=$(printf '%s\n' "$added" | grep -n -i -E -f <(printf '%s\n' "$active") || true)
    if [ -n "$hits" ]; then
      echo "Push rejected by wording check in commit ${commit:0:10}:" >&2
      echo "$hits" | head -20 >&2
      status=1
    fi
  done
done
exit $status
```
