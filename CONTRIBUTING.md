# Contributing to Driveline

Thanks for helping. The full rules are in [Engineering Standards](docs/03-engineering-standards.md); this is the short version.

## Workflow

1. **Pick or open an issue.** Work starts from an issue that meets the Definition of Ready (user story, acceptance criteria, requirement ID).
2. **Branch from `main`:** `feat/<issue>-short-name`, `fix/…`, `docs/…`, `chore/…`, `spike/…`.
3. **Build with tests.** Business logic goes in `packages/domain` with unit tests.
4. **Commit with Conventional Commits:** `feat(fillups): pre-fill station from last entry`.
5. **Open a pull request** using the template. Keep it small (< 400 lines changed, excluding tests and generated code).
6. **CI must be green** and the PR approved before merging. Squash-merge into `main`.

## Significant decisions

If your change adds a framework or vendor, changes the data model, sync protocol, API or security model, or would be expensive to reverse, write an ADR in `docs/adr/` (see the [template](docs/adr/template.md)).

## Documentation

Update the relevant doc in the same PR as the code: PRD for behaviour, architecture for structure, spec for calculations, `CHANGELOG.md` for anything user-visible.

## Code of conduct

Be kind, be specific, assume good intent. Review the code, not the person.
