---
name: apply-edits
description: "Applies user edits to LinkedIn posts marked 'Need edits' in Notion. Reads inline comments on each post page, applies the requested changes, replies 'Applied' to each comment, and sets status back to 'Generated'."
argument-hint: ""
---

# /apply-edits

Apply pending edits to LinkedIn posts in Notion. Finds all posts with `Status = Need edits`, reads the inline comments you left, applies the changes, and marks them back as `Generated`.

## Notion Database IDs
See `AGENTS.md` for database IDs and schema details.

---

## Workflow

### Step 1: Find Posts Needing Edits

Query the LinkedIn Posts DB for all pages where `Status = Need edits`.

If none found, tell the user: "No posts currently marked 'Need edits'." and stop.

List the posts found:
```
Found N post(s) marked 'Need edits':
- [Post Title]
- [Post Title]
...
```

---

### Step 2: For Each Post — Read Comments and Apply Edits

For each post:

1. Fetch the full post page content from Notion (to get the current Post Content)
2. Fetch all comments on the page using `get_comments` with `include_all_blocks: true` — this captures both page-level and inline comments
3. Read through all comments carefully. Each comment is an edit instruction from the user.

**Applying edits:**
- Treat each comment as a specific instruction to change something in the post
- Apply every comment's requested change to the post content
- If a comment is ambiguous, use judgment to make the most reasonable interpretation — do not ask for clarification mid-run
- Preserve everything not mentioned in the comments (structure, tone, word count, all rules from `instructions.txt`)
- After applying edits, verify the post still complies with `instructions.txt` rules:
  - No em dashes
  - No banned words (ensure, enhance, leverage, utilize, "for example", "imagine", "tends", draining, relentless)
  - No more than one comma per sentence
  - 100-150 words in body (excluding P.S.)
  - No hashtags, no bold inside the post body
  - No instructional language
  - UK-style hedging language present

4. Update the post's `Post Content` field in Notion with the revised text
5. Reply to each comment thread with: `Applied.`
6. Set the post's `Status` to `Generated`

---

### Step 3: Report to User

After processing all posts:

```
Edits applied — N post(s) updated

[Post Title]
  Comments addressed: N
  Status: → Generated

[Post Title]
  Comments addressed: N
  Status: → Generated

Note: Comments remain open in Notion — resolve them manually once you've reviewed the changes.
```

---

## Important Rules

- Never change content that wasn't mentioned in a comment
- Apply edits faithfully — do not rewrite the post beyond what the comment requests
- If an edit would violate `instructions.txt` rules, fix the violation while honoring the spirit of the edit
- Never set status to `Published` — only back to `Generated`
- Process posts one at a time, sequentially
