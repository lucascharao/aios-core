# STORIES 3.1-3.5: Quality Gates 3 Layers Implementation

**Épico:** [EPIC-S3](../../../epics/epic-s3-quality-templates.md) | **Sprint:** 3 | **Created:** 2025-01-19

**CRITICAL:** Estas stories implementam os 3 layers de Quality Gates conforme [Decisão 4](../../../audits/PEDRO-DECISION-LOG.md#decisão-4)

---

## STORY 3.1: Pre-Commit Hooks (Layer 1)

**Points:** 5 | **Priority:** 🔴 Critical

### User Story
**Como** developer, **Quero** validation automática antes de commit, **Para** catch erros em < 5s

### Scope

**Layer 1: Local (< 5s)**
- Executor: Worker (deterministic, fast, cheap)
- Tools: ESLint, Prettier, TypeScript, Husky, lint-staged

```javascript
// .husky/pre-commit
#!/bin/sh
. "$(dirname "$0")/_/husky.sh"

npx lint-staged
```

```json
// package.json
{
  "lint-staged": {
    "*.{js,ts}": ["eslint --fix", "prettier --write"],
    "*.md": ["prettier --write"]
  }
}
```

### Tasks (5 pts = 2 dias)
- [ ] 3.1.1: Install Husky + lint-staged (2h)
- [ ] 3.1.2: Configure ESLint rules (3h)
- [ ] 3.1.3: Configure Prettier (1h)
- [ ] 3.1.4: Configure TypeScript checks (2h)
- [ ] 3.1.5: Test pre-commit hook (2h)
- [ ] 3.1.6: Performance optimization (< 5s) (3h)

**Total:** 13h

---

## STORY 3.2: CodeRabbit Local Extension

**Points:** 5 | **Priority:** 🔴 Critical

### User Story
**Como** developer, **Quero** CodeRabbit local na IDE, **Para** feedback imediato durante coding

### Scope

**IDE Extension Integration:**
- Cursor: CodeRabbit extension
- Windsurf: CodeRabbit extension
- Trae: CodeRabbit extension
- Zed: CodeRabbit extension
- Antigravity: CodeRabbit extension
- Continue.dev: CodeRabbit extension

**Setup em Sprint 1 installer:**
1. Detecta IDE instalada
2. Recomenda CodeRabbit extension
3. Fornece link de instalação
4. (Optional) Auto-install se IDE suporta

### Tasks (5 pts = 2 dias)
- [ ] 3.2.1: Research CodeRabbit IDE extensions (3h)
- [ ] 3.2.2: Create setup guide per IDE (4h)
- [ ] 3.2.3: Test in 6 IDEs (6h)
- [ ] 3.2.4: Document setup process (2h)

**Total:** 15h

---

## STORY 3.3: PR Automation (Layer 2)

**Points:** 5 | **Priority:** 🔴 Critical

### User Story
**Como** developer, **Quero** automação em PRs, **Para** validation completa antes de merge

### Scope

**Layer 2: PR (< 3min)**
- Executor: Agent (Quinn) + CodeRabbit GitHub App
- GitHub Actions workflow

```yaml
# .github/workflows/pr-validation.yml
name: PR Validation
on: [pull_request]
jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Run tests
        run: npm test
      - name: CodeRabbit analysis
        uses: coderabbitai/coderabbit-action@v1
      - name: Quinn QA Agent
        run: npx aios agents run quinn --task=pr-review
```

### Tasks (5 pts = 2 dias)
- [ ] 3.3.1: Create GitHub Actions workflow (3h)
- [ ] 3.3.2: Integrate CodeRabbit GitHub App (3h)
- [ ] 3.3.3: Configure test runner (2h)
- [ ] 3.3.4: Test on real PRs (4h)

**Total:** 12h

---

## STORY 3.4: Quinn Layer 2 Integration

**Points:** 8 | **Priority:** 🔴 Critical

### User Story
**Como** QA (Quinn), **Quero** revisar PRs automaticamente, **Para** catch logic issues que linters não detectam

### Scope

**Quinn's Role in Layer 2:**
- Analyze code changes
- Check acceptance criteria compliance
- Verify test coverage
- Detect logic issues
- Generate review comments
- **🆕 Integrate with CodeRabbit output** for comprehensive analysis

```javascript
// Quinn's PR review task
async function reviewPR(prNumber) {
  const diff = await github.getPRDiff(prNumber);
  const story = await findRelatedStory(prNumber);
  
  const analysis = await analyzeCode(diff, {
    acceptanceCriteria: story.acceptanceCriteria,
    testCoverage: true,
    logicIssues: true
  });
  
  await postReview(prNumber, analysis);
}
```

### Tasks (8 pts = 3 dias)
- [ ] 3.4.1: Implement PR analysis logic (8h)
- [ ] 3.4.2: Acceptance criteria checker (5h)
- [ ] 3.4.3: Test coverage analyzer (4h)
- [ ] 3.4.4: Logic issue detector (5h)
- [ ] 3.4.5: **🆕 CodeRabbit output integration** (3h)
- [ ] 3.4.6: Review comment generator (3h)
- [ ] 3.4.7: Test with 10+ PRs (5h)

**Total:** 33h

---

## STORY 3.5: Human Review Orchestration (Layer 3)

**Points:** 5 | **Priority:** 🟠 High

### User Story
**Como** tech lead, **Quero** review estratégico apenas, **Para** focar em architecture e não syntax

### Scope

**Layer 3: Human (30min vs. 2-4h)**
- Executor: Humano (strategic decisions only)
- Focus: Architecture, business logic, UX, security

**Orchestration:**
1. Layers 1+2 pass → Notify human reviewer
2. Layers 1+2 fail → Block human review (fix first)
3. **🆕 Provide CodeRabbit + Quinn summary** to human reviewer
4. Human reviews only strategic aspects
5. Human approves → Merge allowed

```javascript
// Quality Gate Manager orchestration
async function orchestrateReview(pr) {
  const layer1 = await runPreCommitChecks();
  if (!layer1.pass) return block('Fix linting first');
  
  const layer2 = await runPRAutomation();
  if (!layer2.pass) return block('Fix Quinn/CodeRabbit issues');
  
  return requestHumanReview({
    focus: ['architecture', 'business-logic', 'security'],
    skip: ['syntax', 'formatting', 'simple-logic']
  });
}
```

### Tasks (5 pts = 2 dias)
- [ ] 3.5.1: Implement orchestration logic (4h)
- [ ] 3.5.2: Human review request system (3h)
- [ ] 3.5.3: Focus area recommendations (2h)
- [ ] 3.5.4: Notification system (2h)
- [ ] 3.5.5: Test end-to-end flow (4h)

**Total:** 15h

---

## 🔗 Dependencies

**3.1 depends on:** [2.10] Quality Gate Manager  
**3.2 depends on:** [1.4] IDE Selection  
**3.3 depends on:** [3.1] Pre-commit Hooks  
**3.4 depends on:** [3.3] PR Automation  
**3.5 depends on:** [3.1, 3.3, 3.4] All layers  

---

## ✅ Shared Success Criteria

- [ ] 80% auto-catch rate achieved
- [ ] Human review time reduced 75%
- [ ] False positive rate < 15%
- [ ] 3-layer flow working end-to-end

---

**Criado por:** River 🌊 | **Based on:** [Decisão 4](../../../audits/PEDRO-DECISION-LOG.md#decisão-4) + [DECISION-4-INVESTIGATION](../../../audits/DECISION-4-QUALITY-GATES-INVESTIGATION-REPORT.md)

