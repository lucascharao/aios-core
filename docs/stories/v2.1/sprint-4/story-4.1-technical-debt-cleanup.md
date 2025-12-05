# STORY 4.1: Technical Debt Cleanup Sprint

**ID:** 4.1 | **Epic:** [EPIC-S4](../../../epics/epic-s4-stabilization.md)
**Sprint:** 4 | **Points:** 5 | **Priority:** 🟠 High | **Created:** 2025-12-05
**Updated:** 2025-12-05
**Status:** 📋 Ready

**Consolidates:** Backlog items #1763298742141, #1732891500001, #1732891500002, #1732978800001, #1732891500003

---

## User Story

**Como** tech lead,
**Quero** eliminar technical debt acumulado durante Sprint 3,
**Para que** a base de código esteja sólida antes de iniciar novas features.

---

## Background

Durante o Sprint 3, foram identificados 5 itens de technical debt através de:
- Code reviews do CodeRabbit
- Análise do @qa agent (Quinn)
- Feedback do GitHub Devops automation

Esses itens foram registrados no backlog mas priorizados após a conclusão das features principais do Sprint 3.

---

## Acceptance Criteria

### Task 1: Decision Log Generator Tests (2h)
- [x] AC4.1.1: Criar unit tests para `decision-log-generator`
- [x] AC4.1.2: Cobertura mínima de 80% no módulo
- [x] AC4.1.3: Testes incluem casos de edge (empty input, malformed data)
- [x] AC4.1.4: Testes executam em < 5 segundos

**Source:** Backlog #1763298742141 | **Related:** Story 6.1.2.6

### Task 2: Core Module Security Hardening (4h)
- [x] AC4.1.5: Implementar sanitização de input em todos entry points
- [x] AC4.1.6: Adicionar validação de paths para prevenir path traversal
- [x] AC4.1.7: Implementar rate limiting básico onde aplicável
- [x] AC4.1.8: Revisar e corrigir todos findings críticos do CodeRabbit

**Source:** Backlog #1732891500001 | **Related:** Story 2.2

### Task 3: Core Module Code Quality (2h)
- [ ] AC4.1.9: Resolver todos warnings de ESLint pendentes
- [ ] AC4.1.10: Corrigir inconsistências de naming convention
- [ ] AC4.1.11: Remover código morto (unused imports, dead code)
- [ ] AC4.1.12: Adicionar JSDoc para funções públicas não documentadas

**Source:** Backlog #1732891500002 | **Related:** Story 2.2

### Task 4: Fix Pre-existing Test Failures (30min)
- [ ] AC4.1.13: Identificar e listar todos os testes falhando
- [ ] AC4.1.14: Corrigir ou skip (com justificativa documentada) cada teste
- [ ] AC4.1.15: `npm test` passa com 0 failures
- [ ] AC4.1.16: Documentar testes skippados em `docs/qa/skipped-tests.md`

**Source:** Backlog #1732978800001 | **Related:** Story 1.4

**Skip Policy (AC4.1.14):**
- Max 3 tests podem ser skippados nesta story
- Justificativas válidas: flaky test, dependência externa indisponível, feature removida
- Justificativas inválidas: "difícil de corrigir", "falta tempo"
- Cada skip DEVE incluir: motivo, data, owner para futuro fix

### Task 5: TypeScript Definitions for Core (3h)
- [ ] AC4.1.17: Criar arquivo `types/core.d.ts` com definições principais
- [ ] AC4.1.18: Definir interfaces para todos módulos públicos
- [ ] AC4.1.19: Adicionar JSDoc com @typedef para módulos JS
- [ ] AC4.1.20: Configurar `tsconfig.json` para type checking opcional

**Source:** Backlog #1732891500003 | **Related:** Story 2.2

---

## Scope

### Files Impacted

| Task | Files |
|------|-------|
| Task 1 | `tests/decision-log-generator.test.js` (new) |
| Task 2 | `.aios-core/core/**/*.js`, `.aios-core/utils/**/*.js` |
| Task 3 | `.aios-core/**/*.js` (multiple) |
| Task 4 | `tests/**/*.test.js` |
| Task 5 | `types/core.d.ts` (new), `tsconfig.json` |

### Out of Scope
- Refatoração de arquitetura (será Epic separado)
- Migração completa para TypeScript
- Mudanças em APIs públicas

---

## Technical Notes

### Task 1: Testing Strategy
```javascript
// tests/decision-log-generator.test.js
describe('DecisionLogGenerator', () => {
  describe('generateEntry()', () => {
    it('should create valid entry from decision data');
    it('should handle empty alternatives array');
    it('should sanitize markdown special characters');
  });

  describe('appendToLog()', () => {
    it('should create file if not exists');
    it('should append without corrupting existing content');
  });
});
```

