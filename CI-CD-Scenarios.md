# CI/CD Scenario-Based Interview Questions

## Scenario 1: Pipeline Failure in Production
**Q: Your CI/CD pipeline passes all tests but fails during the production deployment. How do you handle it?**

**A:**
1. Immediately trigger a rollback to the last stable release
2. Check deployment logs — look for environment config mismatches, missing secrets, or infra issues
3. Reproduce the failure in staging with production-equivalent config
4. Fix the root cause, add a test to catch it, re-deploy
5. Post a blameless incident report

---

## Scenario 2: Slow Pipeline (45+ minutes)
**Q: Your pipeline takes 45 minutes. Developers are complaining. How do you optimize it?**

**A:**
1. Profile each stage — identify the bottleneck (usually tests or build)
2. Parallelize independent test suites
3. Add layer caching for Docker builds
4. Split into fast (unit) and slow (integration) pipelines — run fast on every commit
5. Use incremental builds — only rebuild changed modules

---

## Scenario 3: Flaky Tests Breaking the Pipeline
**Q: One test fails intermittently and blocks all deployments. What do you do?**

**A:**
1. Quarantine the flaky test immediately (move to a separate non-blocking suite)
2. Investigate root cause — race conditions, external API calls, timing issues
3. Fix or rewrite the test with proper mocking/retries
4. Restore it to the main suite only after it passes 50+ consecutive runs

---

## Scenario 4: Hotfix Needed in Production Now
**Q: A critical bug is in production. Your normal pipeline takes 2 hours. What do you do?**

**A:**
1. Branch off the production tag, not main
2. Apply minimal fix, get expedited peer review
3. Run only smoke tests + affected unit tests (not full suite)
4. Deploy via hotfix pipeline directly to production
5. Merge fix back to main and run full pipeline async

---

## Scenario 5: Secret Leaked in Git History
**Q: A developer accidentally committed an API key. It's now in git history. What do you do?**

**A:**
1. Immediately rotate/revoke the exposed secret
2. Remove from history using `git filter-repo` or BFG Repo Cleaner
3. Force push the cleaned history
4. Audit access logs for unauthorized use of the leaked key
5. Add pre-commit hooks (e.g., `git-secrets`, `trufflehog`) to prevent recurrence
6. Store all secrets in a vault (HashiCorp Vault, AWS Secrets Manager)

---

## Scenario 6: Deployment Causes 50% Error Rate
**Q: You deployed 10 minutes ago and error rate jumped to 50%. What do you do?**

**A:**
1. Rollback immediately — don't investigate first, restore service first
2. Confirm error rate drops post-rollback
3. Compare the diff between current and previous release
4. Reproduce in staging, identify the breaking change
5. Implement canary deployments to catch this earlier next time

---

## Scenario 7: Multiple Teams, One Pipeline
**Q: 5 teams share one pipeline and constantly block each other. How do you fix it?**

**A:**
1. Give each team their own pipeline for their service
2. Implement trunk-based development with feature flags
3. Use a monorepo-aware build tool (Nx, Turborepo, Bazel) to only build affected services
4. Set up a shared pipeline library for common stages

