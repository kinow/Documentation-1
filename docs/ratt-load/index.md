---
title: "RATT"
path: "/docs/ratt-load"
---

**RATT can only be used in combination with [TriplyDB](https://triply.cc/triplydb). Contact [info@triply.cc](mailto:info@triply.cc) for more information, or to check if you are allowed to use it.**

RATT is a [TypeScript package](https://www.npmjs.com/package/@triply/ratt) that is developed by [Triply](https://triply.cc/).  RATT makes it possible to develop and maintain production-grade linked data pipelines. It is used in combination with one of the [TriplyDB subscriptions](https://triply.cc/subscriptions) to create large-scale knowledge graphs.


## Validating RDF output

RATT is able to automatically validate the RDF that is generated in the pipeline against a SHACL information model.

```ts
app.use(
  // Create all linked data statements.
  …
  // Now that all the data is created, validate it using a model.
  mw.validateShacl(app.sources.model)
)
```


### Validation report

Validation creates a report that is asserted in linked data.  This report can be stored as a named graph in the created linked dataset.

The following example code stores the validation report in a dedicated named graph:

```ts
const prefix = {
  graph: 'https://triplydb.com/Triply/example/graph/',
}

const graph = {
  report: prefix.graph('report'),
}

app.use(
  // Create all linked data statements.
  …
  // Now that all the data is created, validate it using a model.
  mw.validateShacl(
    app.sources.model,
    {report: {destination: app.sources.dataset,
              graph: graph.report}}),
)
```


### Termination conditions

The `validateShacl` function can optionally be given the `terminateOn` option.  This option determines when validation halts.  It can take the following values:

<dl>
  <dt><code>false</code></dt>
  <dd>Do not halt; run the validation for the full dataset.</dd>
  <dt><code>'Violation'</code></dt>
  <dd>Halt validation when the first SHACL Violation is encountered.</dd>
  <dt><code>'Warning'</code></dt>
  <dd>Halt validation when the first SHACL Violation or SHACL Warning is encountered.</dd>
  <dt><code>'Info'</code></dt>
  <dd>Halt validation when the first SHACL Violation or SHACL Warning or SHACL Informational message is encountered.</dd>
</dl>

The following example code lets validation run for the full dataset, regardless of how many violations, warnings, and/or information messages are encountered:

```ts
app.use(
  // Create all linked data statements.
  …
  // Now that all the data is created, validate it using a model.
  mw.validateShacl(app.sources.model, {terminateOn: false}
)
```