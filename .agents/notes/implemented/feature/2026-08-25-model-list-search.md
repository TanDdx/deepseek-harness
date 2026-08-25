# Agent Note: Search in the composer model list

Status: implemented

## Problem

The composer model list renders every advertised row provider-grouped, so picking a known model inside a large catalog means scrolling or reading every group. Model discovery degrades exactly where selection happens, and the dynamic-plugin prototype that validated the demand (a session-local lower-priority seat shadow) cannot be the durable answer, because shipped UI cannot depend on process-local extensions.

## Decision

The drilled-in Model pane of the composer seat owns a search box ([session model selector](2026-07-24-web-session-model-selector.md)). Whitespace-separated tokens match case-insensitively and all must hit somewhere in provider name, provider id, model name, id, or description. Enter submits the first surviving row through the unchanged shared selection path, ArrowDown moves focus from the input into the list, Escape clears a non-empty query before the shipped back-out ladder resumes, and a fully filtered-out catalog shows a per-query empty message beside the untouched no-models-at-all message. Entering or reopening the pane resets the query. The `/model` popupSelect keeps its full unfiltered option list.

## Alternatives considered

**Shadow the seat from a separate package or a dynamic plugin.** A lower-priority second occupant leaves `ui-model-selection` untouched but ships two competing triggers for one affordance and duplicates the effort-pane state behind them; rejected as permanent architecture and used only as the throwaway validation prototype.

**Filter the `/model` popup too.** The popup shell consumes plain option arrays from any contributor, so per-entry filtering needs a command-UI extension point that no package owns yet; recorded as the package README's limitation instead.

**Fuzzy or scored client-side matching.** Required-substring tokens stay explainable against adapter-owned names and ids; relevance scoring adds vocabulary assumptions without a current consumer.

## Consequences

Large catalogs select in one keystroke from the composer, and the filter rides the existing directory store, so no wire, persistence, or Host contract moved. The popup and the seat now differ in capability, which the package README records until the command UI grows a filtering seam.

## Testing

`tests/model-select.client.spec.tsx` pins query filtering, provider-token matching, Enter-first-match submission with close, and the clear-before-back-out Escape ladder; `DSH_SNAPSHOT=replay pnpm run test:web` covers the assembled browser output.
