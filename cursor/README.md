# PostSider for Cursor

Your own social media calendar in Cursor, through the hosted
[PostSider](https://postsider.com) MCP server. Plan, draft, schedule, publish and review posts on 30+
networks, and let a human approve what goes live.

Ask things like:

- "Draft a LinkedIn and X post about our release and put it in the next free slot."
- "What is scheduled for the next 14 days? Which posts failed, and why?"
- "Attach this image to the draft and send it for approval."
- "How did last month's posts perform on Instagram?"
- "Give me the morning check: errors, stuck posts, channels that need a reconnect."

## What is in this plugin

- **The PostSider MCP server**: the remote server `https://mcp.postsider.com/mcp` (`mcp.json`). It brings
  34 tools: channels, content calendar, channel rules and posting queues, media
  uploads, drafts, scheduling and publishing, approval requests, failure triage, notifications,
  analytics, saved hashtag groups and caption templates, and an AI post checker and caption rewrite.
- **The `postsider` skill**: tells the agent how to use those tools well: default to drafts, read every write
  back, show the exact text, channels and timing before anything goes live, and confirm before anything
  permanent.

## Set up

1. Add the plugin from the Cursor marketplace, or add the server alone with this link:
   `cursor://anysphere.cursor-deeplink/mcp/install?name=postsider&config=eyJ1cmwiOiJodHRwczovL21jcC5wb3N0c2lkZXIuY29tL21jcCJ9`
2. Open Cursor Settings, then MCP (or Tools & Integrations), choose `postsider` and click **Connect**. Sign
   in to PostSider and allow access (read posts, write posts, manage channels). A 7-day trial with no
   credit card is available at https://app.postsider.com/register. The channels you connected in PostSider
   are the ones the agent can see.

## Plans

PostSider starts with a 7-day trial and no credit card. Paid plans start at $20 a month per organization
and include MCP, API and webhook access on every tier. Details: https://postsider.com/pricing/

## What this plugin sends and runs

- Every tool call goes to PostSider's hosted server (`mcp.postsider.com`), which acts on the user's own
  PostSider workspace and, when posts go out, calls the social networks the user connected in PostSider.
  Post text, media and the other arguments of a call are handled by PostSider as part of the user's
  account, under its privacy policy.
- The tools are served by that server at runtime; this plugin holds only the server address and the
  skill. It runs nothing on your computer, has no hooks and no scripts, and sends no telemetry.
- **Write tools have real effects.** They can create, edit, schedule and delete posts, and a post created
  with type `now` or `schedule` is published to the user's connected public social accounts. They can also
  upload and delete media, change channel settings and posting slots, connect or disconnect channels, and
  pause publishing for the whole organization (only a person can resume it). Every tool carries
  `readOnlyHint` / `destructiveHint` annotations, and the skill tells the agent to default to drafts and to
  confirm exact text, channels and timing first.
- Actions count toward the user's PostSider plan (posts per month, channels, and a monthly allowance for
  the AI post checker and caption rewrite).

Privacy policy: https://postsider.com/privacy/ · Terms: https://postsider.com/terms/ ·
Contact: lukasz@postsider.com

## License

MIT. PostSider itself is a hosted service.
