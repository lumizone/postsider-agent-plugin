# PostSider

This extension connects Gemini CLI to the user's PostSider workspace (social media calendar) through the
hosted MCP server `postsider` (`https://mcp.postsider.com/mcp`). Sign in once with `/mcp auth postsider`.

When the user asks to post, schedule, review or fix social media posts, use the PostSider tools and the
`postsider` skill (`skills/postsider/SKILL.md`). Rules that always apply:

- Default to `draft`. Use `schedule` or `now` only when the user approved the exact text, channels and
  timing in this conversation. `now` publishes publicly and immediately.
- Show the final text and channel list before any create or update call.
- Deleting is permanent (`postsider_delete_post` removes every channel version of a post). Read first and
  confirm with the user. The same goes for deleting media or library items and for disconnecting a channel.
- Publishing is asynchronous: after `postsider_create_post`, read the post back with `postsider_get_post`
  until it is `PUBLISHED` or `ERROR`. Fix failures with `postsider_republish_post`, never by creating a
  duplicate.
- Report only what a read-back confirms.
