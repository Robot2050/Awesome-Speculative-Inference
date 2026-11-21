# Contributing to Awesome Speculative Inference

Thank you for helping improve this collection. The repository accompanies the survey Racing Against Time: Speculative Inference in the Era of Long Contexts and Long-Horizon Agents and is intended to remain a reliable, chapter aligned resource.

Contributions can add a missing paper, correct bibliographic metadata, repair a link, clarify a category, or improve the research directions. For a new section or a broad reorganization, please open an issue before preparing a pull request.

## Scope

The collection covers research on speculative inference, including:

- Exact and approximate speculative sampling
- Draft architectures, candidate trees, and verification
- Long context drafting, cache reuse, and verification cost
- Retrieval based drafting and adaptation across interactions
- Tool call generation, action execution, observation prediction, and trajectory verification
- Scheduling, overlap, resource allocation, and serving
- Benchmarks, controlled comparisons, correctness studies, and negative results

A general inference optimization paper belongs here only when speculation or verified prediction is central to the method or analysis. A general agent paper belongs here only when it contributes a mechanism or evaluation directly relevant to speculative execution.

## Placement

Use the section that best matches the primary contribution. Add each work once, even when it spans several topics. The surrounding discussion can acknowledge secondary connections without duplicating the entry.

Pay particular attention to these boundaries:

- Exact token sampling and approximate target verification make different guarantees.
- Predicting tool call text is distinct from executing a predicted action.
- Bounding draft storage is distinct from bounding the cost of reading target history.
- Fixed prefix replay, dynamic execution, and real tool execution provide different forms of evidence.
- Accepted length and prediction accuracy are not substitutes for complete runtime or task measurements.

## Entry Format

Keep the format consistent with neighboring entries:

```markdown
- **`arXiv 2026`** Paper Title. [[paper](https://arxiv.org/abs/XXXX.XXXXX)] [[code](https://github.com/OWNER/REPO)]
```

Guidelines:

1. Use the accepted venue and year when they are established. Otherwise use arXiv and the submission year.
2. Link to the canonical abstract or publication page rather than a PDF when possible.
3. Add a code link only when the repository is publicly accessible and clearly associated with the work.
4. Preserve the official paper title and check author supplied metadata before changing it.
5. Omit unavailable links rather than adding placeholders.
6. Keep entries focused on research artifacts and avoid promotional descriptions.

## Pull Request Checklist

Before opening a pull request, please confirm that:

- [ ] The work fits the scope and is placed under its primary contribution.
- [ ] The title, year, venue, and links have been checked against an official source.
- [ ] The entry follows the existing format and is not duplicated elsewhere.
- [ ] Any claim about correctness, quality retention, or acceleration matches the conditions in the paper.
- [ ] New sections preserve the chapter alignment of the collection or have been discussed in an issue.
- [ ] The Markdown renders correctly and all modified links work.

## License

By contributing, you agree that your contributions are provided under the [Apache License 2.0](LICENSE).
