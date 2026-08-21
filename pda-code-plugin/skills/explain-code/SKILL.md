---
name: explain-code
description: Explain code in a understandable way
---

Explain the code as $1 format:
$0

If format is "plain text", provide a text summary in a simple way so I can understand it. Give me a little bit of context about codebase special words, talk in ASD-STE100 Simplified Techinical English. It should be single simple active sentence with bullet points that can can skim through easily. No need to explain much the reason for the code. For example: "The worker calls a “sink” object.". I can skim "the worker" -> " a calls" -> "sink object" and if I dont understand "sink" since it is specific to codebase, I can look into the context. The answer should look like this:

```
* **Context:**
  * **Agent ID:** When a conversation finishes, some other part of the system pushes an “agent ID” onto queue
    * The queue is a list in Redis.
    * Agent ID is a tuple `space_id, user_id, team_id, agent_id`
  * **Candidate**: A log of what the LLM actually did during this extraction
    * ```json
       [
        { "action": "create", "name": "deploy-tencentdb-cluster", "skill_id": "sk_8f3a2b91", "version": 1, "description": "Steps to deploy a new
        TencentDB cluster via the internal console." },
        { "action": "update", "name": "deploy-tencentdb-cluster", "skill_id": "sk_8f3a2b91", "version": 2 },
        { "action": "patch",  "name": "deploy-tencentdb-cluster", "skill_id": "sk_8f3a2b91", "version": 3 },
        { "action": "files_write", "name": "deploy-tencentdb-cluster", "skill_id": "sk_8f3a2b91", "version": 4 }
       ]
      ```
    * **Sink**: The endpoint where data flows to and stops
        * It filter out candidates with action: “create”
        * It registers the new skill in UI
        * If fail:
        * It logs a warning
* **Detail:**


  1. **Get an agent**: The worker waits for an agent ID from the queue
  2. **Get a lock:**  The worker locks this agent
  3. **Get the next task:**
     * The worker reads the task list for this agent
     * The worker take the first task
  4. **Read the conversation:**
     * The worker reads the full conversation for this task
     * If the conversation is missing:
       * The worker marks the task as a “ghost task”
       * The worker  get back the candidates
  5. **Call the extractor:**
     * The worker sends the conversation to skill extractor
     * The worker gets back the candidates
  6. **Save the candidates:**
     * The worker calls a “sink” object
     * The sink saves the candidates to the real skill storage
  7. **Remove the task:**
     * The worker deletes the finished task from the task list
  8. **Release the lock:**
     * The worker unlocks the agent
```


If format is "diagram", create a visual representation of the code flow like this using `/artifact-diagramming` skill:

```
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>Extract Skill Flowchart</title>
<style>
  html,body{
    margin:0;
    padding:40px 20px;
    background:#fbfaf7;
    color:#1c2a3a;
    font-family:"Times New Roman", Times, serif;
    display:flex;
    justify-content:center;
  }
  svg{ display:block; max-width:100%; height:auto; }
  svg text{ fill:#1c2a3a; font-family:"Times New Roman", Times, serif; }
  .lbl{ font-style:italic; }
  .branch{ font-size:13px; font-weight:bold; }
  .branch-warn{ fill:#9a4a1f; }
  .lane-label{ fill:#7a6a52; font-size:12px; }
</style>
</head>
<body>

<svg viewBox="-120 0 1200 1560" width="1200" height="1560" role="img"
     aria-label="Flowchart of the skill extraction worker. Begin, wait for the next agent, acquire its extract lock, then loop. Inside the loop: if the task queue is empty, exit the loop, release the lock, and end. Otherwise read the archive; if it is missing, drop the ghost task and loop again. Otherwise send the transcript and skill list to the LLM. If that call fails, check whether the error is permanent. A transient error sleeps and loops again. A permanent error increments a retry count; below the maximum it loops again, at the maximum the task moves to a dead letter queue and then loops again. If the call succeeds, save the candidates and remove the task, then loop again.">
<defs>
  <marker id="arrow" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="8" markerHeight="8" orient="auto-start-reverse">
    <path d="M0,0 L10,5 L0,10 z" fill="#1c2a3a"/>
  </marker>
  <marker id="arrow-warn" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="8" markerHeight="8" orient="auto-start-reverse">
    <path d="M0,0 L10,5 L0,10 z" fill="#9a4a1f"/>
  </marker>
</defs>

<!-- ============================================================ -->
<!-- MAIN SPINE (center column, x = 480)                          -->
<!-- ============================================================ -->

<!-- BEGIN -->
<ellipse cx="480" cy="40" rx="70" ry="26" fill="#dcece2" stroke="#1c2a3a" stroke-width="1.6"/>
<text x="480" y="46" text-anchor="middle" font-size="15">BEGIN</text>
<line x1="480" y1="66" x2="480" y2="104" stroke="#1c2a3a" stroke-width="1.6" marker-end="url(#arrow)"/>

<!-- Wait for agent -->
<polygon points="410,104 570,104 542,158 382,158" fill="#e4ecf5" stroke="#1c2a3a" stroke-width="1.6"/>
<text x="480" y="126" text-anchor="middle" font-size="14">Wait for next</text>
<text x="480" y="144" text-anchor="middle" font-size="14"><tspan class="lbl">Agent</tspan> in queue</text>
<line x1="480" y1="158" x2="480" y2="196" stroke="#1c2a3a" stroke-width="1.6" marker-end="url(#arrow)"/>

<!-- Acquire lock -->
<rect x="370" y="196" width="220" height="56" rx="2" fill="#eef1f6" stroke="#1c2a3a" stroke-width="1.6"/>
<text x="480" y="219" text-anchor="middle" font-size="14">Acquire <tspan class="lbl">Extract Lock</tspan></text>
<text x="480" y="238" text-anchor="middle" font-size="14">for <tspan class="lbl">Agent</tspan></text>
<line x1="480" y1="252" x2="480" y2="288" stroke="#1c2a3a" stroke-width="1.6" marker-end="url(#arrow)"/>

<!-- DO hexagon (single loop re-entry point) -->
<polygon points="400,288 560,288 588,316 560,344 400,344 372,316" fill="#f2e6c9" stroke="#1c2a3a" stroke-width="1.6"/>
<text x="480" y="322" text-anchor="middle" font-size="15" font-weight="bold">DO</text>
<line x1="480" y1="344" x2="480" y2="380" stroke="#1c2a3a" stroke-width="1.6" marker-end="url(#arrow)"/>

<!-- Decision 1: Task Queue empty? -->
<polygon points="480,380 590,440 480,500 370,440" fill="#fbeecb" stroke="#1c2a3a" stroke-width="1.6"/>
<text x="480" y="434" text-anchor="middle" font-size="13">If <tspan class="lbl">Task Queue</tspan></text>
<text x="480" y="452" text-anchor="middle" font-size="13">is empty</text>

<!-- true -> far right lane, straight down to release-lock -->
<line x1="590" y1="440" x2="960" y2="440" stroke="#1c2a3a" stroke-width="1.6"/>
<text x="775" y="428" text-anchor="middle" class="branch">true</text>
<line x1="960" y1="440" x2="960" y2="1400" stroke="#1c2a3a" stroke-width="1.6" marker-end="url(#arrow)"/>

<!-- false -> continue down spine -->
<text x="500" y="524" text-anchor="middle" class="branch">false</text>
<line x1="480" y1="500" x2="480" y2="536" stroke="#1c2a3a" stroke-width="1.6" marker-end="url(#arrow)"/>

<!-- Read archive -->
<rect x="370" y="536" width="220" height="56" rx="2" fill="#eef1f6" stroke="#1c2a3a" stroke-width="1.6"/>
<text x="480" y="559" text-anchor="middle" font-size="14">Read <tspan class="lbl">Archive</tspan> for</text>
<text x="480" y="578" text-anchor="middle" font-size="14">head of <tspan class="lbl">Task Queue</tspan></text>
<line x1="480" y1="592" x2="480" y2="628" stroke="#1c2a3a" stroke-width="1.6" marker-end="url(#arrow)"/>

<!-- Decision 2: archive missing? -->
<polygon points="480,628 590,688 480,748 370,688" fill="#fbeecb" stroke="#1c2a3a" stroke-width="1.6"/>
<text x="480" y="682" text-anchor="middle" font-size="13">If <tspan class="lbl">Archive</tspan></text>
<text x="480" y="700" text-anchor="middle" font-size="13">is missing</text>

<!-- true (ghost) -> right, down to the shared loop-back lane at x=800 -->
<line x1="590" y1="688" x2="800" y2="688" stroke="#1c2a3a" stroke-width="1.6"/>
<text x="700" y="676" text-anchor="middle" class="branch">true</text>
<line x1="800" y1="688" x2="800" y2="1230" stroke="#1c2a3a" stroke-width="1.6"/>
<text x="782" y="960" text-anchor="middle" class="lane-label" transform="rotate(-90 782 960)">ghost task — nothing to save</text>

<!-- false -> continue down spine -->
<text x="500" y="772" text-anchor="middle" class="branch">false</text>
<line x1="480" y1="748" x2="480" y2="784" stroke="#1c2a3a" stroke-width="1.6" marker-end="url(#arrow)"/>

<!-- Call LLM -->
<rect x="350" y="784" width="260" height="72" rx="2" fill="#e4ecf5" stroke="#1c2a3a" stroke-width="1.6"/>
<text x="480" y="808" text-anchor="middle" font-size="14">Send <tspan class="lbl">Transcript</tspan> and</text>
<text x="480" y="827" text-anchor="middle" font-size="14"><tspan class="lbl">Skill List</tspan> to LLM;</text>
<text x="480" y="846" text-anchor="middle" font-size="14">LLM calls save tools</text>
<line x1="480" y1="856" x2="480" y2="892" stroke="#1c2a3a" stroke-width="1.6" marker-end="url(#arrow)"/>

<!-- Decision 3: extraction failed? -->
<polygon points="480,892 590,952 480,1012 370,952" fill="#fbeecb" stroke="#1c2a3a" stroke-width="1.6"/>
<text x="480" y="946" text-anchor="middle" font-size="13">If extraction</text>
<text x="480" y="964" text-anchor="middle" font-size="13">failed</text>

<!-- false -> continue down spine -->
<text x="500" y="1036" text-anchor="middle" class="branch">false</text>
<line x1="480" y1="1012" x2="480" y2="1048" stroke="#1c2a3a" stroke-width="1.6" marker-end="url(#arrow)"/>

<!-- Save + remove task -->
<rect x="350" y="1048" width="260" height="72" rx="2" fill="#dcece2" stroke="#1c2a3a" stroke-width="1.6"/>
<text x="480" y="1072" text-anchor="middle" font-size="14">Save <tspan class="lbl">Candidates</tspan> to</text>
<text x="480" y="1091" text-anchor="middle" font-size="14"><tspan class="lbl">Skill Store</tspan>; remove</text>
<text x="480" y="1110" text-anchor="middle" font-size="14"><tspan class="lbl">Task</tspan> from queue</text>

<!-- from save/remove box down and right into the shared loop-back lane at x=800 -->
<line x1="480" y1="1120" x2="480" y2="1160" stroke="#1c2a3a" stroke-width="1.6"/>
<line x1="480" y1="1160" x2="800" y2="1160" stroke="#1c2a3a" stroke-width="1.6"/>
<line x1="800" y1="1230" x2="800" y2="1160" stroke="#1c2a3a" stroke-width="1.6"/>

<!-- true (failed) -> left, down to error-type diamond -->
<line x1="370" y1="952" x2="150" y2="952" stroke="#9a4a1f" stroke-width="1.6"/>
<text x="260" y="940" text-anchor="middle" class="branch branch-warn">true</text>
<line x1="150" y1="952" x2="150" y2="1040" stroke="#9a4a1f" stroke-width="1.6" marker-end="url(#arrow-warn)"/>

<!-- ============================================================ -->
<!-- ERROR-HANDLING BRANCH (left column)                          -->
<!-- ============================================================ -->

<!-- Decision 4: error permanent? -->
<polygon points="150,1040 250,1100 150,1160 50,1100" fill="#f7ded0" stroke="#9a4a1f" stroke-width="1.6"/>
<text x="150" y="1094" text-anchor="middle" font-size="13">If error is</text>
<text x="150" y="1112" text-anchor="middle" font-size="13">permanent</text>

<!-- false (transient) -> left, up, into its own loop-back lane -->
<line x1="50" y1="1100" x2="10" y2="1100" stroke="#1c2a3a" stroke-width="1.6"/>
<text x="30" y="1088" text-anchor="middle" class="branch">false</text>
<path d="M 10 1100 V 316 H 372" fill="none" stroke="#1c2a3a" stroke-width="1.6" marker-end="url(#arrow)"/>
<text x="-8" y="740" text-anchor="middle" class="lane-label" transform="rotate(-90 -8 740)">transient — sleep, then retry</text>

<!-- true (permanent) -> down to increment-retry box -->
<text x="170" y="1184" text-anchor="middle" class="branch branch-warn">true</text>
<line x1="150" y1="1160" x2="150" y2="1196" stroke="#9a4a1f" stroke-width="1.6" marker-end="url(#arrow-warn)"/>

<!-- Increment retry -->
<rect x="40" y="1196" width="220" height="56" rx="2" fill="#f7ded0" stroke="#9a4a1f" stroke-width="1.6"/>
<text x="150" y="1219" text-anchor="middle" font-size="14">Increment <tspan class="lbl">Retry</tspan></text>
<text x="150" y="1238" text-anchor="middle" font-size="14">Count by 1</text>
<line x1="150" y1="1252" x2="150" y2="1288" stroke="#9a4a1f" stroke-width="1.6" marker-end="url(#arrow-warn)"/>

<!-- Decision 5: retry at max? -->
<polygon points="150,1288 260,1348 150,1408 40,1348" fill="#f7ded0" stroke="#9a4a1f" stroke-width="1.6"/>
<text x="150" y="1342" text-anchor="middle" font-size="13">If <tspan class="lbl">Retry</tspan></text>
<text x="150" y="1360" text-anchor="middle" font-size="13">Count at max</text>

<!-- false -> left, up into its own loop-back lane -->
<line x1="40" y1="1348" x2="-70" y2="1348" stroke="#1c2a3a" stroke-width="1.6"/>
<text x="-15" y="1336" text-anchor="middle" class="branch">false</text>

<!-- true -> down, right into DLQ box -->
<text x="170" y="1432" text-anchor="middle" class="branch branch-warn">true</text>
<line x1="150" y1="1408" x2="150" y2="1444" stroke="#9a4a1f" stroke-width="1.6" marker-end="url(#arrow-warn)"/>

<rect x="40" y="1444" width="220" height="60" rx="2" fill="#efc3ad" stroke="#9a4a1f" stroke-width="1.6"/>
<text x="150" y="1468" text-anchor="middle" font-size="14">Move <tspan class="lbl">Task</tspan> to</text>
<text x="150" y="1487" text-anchor="middle" font-size="14"><tspan class="lbl">Dead-Letter Queue</tspan></text>

<!-- DLQ box: right, up into shared loop-back lane at x=800 -->
<line x1="260" y1="1474" x2="800" y2="1474" stroke="#1c2a3a" stroke-width="1.6"/>
<line x1="800" y1="1474" x2="800" y2="1230" stroke="#1c2a3a" stroke-width="1.6"/>

<!-- ============================================================ -->
<!-- Shared loop-back lane: everything arriving at x=800, y=1230  -->
<!-- rises to y=316 and re-enters DO from the right side.         -->
<!-- ============================================================ -->
<line x1="800" y1="1230" x2="800" y2="316" stroke="#1c2a3a" stroke-width="1.6"/>
<line x1="800" y1="316" x2="588" y2="316" stroke="#1c2a3a" stroke-width="1.6" marker-end="url(#arrow)"/>
<text x="818" y="770" text-anchor="middle" class="lane-label" transform="rotate(-90 818 770)">back to DO — next task, same agent</text>

<!-- ============================================================ -->
<!-- Retry-not-at-max (false) lane rejoins DO directly            -->
<!-- ============================================================ -->
<path d="M -70 1348 V 316 H 372" fill="none" stroke="#1c2a3a" stroke-width="1.6" marker-end="url(#arrow)"/>
<text x="-88" y="820" text-anchor="middle" class="lane-label" transform="rotate(-90 -88 820)">below max — sleep, then loop again</text>

<!-- ============================================================ -->
<!-- Release lock + END (far right lane, x = 960)                 -->
<!-- ============================================================ -->

<polygon points="890,1400 1030,1400 1002,1454 862,1454" fill="#e4ecf5" stroke="#1c2a3a" stroke-width="1.6"/>
<text x="960" y="1422" text-anchor="middle" font-size="14">Release <tspan class="lbl">Lock</tspan>,</text>
<text x="960" y="1441" text-anchor="middle" font-size="14">go to step 1</text>
<line x1="960" y1="1454" x2="960" y2="1490" stroke="#1c2a3a" stroke-width="1.6" marker-end="url(#arrow)"/>

<ellipse cx="960" cy="1518" rx="70" ry="26" fill="#dcece2" stroke="#1c2a3a" stroke-width="1.6"/>
<text x="960" y="1524" text-anchor="middle" font-size="15">END</text>

</svg>

</body>
</html>
```


If format is "steps", create flows like this:
```
POST /api/v2/files (multipart: file, purpose=batch, endpoint=/v1/chat/completions)
  -> require file; purpose must be batch; endpoint must be supported
      -> 400 on missing or invalid values                              x
  -> validate_batch_file in sync_to_async
      -> read nonblank JSONL lines (maximum 20 MiB)
      -> require custom_id, method, url, and body.model on every line
      -> require the configured endpoint on every line
      -> enforce one service_code/model_name for the whole file
      -> HasAccessToAIModel for that model
          -> denied -> 403                                             x
      -> resolve Service and LLMModel
          -> missing service -> 404                                    x
      -> require the service and model to support Batch API
          -> disabled/unsupported -> 400                               x
      -> rewrite client model -> provider model code
      -> estimate prompt tokens from messages
      -> collect up to MAX_BATCH_VALIDATION_ERRORS errors (default 100)
          -> invalid -> 400 {message, errors: [{line, error}, ...]}    x
          -> too large -> 413                                          x
  -> allow_request(batch-rate estimated prompt cost)
      -> quota exceeded -> 429                                         x
  -> broker.acquire(service, actors)
      -> no eligible token -> 429                                      x
      -> select token A
  -> proxy.upload_file(api_key=A) -> upstream file ID
      -> OpenAI-compatible providers: LiteLLM upload
      -> Gemini: translate OpenAI JSONL, then use native resumable upload
  -> BatchFile(actor, service, token=A, upstream_id,
               client_model, actual_model, line_count, byte_size,
               estimated_prompt_tokens)
  -> 200 {id: "file-wf_<uuid>", object: "file", ...}                ok
```

If format is "example", create a flow like this:
```html
<title>Extractor Worked Example</title>
<style>
  html,body{
    margin:0;
    padding:40px 20px;
    background:#fbfaf7;
    color:#1c2a3a;
    font-family:"Times New Roman", Times, serif;
    display:flex;
    justify-content:center;
  }
  figure{ margin:0; max-width:1180px; }
  svg{ display:block; max-width:100%; height:auto; }
  svg text{ fill:#1c2a3a; font-family:"Times New Roman", Times, serif; }
  figcaption{
    margin-top:18px;
    font-size:15px;
    line-height:1.55;
    color:#4a4033;
  }
  figcaption code{ font-family:"DejaVu Sans Mono", Menlo, monospace; font-size:13px; }
  .mono{ font-family:"DejaVu Sans Mono", Menlo, monospace; }
  .lbl{ font-style:italic; }
  .step{ fill:#7a6a52; font-size:12px; font-weight:bold; letter-spacing:0.5px; }
  .note{ fill:#7a6a52; font-size:12px; }
  .edge{ font-size:12px; }
  .win{ fill:#3d6b52; font-size:12px; font-weight:bold; }
</style>

<figure>
<svg viewBox="0 0 1180 1520" width="1180" height="1520" role="img"
     aria-label="One worked run of SkillExtractor.extract with concrete data. Input is four ExtractMessages about a stuck TencentDB read-only instance. Step one formats them into a transcript with past-user and past-assistant tags ending in end-of-transcript. Step two runs core.list with limit 20 and gets 37 total skills, which exceeds the limit, so step three asks the LLM for BM25 keywords and gets tencentdb read-only instance stuck rollback, then core.search returns two hits. Step four renders the relevant prefix block naming those two skills and prepends it to the transcript, giving a 6100 character prompt in mode relevant. Step five runs the tool loop: the LLM calls skill_view on the first hit, sees the rollback command is wrong, and calls skill_patch with expected_version 4, which writes version 5 into SkillCore and appends one line to the audit sink. Step six returns candidates containing that single patch entry. The final note says the skill was already saved by the tool call, so the candidate is only a receipt.">
<defs>
  <marker id="a" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="8" markerHeight="8" orient="auto-start-reverse">
    <path d="M0,0 L10,5 L0,10 z" fill="#1c2a3a"/>
  </marker>
  <marker id="ag" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="8" markerHeight="8" orient="auto-start-reverse">
    <path d="M0,0 L10,5 L0,10 z" fill="#3d6b52"/>
  </marker>
</defs>

<!-- ═══════════ 1. INPUT ═══════════ -->
<text x="40" y="34" class="step">1 · INPUT — extract({ owner, messages })</text>

<rect x="40" y="46" width="470" height="124" rx="2" fill="#eef1f6" stroke="#1c2a3a" stroke-width="1.6"/>
<text x="58" y="68" class="mono" font-size="12">user_id: "u_921"  team_id: "t_44"  agent_id: "dba-bot"</text>
<text x="58" y="86" class="mono" font-size="12">task_id: "tk_7f21"</text>
<text x="58" y="110" class="mono" font-size="12">messages: [</text>
<text x="58" y="128" class="mono" font-size="12">  { role: "user",      content: "只读实例卡在 rollback…" },</text>
<text x="58" y="146" class="mono" font-size="12">  { role: "assistant", content: "先查 slave_status…" },  … ×4</text>
<text x="58" y="164" class="mono" font-size="12">]</text>

<line x1="510" y1="108" x2="588" y2="108" stroke="#1c2a3a" stroke-width="1.6" marker-end="url(#a)"/>
<text x="549" y="98" text-anchor="middle" class="edge">formatTranscript</text>

<rect x="588" y="46" width="552" height="124" rx="2" fill="#f7f4ec" stroke="#1c2a3a" stroke-width="1.6"/>
<text x="606" y="68" class="mono" font-size="12">&lt;&lt;past-user&gt;&gt;</text>
<text x="606" y="86" class="mono" font-size="12">只读实例卡在 rollback…</text>
<text x="606" y="110" class="mono" font-size="12">&lt;&lt;past-assistant&gt;&gt;</text>
<text x="606" y="128" class="mono" font-size="12">先查 slave_status…</text>
<text x="606" y="152" class="mono" font-size="12">&lt;&lt;end-of-transcript&gt;&gt;   ← 4 820 chars, under 8k+32k: no cut</text>

<line x1="275" y1="170" x2="275" y2="212" stroke="#1c2a3a" stroke-width="1.6" marker-end="url(#a)"/>

<!-- ═══════════ 2. LIST ═══════════ -->
<text x="40" y="240" class="step">2 · PREFETCH — core.list(owner, limit: 20)</text>

<rect x="40" y="252" width="470" height="96" rx="2" fill="#e4ecf5" stroke="#1c2a3a" stroke-width="1.6"/>
<text x="58" y="276" class="mono" font-size="12">→ { items: [ …20 skills… ], total: 37 }</text>
<text x="58" y="302" font-size="13">total 37 &gt; limit 20  →  cannot show the full inventory</text>
<text x="58" y="322" font-size="13">→ spend one query-gen call, try <tspan class="lbl">relevant</tspan></text>
<text x="58" y="340" class="note">(if total ≤ 20 the run would jump straight to step 4 as mode = full)</text>

<line x1="510" y1="300" x2="588" y2="300" stroke="#1c2a3a" stroke-width="1.6" marker-end="url(#a)"/>

<!-- ═══════════ 3. QUERY-GEN + SEARCH ═══════════ -->
<text x="588" y="240" class="step">3 · QUERY-GEN LLM + BM25</text>

<rect x="588" y="252" width="552" height="96" rx="2" fill="#e4ecf5" stroke="#1c2a3a" stroke-width="1.6"/>
<text x="606" y="274" class="mono" font-size="12">run({ enableTools: false, maxTokens: 64 }) → "TencentDB, 只读实例, rollback!"</text>
<text x="606" y="296" class="mono" font-size="12">sanitize → "TencentDB 只读实例 rollback"</text>
<text x="606" y="320" class="mono" font-size="12">core.search(query, top_k: 20) → 2 hits</text>
<text x="606" y="340" class="note">empty query or 0 hits here → fall back to the recent top-20 block</text>

<line x1="864" y1="348" x2="864" y2="392" stroke="#1c2a3a" stroke-width="1.6"/>
<line x1="864" y1="392" x2="275" y2="392" stroke="#1c2a3a" stroke-width="1.6"/>
<line x1="275" y1="392" x2="275" y2="428" stroke="#1c2a3a" stroke-width="1.6" marker-end="url(#a)"/>

<!-- ═══════════ 4. PROMPT ═══════════ -->
<text x="40" y="456" class="step">4 · PROMPT — prefix block + transcript   (mode = relevant)</text>

<rect x="40" y="468" width="1100" height="180" rx="2" fill="#f2e6c9" stroke="#1c2a3a" stroke-width="1.6"/>
<text x="58" y="492" class="mono" font-size="12">## Skills possibly relevant to this conversation (query='TencentDB 只读实例 rollback', BM25 prefetched)</text>
<text x="58" y="512" class="mono" font-size="12">If any of these already covers the topic, prefer `skill_update` / `skill_patch` over creating a duplicate.</text>
<text x="58" y="536" class="mono" font-size="12">- tencentdb-readonly-stuck — 只读实例回滚卡住时的排查步骤：先看 slave_status，再看 undo…</text>
<text x="58" y="556" class="mono" font-size="12">- tencentdb-failover-drill — 主备切换演练流程</text>
<text x="58" y="580" class="mono" font-size="12">---</text>
<text x="58" y="600" class="mono" font-size="12">&lt;&lt;past-user&gt;&gt; 只读实例卡在 rollback… &lt;&lt;end-of-transcript&gt;&gt;</text>
<text x="58" y="628" font-size="13">prompt_chars ≈ 6 100  ·  a <tspan class="lbl">reason</tspan> hint from the main agent, if set, is prepended above all of this</text>

<line x1="590" y1="648" x2="590" y2="692" stroke="#1c2a3a" stroke-width="1.6" marker-end="url(#a)"/>

<!-- ═══════════ 5. TOOL LOOP ═══════════ -->
<text x="40" y="720" class="step">5 · TOOL LOOP — runner.run(prompt, 6 tools, ≤16 iterations)</text>

<!-- loop container -->
<rect x="40" y="732" width="1100" height="470" rx="2" fill="#f7f4ec" stroke="#1c2a3a" stroke-width="1.6" stroke-dasharray="6 4"/>

<!-- iteration 1 -->
<rect x="64" y="758" width="420" height="104" rx="2" fill="#e4ecf5" stroke="#1c2a3a" stroke-width="1.6"/>
<text x="82" y="780" font-size="13" font-weight="bold">iter 1 — LLM calls skill_view</text>
<text x="82" y="802" class="mono" font-size="12">{ skill_id: "sk_8f3a2b91" }</text>
<text x="82" y="826" class="mono" font-size="12">→ { version: 4, content: "…rollback 用</text>
<text x="82" y="844" class="mono" font-size="12">   `kill query` …", manifest: […] }</text>
<text x="500" y="800" class="note">read-only —</text>
<text x="500" y="818" class="note">no sink line</text>

<line x1="274" y1="862" x2="274" y2="900" stroke="#1c2a3a" stroke-width="1.6" marker-end="url(#a)"/>
<text x="292" y="886" class="note">the doc's command is wrong; one line needs fixing</text>

<!-- iteration 2 -->
<rect x="64" y="900" width="420" height="126" rx="2" fill="#dcece2" stroke="#3d6b52" stroke-width="1.8"/>
<text x="82" y="922" font-size="13" font-weight="bold">iter 2 — LLM calls skill_patch</text>
<text x="82" y="944" class="mono" font-size="12">{ skill_id: "sk_8f3a2b91",</text>
<text x="82" y="962" class="mono" font-size="12">  old_string: "kill query",</text>
<text x="82" y="980" class="mono" font-size="12">  new_string: "kill connection",</text>
<text x="82" y="998" class="mono" font-size="12">  expected_version: 4 }   ← optimistic lock</text>
<text x="82" y="1018" class="mono" font-size="12">→ { ok: true, version: 5 }</text>

<!-- writes to core -->
<line x1="484" y1="940" x2="700" y2="940" stroke="#3d6b52" stroke-width="1.8" marker-end="url(#ag)"/>
<text x="592" y="930" text-anchor="middle" class="win">writes now</text>

<rect x="700" y="900" width="410" height="60" rx="2" fill="#dcece2" stroke="#3d6b52" stroke-width="1.8"/>
<text x="905" y="924" text-anchor="middle" font-size="14"><tspan class="lbl">SkillCore</tspan> — sk_8f3a2b91 v4 → v5</text>
<text x="905" y="946" text-anchor="middle" class="note">the durable store; the edit is committed here</text>

<!-- appends to sink -->
<line x1="484" y1="1000" x2="700" y2="1000" stroke="#3d6b52" stroke-width="1.8" marker-end="url(#ag)"/>
<text x="592" y="990" text-anchor="middle" class="win">appends 1 line</text>

<rect x="700" y="972" width="410" height="70" rx="2" fill="#f7f4ec" stroke="#3d6b52" stroke-width="1.8"/>
<text x="905" y="994" text-anchor="middle" font-size="14"><tspan class="lbl">auditSink</tspan> []  — in-memory array</text>
<text x="718" y="1018" class="mono" font-size="12">{ action:"patch", name:"tencentdb-readonly-stuck",</text>
<text x="718" y="1034" class="mono" font-size="12">  skill_id:"sk_8f3a2b91", version:5 }</text>

<line x1="274" y1="1026" x2="274" y2="1064" stroke="#1c2a3a" stroke-width="1.6" marker-end="url(#a)"/>

<!-- iteration 3 -->
<rect x="64" y="1064" width="420" height="82" rx="2" fill="#eef1f6" stroke="#1c2a3a" stroke-width="1.6"/>
<text x="82" y="1086" font-size="13" font-weight="bold">iter 3 — LLM emits text, no tool call</text>
<text x="82" y="1108" class="mono" font-size="12">"Patched the rollback command. Nothing</text>
<text x="82" y="1126" class="mono" font-size="12"> else to save."   → loop ends</text>

<text x="64" y="1180" class="note">a throw anywhere in this loop → warn log + trace success=false + rethrow; the sink keeps whatever was already written</text>

<line x1="590" y1="1202" x2="590" y2="1246" stroke="#1c2a3a" stroke-width="1.6" marker-end="url(#a)"/>

<!-- ═══════════ 6. RETURN ═══════════ -->
<text x="40" y="1274" class="step">6 · RETURN</text>

<rect x="40" y="1286" width="1100" height="122" rx="2" fill="#dcece2" stroke="#1c2a3a" stroke-width="1.6"/>
<text x="58" y="1310" class="mono" font-size="12">{ candidates: [ { action:"patch", name:"tencentdb-readonly-stuck", skill_id:"sk_8f3a2b91", version:5 } ],</text>
<text x="58" y="1330" class="mono" font-size="12">  text: "Patched the rollback command. Nothing else to save." }</text>
<text x="58" y="1358" font-size="13">obsLogger: <tspan class="mono" font-size="12">{ task_id:"tk_7f21", dur_ms:11 240, msg_count:4, candidates:1, prompt_chars:6100, prefix_mode:"relevant" }</tspan></text>
<text x="58" y="1382" font-size="13">metric: <tspan class="mono" font-size="12">skill.extract.candidates = 1</tspan></text>
<text x="58" y="1400" class="note">1 candidate, 3 iterations, 2 LLM calls total (query-gen + review)</text>

<text x="40" y="1450" font-size="14" font-weight="bold">The skill is already at v5 in SkillCore before extract() returns.</text>
<text x="40" y="1472" font-size="14">The candidates array is a receipt of what the tools did — not a payload waiting to be saved.</text>

</svg>

<figcaption>
One run of <code>SkillExtractor.extract</code> with real values at every hop: 37 owned skills exceed
the limit of 20, so a 64-token query-gen call produces the BM25 query that selects the two skills
worth showing; the review LLM reads one, patches a wrong command in it under an
<code>expected_version</code> lock, and the write lands in <code>SkillCore</code> at the moment of
the tool call. The returned <code>candidates</code> array is the audit-sink log of that write.
</figcaption>
</figure>
```