### Task 2: Security Checklist
- [ ] Input validation on all user-provided paths
- [ ] Escape special characters in shell commands
- [ ] Validate JSON before parsing
- [ ] Check file permissions before operations

### Task 5: TypeScript Approach
```typescript
// types/core.d.ts
declare module '@aios/core' {
  export interface AgentConfig {
    name: string;
    persona: string;
    capabilities: string[];
  }

  export interface TaskResult {
    success: boolean;
    output: string;
    errors?: string[];
  }

  export function loadAgent(name: string): Promise<AgentConfig>;
  export function executeTask(task: string, context: object): Promise<TaskResult>;
}
```

---

## Testing Guidance

### Test Approach
| Task | Test Type | Coverage Target |
|------|-----------|-----------------|
| Task 1 | Unit Tests | 80% minimum |
| Task 2 | Unit + Integration | Security-focused |
| Task 3 | Linting (automated) | 0 errors |
| Task 4 | Existing tests | 100% passing |
| Task 5 | Type checking | 0 errors |

### Key Test Scenarios
1. **Decision Log Generator (Task 1)**
   - Empty input handling
   - Malformed data resilience
   - Markdown special character escaping
   - File creation and append operations

2. **Security Hardening (Task 2)**
   - Path traversal attempts (`../`, `..\\`)
   - Shell injection characters (`;`, `|`, `$()`)
   - Malformed JSON handling
   - Permission boundary testing

3. **Test Failures (Task 4)**
   - Run `npm test` and capture all failures
   - Each failure must be resolved or documented

### Success Criteria
- `npm test` exits with code 0
- `npm run lint` shows 0 errors
- `npm run typecheck` shows 0 errors (if configured)
- Coverage report shows >= 80% for new test files

---

## 🤖 CodeRabbit Integration

### Story Type Analysis
- **Primary Type:** Code Quality / Refactoring
- **Complexity:** Medium
- **Secondary Types:** Security (Task 2), Testing (Task 1, 4)

### Specialized Agents
| Agent | Role | Tasks |
|-------|------|-------|
| @dev | Primary implementation | All tasks |
| @qa | Test validation | Tasks 1, 4 |
| @architect | Security review | Task 2 |

### Quality Gates

| Gate | Agent | Trigger | Focus |
|------|-------|---------|-------|
| Pre-Commit | @dev | Each task completion | ESLint, TypeScript |
| Pre-PR | @github-devops | Before PR creation | Full CodeRabbit scan |

### Self-Healing Configuration
```yaml
mode: light              # Dev-led story
max_iterations: 2        # Keep iterations low
timeout_minutes: 15      # Quick feedback loop
severity_threshold: CRITICAL  # Only block on critical
```

### Severity Handling
| Severity | Action | Example |
|----------|--------|---------|
| CRITICAL | Auto-fix required | Security vulnerability |
| HIGH | Auto-fix recommended | Missing input validation |
| MEDIUM | Document as tech debt | Code smell |
| LOW | Note in review | Style suggestion |

### Focus Areas
- [ ] ESLint rule compliance (Task 3)
- [ ] Path traversal prevention (Task 2)
- [ ] Input sanitization patterns (Task 2)
- [ ] TypeScript type safety (Task 5)
- [ ] Test coverage thresholds (Task 1)

---

## Definition of Done

- [ ] Todos os ACs marcados como complete
- [ ] `npm test` passa com 100% success
- [ ] `npm run lint` sem errors
- [ ] `npm run typecheck` sem errors (se aplicável)
- [ ] PR aprovado com code review
- [ ] Items removidos do backlog.md
- [ ] Documentação atualizada se necessário

---

## Effort Estimate

| Task | Estimate | Complexity |
|------|----------|------------|
| Task 1: Decision Log Tests | 2h | Medium |
| Task 2: Security Hardening | 4h | High |
| Task 3: Code Quality | 2h | Low |
| Task 4: Fix Test Failures | 30min | Low |
| Task 5: TypeScript Defs | 3h | Medium |
| **Total** | **11.5h** | - |

---

## Dependencies

- **Requires:** Sprint 3 complete ✅
- **Blocks:** Epic OSR (open-source readiness)

---

## References

- [Backlog](../../backlog.md)
- [Story 2.2 - Core Module](../sprint-2/story-2.2-core-module.md)
- [Story 6.1.2.6 - Framework Config System](../../aios%20migration/story-6.1.2.6-framework-config-system.md)
- [CodeRabbit Config](../../../../.coderabbit.yaml)

---

*AIOS-FULLSTACK Story 4.1 - Technical Debt Cleanup*
