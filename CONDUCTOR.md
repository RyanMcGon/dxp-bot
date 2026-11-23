# Running a factory task in Conductor Cloud

Run these requests on the shared Grok Bot computer as the project crewmate. Supply `CONDUCTOR_API_KEY` from the crewmate's secure credential card. Keep the key out of chat, SQLite, transcripts, and workspace environment overrides.

Set up the following shell function in the shell making the requests. It targets the Conductor API and fails on HTTP errors.

```sh
api() {
  curl --silent --show-error --fail-with-body \
    -H "Authorization: Bearer ${CONDUCTOR_API_KEY:?Conductor API key required}" \
    -H 'Content-Type: application/json' "$@"
}
base=https://api.conductor.build/v0
api "$base/projects" | jq '{hasMore, offset, projects: [.data[] | {id, gitRemote}]}'
```

Match the task's repository owner and name and its forge host to the project's `gitRemote`. Follow list pagination with `limit` and `offset` while `hasMore` is true. If the match is missing or ambiguous, mark the task blocked and ask Firstmate to confirm or connect the project. Record the confirmed project ID and repository together in the project charter.

## Start or recover a task

Use a branch-safe workspace name derived from the unique factory task ID. Before creating a workspace on a resumed task, look up its saved IDs in the crewmate's task handoff. If a create request timed out before returning IDs, check `GET /v0/projects/{projectId}/workspaces` for that name and `GET /v0/workspaces/{workspaceId}/sessions` for its session. Resolve ambiguity before launching or resending work.

Prepare a self-contained `brief` with the task ID, scope and acceptance criteria. A scout returns its report in the final reply and must not push, open a PR or merge. A ship runs the project tests, pushes the workspace branch and returns its branch name, commit and test results. Reserve PR creation and merge for the crewmate's review flow.

Set `project_id`, `workspace_name` and `brief` before running this command. The default uses Codex with high effort and standard speed. Use the captain's chosen supported agent and model when configured.

```sh
jq -n --arg project "$project_id" --arg name "$workspace_name" --arg message "$brief" \
  '{projectId:$project, name:$name, message:$message, agent:"codex", model:"gpt-6.2-sol", effort:"high", fastMode:false}' |
  api --data-binary @- "$base/workspaces"
```

Save the returned `workspaceId` and `sessionId` against the task ID in the crewmate's persistent handoff notes on the shared computer. Keep the SQLite result field available for the report path or PR URL.

## Read results and send follow-ups

Read `GET /v0/sessions/{sessionId}/status` about every 15 seconds while waiting. A queued prompt can initially show idle. Read `GET /v0/sessions/{sessionId}/messages` for the assistant's reply to the current brief, following pagination and using `after=<last message id>` for new messages. Report an errored session or unresolved blocker to Firstmate.

For a scout, save the final report on the shared computer as directed by the charter. For a ship, verify the reported commit on the remote branch before starting independent review. Send review findings or red checks back to the same session, setting `session_id` and `feedback` first:

```sh
jq -n --arg message "$feedback" '{message:$message}' |
  api --data-binary @- "$base/sessions/$session_id/messages"
```

Keep the workspace available through review and checks. After the task is done and its outcome recorded, archive its workspace with `POST /v0/workspaces/{workspaceId}/archive`.

[Conductor API reference](https://www.conductor.build/docs/api)
