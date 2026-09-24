# Process Flow (full decision graph)

```dot
digraph brainstorming {
    "Explore project context" [shape=box];
    "Establish actor coverage" [shape=box];
    "Material open decision + capability available?" [shape=diamond];
    "Co-design active (state)" [shape=box];
    "Ask clarifying questions" [shape=box];
    "Propose 2-3 approaches" [shape=box];
    "Present candidate design sections" [shape=box];
    "Candidate design coherent?" [shape=diamond];
    "Constructive + adversarial review" [shape=box];
    "Synthesize against\nevidence + user intent" [shape=box];
    "Material issue remains?" [shape=diamond];
    "Targeted second review" [shape=box];
    "Targeted synthesis" [shape=box];
    "Material issue still remains?" [shape=diamond];
    "Resolve with user" [shape=box];
    "User gives final approval?" [shape=diamond];
    "Revise with user" [shape=box];
    "Materially different candidate?" [shape=diamond];
    "Write design doc" [shape=box];
    "Spec self-review\n(fix inline)" [shape=box];
    "Independent spec review" [shape=box];
    "Synthesize canonical spec" [shape=box];
    "Spec Critical finding remains?" [shape=diamond];
    "Targeted spec re-check" [shape=box];
    "Resolve spec with user" [shape=box];
    "Commit canonical spec" [shape=box];
    "User reviews spec?" [shape=diamond];
    "Invoke writing-plans skill" [shape=doublecircle];

    "Explore project context" -> "Establish actor coverage";
    "Establish actor coverage" -> "Material open decision + capability available?";
    "Material open decision + capability available?" -> "Co-design active (state)" [label="yes"];
    "Material open decision + capability available?" -> "Ask clarifying questions" [label="no, skip"];
    "Co-design active (state)" -> "Ask clarifying questions";
    "Ask clarifying questions" -> "Propose 2-3 approaches" [label="answers"];
    "Propose 2-3 approaches" -> "Present candidate design sections";
    "Present candidate design sections" -> "Candidate design coherent?";
    "Candidate design coherent?" -> "Present candidate design sections" [label="no, revise"];
    "Candidate design coherent?" -> "Constructive + adversarial review" [label="yes"];
    "Constructive + adversarial review" -> "Synthesize against\nevidence + user intent";
    "Synthesize against\nevidence + user intent" -> "Material issue remains?";
    "Material issue remains?" -> "Targeted second review" [label="yes"];
    "Targeted second review" -> "Targeted synthesis";
    "Targeted synthesis" -> "Material issue still remains?";
    "Material issue still remains?" -> "Resolve with user" [label="yes"];
    "Resolve with user" -> "User gives final approval?";
    "Material issue still remains?" -> "User gives final approval?" [label="no"];
    "Material issue remains?" -> "User gives final approval?" [label="no"];
    "User gives final approval?" -> "Revise with user" [label="no"];
    "Revise with user" -> "Materially different candidate?";
    "Materially different candidate?" -> "Constructive + adversarial review" [label="yes, new bounded gate"];
    "Materially different candidate?" -> "User gives final approval?" [label="no, minor revision"];
    "User gives final approval?" -> "Write design doc" [label="yes"];
    "Write design doc" -> "Spec self-review\n(fix inline)";
    "Spec self-review\n(fix inline)" -> "Independent spec review";
    "Independent spec review" -> "Synthesize canonical spec";
    "Synthesize canonical spec" -> "Spec Critical finding remains?";
    "Spec Critical finding remains?" -> "Targeted spec re-check" [label="yes, once"];
    "Targeted spec re-check" -> "Resolve spec with user" [label="still unresolved"];
    "Targeted spec re-check" -> "Commit canonical spec" [label="resolved"];
    "Resolve spec with user" -> "Commit canonical spec";
    "Spec Critical finding remains?" -> "Commit canonical spec" [label="no"];
    "Commit canonical spec" -> "User reviews spec?";
    "User reviews spec?" -> "Write design doc" [label="changes requested"];
    "User reviews spec?" -> "Invoke writing-plans skill" [label="approved"];
}
```

**"Co-design active" is a state, not a step.** It does not do work before discovery. When it holds: the *Ask clarifying questions* step runs blind SA + independent question generation, then merge/filter/present, BEFORE the user answers; then, after the user's answers, the *Propose 2-3 approaches* step runs blind SA + independent approach generation, then synthesis. Order is always discovery questions → user answers → approach generation, co-design on or off (see `independent-co-design.md`).

**The terminal state is invoking writing-plans.** Do NOT invoke frontend-design, mcp-builder, or any other implementation skill. The ONLY skill you invoke after brainstorming is writing-plans.
