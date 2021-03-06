---
layout: default
title:  Getting Started with GoCD - Part 3
title_tag: Getting Started | GoCD
meta_tag_description: New to GoCD? Start here.
meta_tag_keywords: getting started, documentation, gocd, continuous delivery, go
page_name: getting-started page3
extra_headers: "<link rel='stylesheet' href='/css/getting-started.css'>"
---

In <a href="https://go.cd/getting-started/part-1.html">Part 1</a> of this guide, the concepts of GoCD Server, GoCD Agents,
pipelines, materials, stages, jobs and tasks were introduced. You were also able to get a simple pipeline with a single
stage, job and task each working.


In <a href="/getting-started/part-2.html">Part 2</a>, you learnt about using pipelines as materials
(dependency materials), artifacts and custom tabs.

Let us continue, in this part, by learning about the Value Stream Map (VSM) and about fan-in and fan-out.

##### Table of contents:

- [Value Stream Map (VSM)](#value_stream_map)
  - [Concept 9: Value Stream Map](#concept_vsm)
- [Fan-Out and Fan-In](#fan_out_fan_in)
   - [Concept 10: Fan-Out and Fan-In](#concept_fan_out_fan_in)
- [Previous part of guide (different page): Part 1](/getting-started/part-1.html)
- [Previous part of guide (different page): Part 2](/getting-started/part-2.html)

<h2 class="small_margins"><a name="value_stream_map"></a>Value Stream Map (VSM)</h2>

<div class="concept">
  <h3><a name="concept_vsm"></a>Concept 9: Value Stream Map (VSM)</h3>

  <p>
    The Value Stream Map is the full end-to-end (from commits to production) view across pipelines, and maps to the
    concept of a "Continuous Delivery Pipeline" or "Deployment Pipeline", in the book, "Continuous Delivery" by Jez
    Humble and David Farley. In their book, a couple of different views of a Deployment Pipeline are presented:
  </p>

  <figure class="concept">
    <img src="/images/getting-started/part-3/image01.png">
    <figcaption>
      The idea is that, as you go closer to production, the confidence of the build's production readiness
      increases. (Image &copy; Humble, Farley)
    </figcaption>
  </figure>

  <figure class="concept">
    <img src="/images/getting-started/part-3/image02.png">
    <figcaption>
      This depicts the interactions between source code repositories, artifact repositories and different
      stages of the Deployment Pipeline. (Image &copy; Humble, Farley)
    </figcaption>
  </figure>

  <p>
    A corresponding representation of a Value Stream Map in GoCD looks like this:
  </p>

  <figure class="concept">
    <img src="/images/getting-started/part-3/image03.png">
  </figure>

  <p>
    There are some unfortunate differences in naming between GoCD and the book. What is termed as a "Stage of a Deployment
    Pipeline" in the book is called a "Pipeline" in GoCD. The "Deployment Pipeline" (end-to-end) is called the "Value
    Stream Map". In the rest of this guide, the term "Pipeline" will refer to a GoCD Pipeline and the term "Stage" will
    refer to a "Stage of a GoCD pipeline".
  </p>

  <p>
    Interestingly, Jez Humble was the first product manager of GoCD, and was around while these naming decisions were
    made. Rumour has it that he regrets the choice of the overloaded term, "Pipeline". :)
  </p>

  <p>
    The previously defined concepts of a Pipeline, Artifact and Pipeline Dependency (chaining of pipelines) come
    together to enable a true end-to-end Deployment Pipeline, which is known as a Value Stream Map (VSM) in GoCD. . The
    ability to do this, and so this concept is important because:
  </p>

  <ol start="1">
    <li>
      <strong>Abstraction</strong>: It is a level of abstraction in modeling, and allows you to hide (by default) what
      is going on underneath and not have to know the details unless you need to. It helps talk about and reason about
      your end-to-end workflow at a different level.
    </li>
    <li>
      <strong>Build once</strong>: This allows artifacts to be propagated through the pipelines. As mentioned in the
      Artifact concept definition, this enables an important CD concept, "Only Build Your Binaries Once".
    </li>
    <li>
      <strong>Parallelization</strong>: This provides another approach to parallelize your workflow. In the example
      above, the User Acceptance Pipeline and the Capacity Testing Pipeline can run simultaneously, providing quicker
      feedback. This will be discussed later in this guide, in the Fan-In/Fan-Out section.
    </li>
    <li>
      <strong>Traceability</strong>: This provides true auditability and traceability, all the way from a commit to
      production. You know exactly what went into production.
    </li>
  </ol>
</div>

To use a Value Stream Map in GoCD, you don't really need to do anything. If you've pipelines which use pipeline
dependencies, then you can always get a VSM at the click of a button. Here is a dashboard view of pipelines which map to
the example shown in the concept. Clicking on the label (marked in red boxes) of any of the pipelines shows the VSM from
the perspective of that pipeline:

<figure class="screenshot">
  <img src="/images/getting-started/part-3/image04.png">
  <figcaption>The labels of pipelines: gateways to VSM</figcaption>
</figure>

Clicking on the label of the "Production" pipeline, takes you to its Value Stream Map, in which all of its upstream
pipelines and their source control materials are shown.

<figure class="screenshot">
  <img src="/images/getting-started/part-3/image05.png">
  <figcaption>VSM from the view of the "Production" pipeline</figcaption>
</figure>

Clicking on the label of the "Build" pipeline, either from the dashboard or the VSM view above, takes you to a similar
view with the perspective of the "Build" pipeline instance you clicked on. It is shown below. You can see that the
source material of "CapacityTesting" pipeline is not shown, because it is not directly downstream of the "Build"
pipeline.


<figure class="screenshot">
  <img src="/images/getting-started/part-3/image06.png">
  <figcaption>VSM from the view of the "Build" pipeline</figcaption>
</figure>

Now, that we know about the VSM, let's delve a little deeper into powerful concepts called fan-in and fan-out, which the
VSM visualizes.


<h2 class="small_margins"><a name="fan_out_fan_in"></a>Fan-Out and Fan-In</h2>

Let's start with the basic concepts and then use an example to understand this further.

<div class="concept">
  <h3><a name="concept_fan_out_fan_in"></a>Concept 10: Fan-Out and Fan-In</h3>

  <h4>Fan-Out</h4>

  A pipeline "X" can be said to fan-out to downstream pipelines, when there are multiple downstream pipelines for which pipeline "X" is a dependency. It looks like this:

  <figure class="concept">
    <img src="/images/getting-started/part-3/image07.png">
  </figure>

  Similarly, a source material can be said to fan-out to downstream pipelines:

  <figure class="concept">
    <img src="/images/getting-started/part-3/image08.png">
  </figure>

  This brings in the advantages of parallelization and quicker feedback, which is a key goal of Continuous Delivery. As
  long as there are enough agents available, all three downstream dependencies will trigger together and can run
  together.

  <h4>Fan-In</h4>

  Opposite of fan-out. When multiple dependencies are used by a single pipeline, then the dependencies are said to fan-in to the pipeline. It looks like this:

  <figure class="concept">
    <img src="/images/getting-started/part-3/image03.png">
  </figure>

  In this example, there is a fan-in at the Production pipeline, from its two upstream pipelines and the git
  material. Similarly, there is a fan-in at the CapacityTesting pipeline. The important aspect of fan-in is not that
  there are multiple dependencies upstream, but the way that the pipeline is triggered, by ensuring that all of its
  dependencies are consistent in version, when the pipeline runs. This ensures correctness of the build, and quicker
  feedback through elimination of spurious builds.
</div>

Let's bring back an earlier VSM view, from the Production pipeline perspective, with the links leading into the
Production pipeline emphasized a bit, by pinning it:

<figure class="screenshot">
  <img src="/images/getting-started/part-3/image10.png">
  <figcaption>VSM with links pinned - showing fan-in</figcaption>
</figure>

In this setup, the Build pipeline is said to fan-out to the UserAcceptance and CapacityTesting pipelines, and the git
material, UserAcceptance pipeline, and CapacityTesting pipeline are said to fan-in to the Production pipeline. From a
terminology perspective, that might make sense. But what does it really mean? Why is it important?

To understand that, let's consider what should happen when a commit (say C1) is made to the git material shown. Even
though the Production pipeline has a direct dependency on the git material, it should not be triggered
automatically. The correct order of triggering of pipelines is:

<ol>
  <li>Build pipeline - with commit C1 (let's say the pipeline label of this is "Build_1").</li>
  <li>Once it succeeds, both UserAcceptance and CapacityTesting pipelines with "Build_1" as their dependency (say "UA_1" and "CT_1").</li>
  <li>Once they succeed, Production pipeline with git commit C1, pipeline dependency UA_1, and pipeline dependency CT_1.</li>
</ol>

This ordering and consistency of versioning is what GoCD with fan-out and fan-in will ensure. There are two main reasons for this to be considered very important:

<ol>
  <li>
    <p>
      <strong>Correctness</strong>: Without fan-in, if the CapacityTesting pipeline was slow and UserAcceptance pipeline
      was fast, then the Production pipeline could have been triggered with an old version of CapacityTesting pipeline
      and a new version of UserAcceptance pipeline. Imagine deploying to Production without really knowing that all
      upstream dependent pipelines have not finished successfully!
    </p>

    <p>
      A very important part of fan-out and fan-in is that a downstream pipeline will <em>always</em> figure out the
      correct upstream dependency versions, including repository materials, unless it is manually forced by a user not
      to.
    </p>

    <p>
      Similarly, any artifacts fetched from any upstream pipeline, however many levels up, is guaranteed to be consistent
      and correct. This means that the right revision of those artifacts will be fetched, irrespective of any other runs
      of that pipeline that may have run after.
    </p>
  </li>

  <li>
    <strong>Quicker feedback</strong>: By eliminating spurious builds which should never have been built (using wrong
    dependencies), fan-in provides quicker feedback. Similarly, fan-out allows multiple independent downstream pipelines
    to run simultaneously, providing quicker feedback.
  </li>
</ol>

This has been a bit of a whirlwind tour of some of GoCD's concepts and ideas. To learn more about all the other features
and how to use them, head on over to the [GoCD documentation](https://go.cd/documentation/user/current/) page.
