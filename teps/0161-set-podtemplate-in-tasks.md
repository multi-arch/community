---
status: proposed
title: Set podTemplate in Tasks
creation-date: '2025-03-19'
last-updated: '2025-03-19'
authors:
- '@dorzel'
- '@jeffdyoung'
collaborators: []
---

# TEP-0161: Set podTemplate in Tasks

<!--
**Note:** Please remove comment blocks for sections you've filled in.
When your TEP is complete, all of these comment blocks should be removed.

To get started with this template:

- [ ] **Fill out this file as best you can.**
  At minimum, you should fill in the "Summary", and "Motivation" sections.
  These should be easy if you've preflighted the idea of the TEP with the
  appropriate Working Group.
- [ ] **Create a PR for this TEP.**
  Assign it to people in the Working Group that are sponsoring this process.
- [ ] **Merge early and iterate.**
  Avoid getting hung up on specific details and instead aim to get the goals of
  the TEP clarified and merged quickly. The best way to do this is to just
  start with the high-level sections and fill out details incrementally in
  subsequent PRs.

Just because a TEP is merged does not mean it is complete or approved. Any TEP
marked as a `proposed` is a working document and subject to change. You can
denote sections that are under active debate as follows:

```
<<[UNRESOLVED optional short context or usernames ]>>
Stuff that is being argued.
<<[/UNRESOLVED]>>
```

When editing TEPS, aim for tightly-scoped, single-topic PRs to keep discussions
focused. If you disagree with what is already in a document, open a new PR
with suggested changes.

If there are new details that belong in the TEP, edit the TEP. Once a
feature has become "implemented", major changes should get new TEPs.

The canonical place for the latest set of instructions (and the likely source
of this file) is [here](/teps/tools/tep-template.md.template).

-->

<!--
This is the title of your TEP. Keep it short, simple, and descriptive. A good
title can help communicate what the TEP is and should be considered as part of
any review.
-->

<!--
A table of contents is helpful for quickly jumping to sections of a TEP and for
highlighting any additional information provided beyond the standard TEP
template.

Ensure the TOC is wrapped with
  <code>&lt;!-- toc --&rt;&lt;!-- /toc --&rt;</code>
tags, and then generate with `hack/update-toc.sh`.
-->

