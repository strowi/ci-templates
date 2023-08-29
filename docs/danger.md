# Danger-Bot

> Danger runs during your CI process, and gives teams the chance to automate common code review chores.
> This provides another logical step in your build, through this Danger can help lint your rote tasks in daily code review.
> You can use Danger to codify your teams norms. Leaving humans to think about harder problems.
> She does this by leaving messages inside your PRs based on rules that you create with the Ruby scripting language.
> Over time, as rules are adhered to, the message is amended to reflect the current state of the code review.

- ref: <https://danger.systems/>
- some examples: <https://git.panter.ch/open-source/danger-rules/-/tree/master/rules>
- awesome danger: <https://github.com/danger/awesome-danger>

Template for running the danger-bot during ci.

## Usage

Create your usual `Dangerfile`, and add a ci-job:

```yaml
---
include:
  - project: 'strowi/ci-templates'
    file: '/danger.yml'

stages:
  - review

danger:
  stage: review
  extends: .danger

```

This also requires a CI/CD-Variable with API-access to the Project  named `DANGER_GITLAB_API_TOKEN`.
