---
name: postsider
description: Plan, draft, schedule, publish and review social media posts in the user's PostSider workspace through the PostSider MCP server. Use when the user wants to post or schedule content to X, LinkedIn, Instagram, Facebook, TikTok, YouTube, Threads, Pinterest, Bluesky or other connected channels, check the content calendar, fix failed posts, attach images or video, send a draft for approval, or read post analytics.
license: MIT
compatibility: Needs the postsider MCP server (from this package, which needs Node.js 20.18.1+ for npx, or added with hermes mcp add) and a PostSider account at https://app.postsider.com.
metadata:
  homepage: https://postsider.com
  docs: https://docs.postsider.com/agent/mcp/overview
---

# PostSider

PostSider is a hosted social media calendar. Its MCP server lets you read the user's real workspace and
prepare or publish posts on their connected channels. In Hermes the tools are registered with the server
prefix, for example `mcp__postsider__postsider_list_channels`; this skill uses the bare names.

## Before the first call

- The first tool call opens a browser to sign in to PostSider and approve access. A user without an
  account can create one at https://app.postsider.com/register.
- Channels are connected by the user in the PostSider dashboard, or with `postsider_connect_channel`,
  which returns a one-time link the user must open and approve on the platform. You cannot do that step.
- If you have no other instructions, call `postsider_get_guide` with topic `publishing` once per
  conversation. Other topics: `media`, `platforms`, `troubleshooting`, `clients`.

## Publishing recipe

1. `postsider_list_channels` for channel ids. Never invent an id.
2. `postsider_get_channel_rules` before the first post to a channel: length limit, required `settings`,
   whether media is required. `postsider_run_channel_tool` fetches values a setting needs (TikTok privacy
   options, Pinterest boards) and only reads.
3. Media first, if any: `postsider_upload_media` with a public `url`, or `postsider_prepare_upload`, send the
   file with the returned `curl` command, then `postsider_finalize_upload`. Pass the returned `{id, path}`
   objects in `images`. Reuse `postsider_list_media` before uploading again.
4. `postsider_suggest_times` (`next_free` or `best`) for the `date`.
5. `postsider_create_post` with `type` `draft`, `schedule` (needs `date`) or `now`.
6. Publishing is asynchronous: the result means accepted, not published. Read the post back with
   `postsider_get_post` until it is `PUBLISHED` (report the link) or `ERROR` (report the reason). Fix a
   failed post and use `postsider_republish_post`; never create a duplicate.

## Safety rules

- Default to `draft`. Use `schedule` or `now` only when the user approved the exact text, channels and
  timing in this conversation. `now` publishes publicly and immediately.
- Show the user the final text and the channel list before any create or update call.
- Deleting is permanent: `postsider_delete_post` removes the post and every channel version of it, so read
  it first and confirm the full set with the user. The same goes for `postsider_delete_media`,
  `postsider_delete_library_item` and `postsider_disconnect_channel`.
- `postsider_pause_publishing` halts the whole organization and only a human can resume it. Use it only
  when the user asks or something is clearly going wrong, and say that resuming is theirs to do.
- `postsider_set_queue_plan` replaces the whole plan: read it with `postsider_get_queue_plan` first and send
  back the slots you keep.
- `postsider_update_post` cannot edit published posts or posts waiting for approval. Read the post first
  and check the change afterwards.
- `postsider_check_post` and `postsider_rewrite_post` use the plan's monthly AI allowance; use them when
  asked or clearly useful, not on every draft.
- Report only what a read-back confirms.

## Review and triage

- `postsider_get_overview`: channels, queued, drafts, published, errors with reasons, pending approvals,
  stuck posts and channels that need a reconnect. Good for a morning check or a client report (pass
  `customerId`).
- `postsider_list_posts` with `startDate`, `endDate` and `state` (`ERROR`, `DRAFT`, `QUEUE`, `APPROVAL`,
  `PUBLISHED`) to review the calendar; page through while `hasMore` is true.
- `postsider_get_notifications` for publish failures and reconnect warnings. `postsider_get_publishing_state`
  tells whether the organization is paused.
- `postsider_request_approval` sends an existing draft to the human approval queue; the approval decision
  itself is made by a person in PostSider.
- `postsider_get_analytics` takes `channelId` (account level) or `postId` (one published post) and a
  look-back window in days. An empty list means no data, not zero.
- `postsider_get_content_library` lists saved hashtag groups, caption templates and UTM presets; use them
  instead of inventing hashtags or tracking parameters.

## Limits

Plan limits (channels, monthly AI allowance) apply; a limit error names the plan, so tell the user to
check their plan in the PostSider dashboard instead of retrying.
