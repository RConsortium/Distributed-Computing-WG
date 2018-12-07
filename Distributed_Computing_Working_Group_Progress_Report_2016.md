## Introduction

Data sizes continue to increase, while single core performance has
stagnated. We scale computations by leveraging multiple cores and
machines. Large datasets are expensive to replicate, so we minimize data
movement by moving the computation to the data. Many systems, such as
Hadoop, Spark, and massively parallel processing (MPP) databases, have
emerged to support these strategies, and each exposes its own unique
interface, with little standardization.

Developing and executing an algorithm in the distributed context is a
complex task that requires specific knowledge of and dependency on the
system storing the data. It is also a task orthogonal to the primary
role of a data scientist or statistician: extracting knowledge from
data. The task thus falls to the data analysis environment, which should
mask the complexity behind a familiar interface, maintaining user
productivity. However, it is not always feasible to automatically
determine the optimal strategy for a given problem, so user input is
often beneficial. The environment should only abstract the details to
the extent deemed appropriate by the user.

R needs a standardized, layered and idiomatic abstraction for computing
on distributed data structures. R has many packages that provide
parallelism constructs as well as bridges to distributed systems such as
Hadoop. Unfortunately, each interface has its own syntax, parallelism
techniques, and supported platform(s). As a consequence, contributors
are forced to learn multiple idiosyncratic interfaces, and to restrict
each implementation to a particular interface, thus limiting the
applicability and adoption of their software and hampering
interoperability.

The idea of a unified interface stemmed from a cross-industry workshop
organized at HP Labs in early 2015. The workshop was attended by
different companies, universities, and R-core members. Immediately after
the workshop, Indrajit Roy, Edward Ma, and Michael Lawrence began
designing an abstraction that later became known as the CRAN package ddR
(Distributed Data in R)\[1\]. It declares a unified API for distributed
computing in R and ensures that R programs written using the API are
portable across different systems, such as Distributed R, Spark, etc.

The ddR package has completed its initial phase of development; the
first release is now on CRAN. Three ddR machine-learning algorithms are
also on CRAN, randomForest.ddR, glm.ddR, and kmeans.ddR. Two reference
backends for ddR have been completed, one for R’s parallel package, and
one for HP Distributed R. Example code and scripts to run algorithms and
code on both of these backends are available in our public repository at
<https://github.com/vertica/ddR>.

The overarching goal of the ddR project was for it to be a starting
point in a collaborative effort, ultimately leading to a standard API
for working with distributed data in R. We decided that it was natural
for the R Consortium to sponsor the collaboration, as it should involve
both industry and R-core members. To this end, we established the R
Consortium Working Group on Distributed Computing, with a planned
duration of a single year and the following aims:

1.  Agree on the goal of the group, i.e., we should have a unifying
    framework for distributed computing. Define success metric.
2.  Brainstorm on what primitives should be included in the API. We can
    use ddR’s API of distributed data-structures and dmapply as the
    starting proposal. Understand relationship with existing packages
    such as parallel, foreach, etc.
3.  Explore how ddR like interface will interact with databases. Are
    there connections or redundancies with dplyr and multiplyr?
4.  Decide on a reference implementation for the API.
5.  Decide on whether we should also implement a few ecosystem packages,
    e.g., distributed algorithms written using the API.

We declared the following milestones:

1.  Mid-year milestone: Finalize API. Decide who all will help with
    developing the top-level implementation and backends.
2.  End-year milestone: Summary report and reference implementation.
    Socialize the final package.

This report outlines the progress we have made on the above goals and
milestones, and how we plan to continue progress in the second half of
the working group term.

## Results and Current Status

The working group has achieved the first goal by agreeing that we should
aim for a unifying distributed computing abstraction, and we have
treated ddR as an informal API proposal.

We have discussed many of the issues related to the second goal,
deciding which primitives should be part of the API. We aim for the API
to support three shapes of data --- lists, arrays and data frames ---
and to enable the loading and basic manipulation of distributed data,
including multiple modes of functional iteration (e.g., apply()
operations). We aim to preserve consistency with base R data structures
and functions, so as to provide a simple path for users to port
computations to distributed systems.

The ddR constructs permit a user to express a wide variety of
applications, including machine-learning algorithms, that will run on
different backends. We have successfully implemented distributed
versions of algorithms such as K-means, Regression, Random Forest, and
PageRank using the ddR API. Some of these ddR algorithms are now
available on CRAN. In addition, the package provides several generic
definitions of common operators (such as colSums) that can be invoked on
distributed objects residing in the supporting backends.

Each custom ddR backend is encapsulated in its own driver package. In
the conventional style of functional OOP, the driver registers methods
for generics declared by the backend API, such that ddR can dispatch the
backend-specific instructions by only calling the generics.

The working group explored potential new backends with the aim of
broadening the applicability of the ddR interface. We hosted
presentations from external speakers on Spark and TensorFlow, and also
considered a generic SQL backend. The discussion focused on Spark
integration, and the R Consortium-funded intern Clark Fitzgerald took on
the task of developing a prototype Spark backend. The development of the
Spark backend encountered some obstacles, including the immaturity of
Spark and its R interfaces. Development is currently paused, as we await
additional funding.

During the monthly meetings, the working group deliberated on different
design improvements for ddR itself. We list two key topics that were
discussed. First, Michael Kane and Bryan Lewis argued for a lower level
API that directly operates on chunks of data. While ddR supports
chunk-wise data processing, via a combination of dmapply() and parts(),
its focus on distributed data structures means that the chunk-based
processing is exposed as the manipulation of these data structures.
Second, Clark Fitzgerald proposed restructuring the ddR code into two
layers that includes chunk-wise processing while retaining the emphasis
on distributed data structures\[2\]. The lower level API, which will
interface with backends, will use a Map() like primitive to evaluate
functions on chunks of data, while the higher level ddR API will expose
distributed data structures, dmapply, and other convenience functions.
This refactoring would facilitate the implementation of additional
backends.

## Discussion and Future Plans

The R Consortium-funded working group and internship has helped us start
a conversation on distributed computing APIs for R. The ddR CRAN package
is a concrete outcome of this working group, and serves as a platform
for exploring APIs and their integration with different backends. While
ddR is still maturing, we have arrived at a consensus for how we should
improve and finalize the ddR API.

As part of our goal for a reference implementation, we aim to develop
one or more prototype backends that will make the ddR interface useful
in practice. A good candidate backend is any open-source system that is
effective at R use cases and has strong community support. Spark remains
a viable candidate, and we also aim to further explore TensorFlow.

We plan for a second intern to perform three tasks: (1) refactor the ddR
API to a more final form, (2) compare Spark and TensorFlow in detail,
with an eye towards the feasibility of implementing a useful backend,
and (3) implement a prototype backend based on Spark or TensorFlow,
depending on the recommendation of the working group.

By the conclusion of the working group, it will have produced:

  - A stable version of the ddR package and at least one practical
    backend, released on CRAN,
  - A list of requirements that are relevant and of interest to the
    community but have not yet been met by ddR, including alternative
    implementations that remain independent,
  - A list of topics that the group believes worthy of further
    investigation.

\[1\]
<http://h30507.www3.hp.com/t5/Behind-the-scenes-Labs/Enhancing-R-for-Distributed-Computing/ba-p/6795535#.VjE1K7erQQj>

\[2\] Clark Fitzgerald. <https://github.com/vertica/ddR/wiki/Design>