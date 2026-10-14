# Agent Review output on the LombardPRO draft (templates/CLAUDE.md, Claude Code)

This is the output of the `templates/CLAUDE.md` preset run on the [LombardPRO v0.2 draft](lombard-pro.en.md): the agent stopped before writing code under the "more than 3 problems" rule.

---

## Agent Review: LombardPRO v0.2

I've read the spec. I'm not starting on code: rule 5 in CLAUDE.md says that with more than 3 problems I must stop and report them. I found about 15, and section 14 of the spec covers only some of them.

### Blockers: Phase 1 cannot start without these

1. No .xls from PawnExpert. It's not in the folder, and per the spec it is a critical prerequisite for the parser (BR-010, risk #1).
2. The DB schema is missing tables the spec refers to: staff (already referenced by FKs), buyers, clients, column_mappings, price ranges, bonus rates, cash reconciliations, shop sales, cash_wallet. The schema simply cannot be created with the current FKs.
3. No formula for the expected cash balance (US-004). Pledge issuances and redemptions go through PawnExpert. It's unclear how the system learns about cash movements and where the opening balance comes from.
4. The lifecycle of a pledge from the .xls is not described. What happens if a pledge is redeemed in PawnExpert or disappears from the export? How do the redeemed and repledge statuses arise from the import?

### Contradictions

5. Who changes the route after confirmation. US-003 and AP-004 say "via the admin panel", but the permissions matrix has ✗ for the Admin role on routing, and INV-004 requires an owner button in Telegram.
6. Route buttons don't match the API. The UI has [Cancel], but it doesn't say what that button does. Buyer B (silver) appears in neither the UI nor the API. The API returns a "routed" status that isn't in the status list.
7. INV-003 requires storing who deactivated a record and why, but the schema has no fields for that.
8. TEST-001 doesn't match the API. The test calls POST /pledges/incasso with contract_num, while the API endpoint is /pledges/{id}/incasso. The upload path also differs: /api/upload-pawnexpert vs /api/v1/upload-pawnexpert.
9. INV-005 doesn't describe the owner_approval_token mechanism: how the owner approves and how long the token lives.

### Underspecified

10. BR-004: are days counted from pledge_date or from incasso_date? And the rate is given in percent, so should it be divided by 100?
11. The time zone for 9:00 and 23:59 isn't specified. Also, US-001 says "every day", while the module says "business days".
12. Staff activation in the bot via a code (BR-001): the process itself isn't described.
13. Shop, shifts and payroll, and buyer payments have no User Stories or AC, even though the checklist marks them ✅.
14. Frontend not chosen: React or Vanilla JS.
15. Plus the open questions from section 14