<!-- toc -->
- [TEP-0161: Set podTemplate in Tasks](#tep-0161-set-podtemplate-in-tasks)
  - [Summary](#summary)
  - [Motivation](#motivation)
    - [Goals](#goals)
    - [Non-Goals](#non-goals)
    - [Use Cases](#use-cases)
    - [Requirements](#requirements)
  - [Proposal](#proposal)
    - [Notes and Caveats](#notes-and-caveats)
  - [Design Details](#design-details)
  - [Design Evaluation](#design-evaluation)
    - [Reusability](#reusability)
    - [Simplicity](#simplicity)
    - [Flexibility](#flexibility)
    - [Conformance](#conformance)
    - [User Experience](#user-experience)
    - [Performance](#performance)
    - [Risks and Mitigations](#risks-and-mitigations)
    - [Drawbacks](#drawbacks)
  - [Alternatives](#alternatives)
  - [Implementation Plan](#implementation-plan)
    - [Test Plan](#test-plan)
    - [Infrastructure Needed](#infrastructure-needed)
    - [Upgrade and Migration Strategy](#upgrade-and-migration-strategy)
    - [Implementation Pull Requests](#implementation-pull-requests)
  - [References](#references)
<!-- /toc -->

## Summary

<!--
This section is incredibly important for producing high quality user-focused
documentation such as release notes or a development roadmap. It should be
possible to collect this information before implementation begins in order to
avoid requiring implementors to split their attention between writing release
notes and implementing the feature itself.

A good summary is probably at least a paragraph in length.

Both in this section and below, follow the guidelines of the [documentation
style guide]. In particular, wrap lines to a reasonable length, to make it
easier for reviewers to cite specific portions, and to minimize diff churn on
updates.

[documentation style guide]: https://github.com/kubernetes/community/blob/master/contributors/guide/style-guide.md
-->

This TEP proposes a feature to enable podTemplate to be set at the Task level and be parameterized via Matrix in a Pipeline. 
Tekton does not currently provide a way to set podTemplate anywhere but on TaskRuns (and via taskRunSpecs in PipelineRuns).

See #6742 for an existing discussion. 

## Motivation

<!--
This section is for explicitly listing the motivation, goals and non-goals of
this TEP. Describe why the change is important and the benefits to users. The
motivation section can optionally provide links to [experience reports][experience reports]
to demonstrate the interest in a TEP within the wider Tekton community.

[experience reports]: https://github.com/golang/go/wiki/ExperienceReports
-->

The motivation here is to more easily support building multi-arch images on clusters with nodes of multiple architectures.

Task/Pipeline authors currently do not have the flexibility to "fan out" tasks to nodes of a specified arch and must duplicate TaskRuns to achieve the desired effect. For example, making a duplicate Task for each arch and specifying podTemplate via taskRunSpecs for each one. 


### Goals

<!--
List the specific goals of the TEP.
- What is it trying to achieve?
- How will we know that this has succeeded?
-->

- Enable builds to be easily scheduled on nodes of multiple architectures
- Simplify specifying podTemplate for Tasks/TaskRuns
- Parametrize podTemplate with Matrix

### Non-Goals

<!--
Listing non-goals helps to focus discussion and make progress.
- What is out of scope for this TEP?
-->

### Use Cases

<!--
Describe the concrete improvement specific groups of users will see if the
Motivations in this doc result in a fix or feature.

Consider the user's:
- [role][role] - are they a Task author? Catalog Task user? Cluster Admin? e.t.c.
- experience - what workflows or actions are enhanced if this problem is solved?

[role]: https://github.com/tektoncd/community/blob/main/user-profiles.md
-->

- Authors of Tasks and Pipelines can parameterize podTemplate via Matrix

- Authors of Tasks and Pipelines can use this feature to easily schedule builds on nodes of multiple architectures, and 
create the resulting manifest list (/ image index: https://github.com/opencontainers/image-spec/blob/main/image-index.md)

### Requirements

<!--
Describe constraints on the solution that must be met, such as:
- which performance characteristics that must be met?
- which specific edge cases that must be handled?
- which user scenarios that will be affected and must be accommodated?
-->

## Proposal

<!--
This is where we get down to the specifics of what the proposal actually is.
This should have enough detail that reviewers can understand exactly what
you're proposing, but should not include things like API designs or
implementation. The "Design Details" section below is for the real
nitty-gritty.
-->

### Notes and Caveats

<!--
(optional)

Go in to as much detail as necessary here.
- What are the caveats to the proposal?
- What are some important details that didn't come across above?
- What are the core concepts and how do they relate?
-->


## Design Details

<!--
This section should contain enough information that the specifics of your
change are understandable. This may include API specs (though not always
required) or even code snippets. If there's any ambiguity about HOW your
proposal will be implemented, this is the place to discuss them.

If it's helpful to include workflow diagrams or any other related images,
add them under "/teps/images/". It's upto the TEP author to choose the name
of the file, but general guidance is to include at least TEP number in the
file name, for example, "/teps/images/NNNN-workflow.jpg".
-->

Add a podTemplate field to the Task spec (as is already done with TaskRuns), and implement param substitution on this
field to enable Matrix to fan out params to the podTemplate object. This can be used in a pipeline as such:

```
kind: PipelineRun
metadata:
  generateName: matrixed-pipelinerun-
spec:
  pipelineSpec:
    tasks:
      - name: build-and-push-manifest
        matrix:
          params:
          - name: arch
            value: 
              - "amd64"
              - "arm64"
        taskSpec:
          results:
            - name: manifest
              type: string
          params:
            - name: arch
          podTemplate:
            nodeSelector:
              kubernetes.io/arch: $(params.arch)
          steps:
            - name: build-and-push
              image: ubuntu
              script: |
                echo "building on $(params.arch)"
                echo "testmanifest-$(params.arch)" | tee $(results.manifest.path)
      - name: create-manifest-list
        params:
          - name: manifest
            value: $(tasks.build-and-push-manifest.results.manifest[*])
        taskSpec:
          steps:
            - name: echo-manifests
              image: ubuntu
              args: ["$(params.manifest[*])"]
              script: echo "$@"
```

When a TaskRun specifies fields in the podTemplate, those fields override those specified in the related Task. 

## Design Evaluation
<!--
How does this proposal affect the api conventions, reusability, simplicity, flexibility
and conformance of Tekton, as described in [design principles](https://github.com/tektoncd/community/blob/master/design-principles.md)
-->

This largely slots into Tekton's existing design, with changes being somewhat minimal. 
This enhances Tekton's flexibility by adding the ability to modify podTemplate fields with Matrix params.

### Reusability

<!--
https://github.com/tektoncd/community/blob/main/design-principles.md#reusability

- Are there existing features related to the proposed features? Were the existing features reused?
- Is the problem being solved an authoring-time or runtime-concern? Is the proposed feature at the appropriate level
authoring or runtime?
-->

A related feature is the ability to set podTemplate in Pipeline.TaskRunSpecs and on TaskRuns themselves. 

### Simplicity

<!--
https://github.com/tektoncd/community/blob/main/design-principles.md#simplicity

- How does this proposal affect the user experience?
- What’s the current user experience without the feature and how challenging is it?
- What will be the user experience with the feature? How would it have changed?
- Does this proposal contain the bare minimum change needed to solve for the use cases?
- Are there any implicit behaviors in the proposal? Would users expect these implicit behaviors or would they be
surprising? Are there security implications for these implicit behaviors?
-->

This should overall simplify the user experience of running Tasks on nodes of multiple architectures.

Current user experience: create multiple Tasks/TaskRuns for each architecture and specify podTemplate in Pipeline.TaskRunSpecs. Or otherwise duplicate TaskRuns in a different configuration.

User experience after this change: have full flexibility with Matrix + params on all fields of the podTemplate. Create a 
single task instead of multiple for each arch. Gather results easily from multiple Tasks. 

### Flexibility

<!--
https://github.com/tektoncd/community/blob/main/design-principles.md#flexibility

- Are there dependencies that need to be pulled in for this proposal to work? What support or maintenance would be
required for these dependencies?
- Are we coupling two or more Tekton projects in this proposal (e.g. coupling Pipelines to Chains)?
- Are we coupling Tekton and other projects (e.g. Knative, Sigstore) in this proposal?
- What is the impact of the coupling to operators e.g. maintenance & end-to-end testing?
- Are there opinionated choices being made in this proposal? If so, are they necessary and can users extend it with
their own choices?
-->

### Conformance

<!--
https://github.com/tektoncd/community/blob/main/design-principles.md#conformance

- Does this proposal require the user to understand how the Tekton API is implemented?
- Does this proposal introduce additional Kubernetes concepts into the API? If so, is this necessary?
- If the API is changing as a result of this proposal, what updates are needed to the
[API spec](https://github.com/tektoncd/pipeline/blob/main/docs/api-spec.md)?
-->

Task.spec (TaskSpec) API will change to include podTemplate. 

### User Experience

<!--
(optional)

Consideration about the user experience. Depending on the area of change,
users may be Task and Pipeline editors, they may trigger TaskRuns and
PipelineRuns or they may be responsible for monitoring the execution of runs,
via CLI, dashboard or a monitoring system.

Consider including folks that also work on CLI and dashboard.
-->

### Performance

<!--
(optional)

Consider which use cases are impacted by this change and what are their
performance requirements.
- What impact does this change have on the start-up time and execution time
of TaskRuns and PipelineRuns?
- What impact does it have on the resource footprint of Tekton controllers
as well as TaskRuns and PipelineRuns?
-->

### Risks and Mitigations

<!--
What are the risks of this proposal and how do we mitigate? Think broadly.
For example, consider both security and how this will impact the larger
Tekton ecosystem. Consider including folks that also work outside the WGs
or subproject.
- How will security be reviewed and by whom?
- How will UX be reviewed and by whom?
-->

### Drawbacks

<!--
Why should this TEP _not_ be implemented?
-->

## Alternatives

<!--
What other approaches did you consider and why did you rule them out? These do
not need to be as detailed as the proposal, but should include enough
information to express the idea and why it was not acceptable.
-->

Possibly adding more functionality to Pipeline.TaskRunSpecs (like growing matrix functionality). This was considered
more complex than adding podTemplate to Tasks.

## Implementation Plan

<!--
What are the implementation phases or milestones? Taking an incremental approach
makes it easier to review and merge the implementation pull request.
-->


### Test Plan

<!--
Consider the following in developing a test plan for this enhancement:
- Will there be e2e and integration tests, in addition to unit tests?
- How will it be tested in isolation vs with other components?

No need to outline all the test cases, just the general strategy. Anything
that would count as tricky in the implementation and anything particularly
challenging to test should be called out.

All code is expected to have adequate tests (eventually with coverage
expectations).
-->

Unit, integration, and e2e tests. Something that may require some tweaking is simulating multi-arch nodes but this is
a big unknown at the moment. 

### Infrastructure Needed

<!--
(optional)

Use this section if you need things from the project or working group.
Examples include a new subproject, repos requested, GitHub details.
Listing these here allows a working group to get the process for these
resources started right away.
-->

### Upgrade and Migration Strategy

<!--
(optional)

Use this section to detail whether this feature needs an upgrade or
migration strategy. This is especially useful when we modify a
behavior or add a feature that may replace and deprecate a current one.
-->

### Implementation Pull Requests

<!--
Once the TEP is ready to be marked as implemented, list down all the GitHub
merged pull requests.

Note: This section is exclusively for merged pull requests for this TEP.
It will be a quick reference for those looking for implementation of this TEP.
-->

## References

<!--
(optional)

Use this section to add links to GitHub issues, other TEPs, design docs in Tekton
shared drive, examples, etc. This is useful to refer back to any other related links
to get more details.
-->

https://github.com/tektoncd/pipeline/issues/6742
https://github.com/tektoncd/pipeline/pull/8599 (WIP PR, includes an example Pipeline)
