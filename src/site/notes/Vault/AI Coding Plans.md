---
{"dg-publish":true,"permalink":"/vault/ai-coding-plans/"}
---


### Cursor
**Free For 1 Year for Students**
Sign up [here](https://cursor.com/students)
$20/month after that
Use any model you want. Anthropic, ChatGPT, GLM, KimiK2.5, Google Gemini etc.
### Gemini
**FREE**
Gemini CLI
### GitHub Copilot
Sign up [here](https://education.github.com/pack)
**FREE**
Pros: Use most major models; Works in GitHub.
Cons: Not as "smart" or useful as Cursor, or other CLI's like Claude Code and Codex
### Claude Code
$20/month
**Sonnet 4.5 - $3/Million input $6/Million output**
**Haiku 4.5 - $1/m input $5/m output**
> [!Note]
> Out of all the plans, you get the least usage per month from Claude Code on the $20 plan
### GLM Coding Plan
[Sign up Here](https://z.ai/subscribe?ic=UVJIH86GSY)
$6/month (message me for 10% off) (boasts 3x more usage than $20 Claude Code plan)
**GLM 4.7 - $0.6/m input $2.2/m output**
Set up with the following:
```
#!/bin/bash
export ANTHROPIC_AUTH_TOKEN='your-api-key-here'
export ANTHROPIC_BASE_URL='https://api.z.ai/api/anthropic'
export API_TIMEOUT_MS='3000000'
claude "$@"
```
Make a file in `~/.local/bin/` called `claudez`
run `chmod +x ~/.local/bin/claudez`
Now run `claudez` to run GLM 4.7 in Claude Code
### Kimi K2.5
$19/month
[Kimi CLI Install Instructions](https://moonshotai.github.io/kimi-cli/en/guides/getting-started.html)
**Kimi K2.5 - $0.1/m input $3/m output**
To use in Claude Code, set up with the following:
```
#!/bin/bash
export ANTHROPIC_AUTH_TOKEN='your-api-key'
export ANTHROPIC_BASE_URL='https://api.kimi.com/coding/'
export ANTHROPIC_MODEL='moonshotai/kimi-k2.5'
export ANTHROPIC_SMALL_FAST_MODEL='moonshotai/kimi-k2.5'
claude "$@"

```
Make a file in `~/.local/bin/` called `claudek`
run `chmod +x ~/.local/bin/claudek`
Now run `claudek` to run Kimi K2.5 in Claude Code

### ChatGPT
$20/month
Codex CLI
>[!Note]
>You get a decent amount of usage on the $20 plan for ChatGPT