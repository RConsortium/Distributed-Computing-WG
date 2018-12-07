## Goals and Purpose

The Distributed Computing Working Group will endorse the design of a
common abstraction for distributed data structures in R. We aim to have
at least one open-source implementation, as well as a SQL
implementation, released within a year of forming the group.

## Members

  - **Michael Lawrence** (Genentech)
  - **Indrajit Roy** (Google)
  - *Joe Rickert* (ISC liason, RStudio)
  - Bernd Bischl (LMU)
  - Matt Dowle (H2O)
  - Mario Inchiosa (Microsoft)
  - Michael Kane (Yale)
  - Javier Luraschi (RStudio)
  - Edward Ma (HP Enterprise)
  - Luke Tierney (University of Iowa)
  - Simon Urbanek (AT\&T)
  - Bryan Lewis (Paradigm4)
  - Hossein Falaki (databricks)

## Reports

[2016 Progress Report](Distributed_Computing_Working_Group_Progress_Report_2016.md)

## Milestones

### Achieved

  - Adopt ddR as a prototype for a standard API for distributed
    computing in R

### 2016 Internship

Clark Fitzgerald, a PhD student in the UC Davis Statistics department,
worked on ddR and Spark integration.

  - Wrote [sparklite](https://github.com/clarkfitzg/sparklite) and
    [rddlist](https://github.com/clarkfitzg/rddlist) as minimal
    proof-of-concept R packages to connect and store general data on
    Spark.
    [slides](https://docs.google.com/presentation/d/1WfUQ2ockNku90GWMXonEhUEcVOWcgBmWwt5uYSSBYPY/edit?usp=sharing)
  - [Patched SparkR](https://issues.apache.org/jira/browse/SPARK-16785)
    to allow user defined functions returning binary columns. This
    allows implementation of different data structures in SparkR.
  - Updated [design
    documents](https://github.com/vertica/ddR/wiki/Design) with
    suggested changes to DDR's internal design and object oriented
    model.
  - Improved [testing and ddR
    internals](https://github.com/vertica/ddR/pull/15).

### Outstanding

  - Agree on a final standard API for distributed computing in R
  - Implement at least one scalable backend based on an open-source
    technology like Spark, SQL, etc

## Open Questions

  - How can we address the needs of both the end user data scientists
    and the algorithm implementers?
  - How should we share data between R and a system like Spark?
  - Is there any way to unify SparkR and sparklyr?
  - Could we use the abstractions of tensorflow to partially or fully
    integrate with platforms like Spark?

## Minutes

### 05/11/2017

  - Hernik presented his work on “Futures in R: Atomic Building Blocks
    for Asynchronous Evaluation”
      - Future is an abstraction for a value that will be available
        later
      - Futures are in one of the two states— resolved or unresolved
      - Syntax for explicit future:

`      f<- future(expo)`  
`      v<-value(f) `

  -   - There are many ways to resolve futures. E.g., sequential,
        multicore, multisession, etc.
      - Aim is to make the future package “write once, run anywhere”. It
        works on all platforms.
      - In future api, the developer decides what to parallelize and the
        user decides how to
      - future.Batchjobs a future API on top of BatchJobs (map-reduce
        API for HPC schedulers)
      - Hernik presented an example of using DNA-sequence files with the
        future API.
      - Futures can be nested. E.g., one can create a future in an outer
        loop and create more futures within the inner loop.
      - The plan() function can be used with futures to decide whether
        the futures are run in parallel or sequentially.
      - The doFuture() package is a foreach adaptor. It allows foreach
        to utilize HPC clusters.

Slides are available here: [Futures in R](BengtssonH_20170511-future,RConsortium,flat.pdf)

### 03/09/2017

  - Talk by Brian Lewis
      - Has created a Github page with notes on Singularity.
      - Singularity is a container technology for HPC applications
      - No daemon. Minimum virtualization to get application running.
        Light weight and has very low overheads.
      - Used widely in supercomputers
      - All distributed computing platforms even with R skins are
        difficult to use.
      - Containers make it much easier to abstract away the long tail of
        software dependencies and focus on R
      - Demonstrated an example of using Singularity with Tensorflow
      - Tried MPI and dopar on the 1000Genome data
      - The program parses the variant data and stores chunks as files.
        Then ran principal components on each file.
      - Overload matrix operations to use foreach/MPI underneath.
      - Overall: Use existing R operators and overloading them with the
        appropriate backend.
      - Will spend time working on Tensorflow, e.g., take a number of
        algorithms such as PCA and write them on top of Tensorflow using
        existing R primitives.

### 12/08/2016

  - Yuan Tang from Uptake was the presenter
      - Michael and Indrajit will write a status report for the working
        group sometime in December or January
      - Yuan gave an overview of TensorFlow
      - JJ, Dirk and Yuan are working on R layer for TensorFlow
      - TensorFlow is a platform for machine learning as well as other
        computations (even math proofs).
      - It is GPU optimized and distributed.
      - It is used in search, speech recognition, Google photos, etc.
      - TensorFlow computations are directed graphs. odes are operations
        and edges are tensors.
      - A lot of array, matrix, etc. operations are available
      - Backend is mostly C++. Python front end exists.
      - TensorFlow R is based on the python fronted
      - In multi-device setting, TensorFlow figures out which devices to
        use and manages communication between devices.
      - Computations are fault tolerant
      - Yuan has previously worked on Scikit Flow which is now TF.Learn.
        It’s a easy transition for Scikit learn users.
      - Yuan gave a brief overview of the python interface
      - TensorFlow in R handles conversion between R and Python. Syntax
        is very similar to python API
      - Future work: Adding more examples and tutorials, integration
        with Kubernetes/Marathon like framework.
      - During the Q/A there were questions related to whether R kernels
        can be supported in TensorFlow, and whether R dataframes are a
        natural wrapper for TensorFlow objects.

### 11/10/2016

  - SparkR slides were presented by Hossein Faliki and Shivaram from
    Databricks and UC Berkeley:
      - SparkR was a prototype from AMPLab (2014). Initially it had the
        RDD API and was similar to PySpark API
      - In 2015, the merge with upstream Spark, the decision was made to
        integrate with the Dataframe API, and hide the RDD API
      - In 2016 more MLLib algorithms have been integrated and new APIs
        have been added. A CRAN package will be released soon
      - Original SparkR architecture runs R on the master that
        communicates with the JVM processes in the driver. the driver
        sends commands to the worker JVM processes, and executes them as
        scala/java statements.
      - The system can read distributed data inside the JVM from
        different sources such as S3, HDFS, etc.
      - The driver has a socket based connection between SparkR and the
        RBackend. RBackend runs on the JVM, deserializes the R code, and
        converts the R statements into Java calls.
      - collect() and createDataFrame() are used to move data between R
        and JVM processes. createDataFrame will convert your local R
        data into a JVM based distributed data frame.
      - The API has IO, Caching, MLLib, and SQL related commands
      - Since Spark 2.0, we can run R processes inside the JVM worker
        processes. There is no need to keep long running R processes.
      - There are 3 UDF functions (1) lapply, runs function on different
        value of a list (2) dapply, runs function on each partition of a
        data frame. You have to careful about how data is partitioned,
        and (3) gapply, performs a grouping on different column names
        and then runs the function on each group.
      - The new CRAN package install.spark() will automatically download
        and install Spark. Automated CRAN checks have been added to
        every commit to the code. Should be available with Spark 2.1.0

<!-- end list -->

  - Q/A
      - Currently trying to get zero copy dataframe between python and
        Spark. Spark 2.0 has an off heap manager that uses Arrow. Once
        this feature is tested on the Python API, the next step will be
        integration R.
      - Spark dataframes gain from plan optimizations. It is not SparkR
        specific. R UDFs are still treated as black boxes by the
        optimizer
      - Spark doesn't directly support matrixes. There is no immediate
        intent to do so either. One can store an array or vector as a
        single column of a Spark dataframe.

### 10/13/2016

*Detailed minutes were not taken for this meeting*

  - Mario Inchiosa: Microsoft's perspective on distributed computing
    with R
      - Microsoft R Server: abstractions and algorithms for distributed
        computation on top of open-source R
      - Desired features of a distributed API like ddR:
          - Supports PEMA (initialize, processData, updateResults,
            processResults)
          - Cross-platform
          - Fast runtime
          - Supports algorithm writer and data scientist
          - Comes with a comprehensive set of algorithms
          - Easy deployment
      - ddR is making good progress but does not yet meet those
        requirements
  - Indrajit: ddR progress report and next steps
      - Recap of Clark's internship
      - Next step: implement some of Clark's design suggestions:
        <https://github.com/vertica/ddR/wiki/Design>
      - Spark integration will be based on sparklyr
      - Should we limit Spark interaction to the DataFrame API or
        directly interact with RDDs?
          - Consensus: will likely need flexibility of RDDs to implement
            everything we need, e.g., arrays and lists
      - Clark and Javier raised concerns about the scalability of
        sharing data between R and Spark
          - Michael: Spark is a platform in its own right, so
            interoperability is important, should figure something out
          - Bryan Lewis: Why not use tensor abstraction from tensorflow?
            Spark supports tensorflow and an R interface is already in
            the works.
      - Michael raised the issue of additional funding from the R
        Consortium to continue Clark's work
          - Joe Rickert suggested that the working group develop one or
            more white papers summarizing the findings of the working
            group for presentation to the Infrastructure Steering
            Committee.
          - Consensus was in favor of this, and several pointed out that
            the progress so far has been worthwhile, despite not meeting
            the specific goals laid out in the proposal.
  - Michael: do we want to invite some external speakers, one per
    meeting, from groups like databricks, tensorflow, etc?
      - Consensus was in favor.

### 9/8/2016

*Detailed minutes were not taken for this meeting*

  - Clark Fitzgerald: internship report
      - Developed two packages for low-level Spark integration: rddlist,
        sparklite
      - Patched a bug in Spark
      - ddR needs refactoring before Spark integration is feasible:
          - dlist, dframe, and darray should be formal classes.
          - Partitions of data should be represented by a distributed
            list abstraction, and most functions (e.g., dmapply) should
            be implemented on top of that list.
  - Javier: sparklyr update
      - Preparing for CRAN release
      - Mario: what happened to sparkapi?
          - Javier: sparkapi has been merged into sparklyr in order to
            avoid overhead of maintaining two packages. ddR can do
            everything it needs with sparklyr.
  - Luke Tierney: Update on the low-level vector abstraction, which
    might support interfaces like ddR and sparklyr.
      - Overall approach seems feasible, but still working out a few
        details.
      - Will land in a branch soon.
  - Bernd Bischl: update on the batchtools package
      - Successor to BatchJobs based on in-memory database

### 8/11/2016

*Meeting was canceled due to lack of availability.*

### 7/14/2016

  - Introduced Clark who is the intern funded by R Consortium. Clark is
    a graduate student from UC Davis. He will work on ddR integration
    with Spark and improving the core ddR API as well such as adding a
    distributed apply() for matrices, split function, etc.
  - Bernd: Can I play around with ddR now? What backend should I use?
    How robust is the code?
      - Clark: It's in good enough shape to be played around with. We
        will continue to improve it. Hopefully the spark integration
        will be done before the end of my internship in September.
  - Q: Is anyone working on using ddR to make ML scale better.
      - Indrajit: We have kmeans, glm, etc. already in CRAN.
      - Michael Kane: We are working on glmnet and other packages
        related to algorithm development.
  - Javier gave a demo of sparklyr and sparkapi.
      - Motivation for the pacakage: The SparkR package overrides the
        dplyr interface. This is an issue for RStudio. SparkR is not a
        CRAN package which makes it difficult to add changes. dplyr is
        the most popular tool by RStudio and is broken on SparkR.
      - Sparklyr provides a dplyr interface. It will also support ML
        like interfaces, such as consuming a ML model.
      - Sparklyr does not currently support any distributed computing
        features. Instead we can recommend ddR as the distributed
        computing framework on top of sparkapi. We will put the code in
        CRAN in a couple of weeks.
      - Simon: Can you talk more about the wrapper/low level API to work
        with Spark?
          - Javier: The package underneath the cover is called
            "sparkapi" it is to be used by pacakge builders.
            "spark\_context()" and "invoke()" are the functionality to
            call scala methods. It does not you to currently run R user
            defined functions. I am currently working on enabling that
            feature. Depending upon the interest in using ddR with
            sparkapi, I can spend more time to make sparkapi feature
            rich.
      - Indrajit: What versions of Spark are supported
          - Javier: Anything after 1.6
      - Bernd: How do you export data?
          - Javier: We are using all the code from SparkR. So everything
            in SparkR should continue to work. We don't need to change
            SparkR. We just need to maintain compatibility.
      - Bernd: What happens when the RDDs are very large?
          - Javier: Spark will spill on disk.
  - Michael Kane: Presented examples that he implemented on ddR.
      - Talked about how the different distributed packages compare to
        each other in terms of functionality.
      - Michael K. looked at glm and truncated SVD on ddR. Was able to
        implement irls on ddR by implementing two distributed functions
        such as "cross". In truncated SVD only needed to overload two
        different distributed multiplications.
      - Ran these algorithms on the 1000 genome dataset.
      - Overall liked ddR since it was easy to implement the algorithms
        in the package.
      - New ideas:
          - Trying to separate the data layer from the execution layer
          - Create an API that works on "chunks" (which is similar to
            the "parts" API in ddR). Would like to add these APIs to
            ddR.
          - Indrajit: You should be able to get some of the chunk like
            features by using parts and dmapply. E.g., you can call
            dmapply to read 10 different files, which correspond 10
            chunks now. These are however wrapped as a darray or dframe.
            But you can continue to work on the individual chunks by
            using parts(i).

### 6/2/2016

  - Round table introduction
  - (Michael) Goals for the group:
      - Make a common abstraction/interfaces to make it easier to work
        with distributed data and R
      - Unify the interface
      - Working group will run for a year. Get an API defined, get at
        least one open source reference implementations
      - not everyone needs to work hands on. We will create smaller
        groups to focus on those aspects.
      - We tried to get a diverse group of participants
  - Logistics: meet monthly, focus groups may meet more often
  - R Consoritum may be able to figure ways to fund smaller projects
    that come out of the working group
  - Michael Kane: Should we start with an inventory of what is available
    and people are using?
      - Michael Lawrence: Yes, we should find the collection of tools as
        well as the use cases that are common.
      - Joe: I will figure out a wiki space.
  - Javier: Who are the end users? Simon: Common layer needed to get
    algorithms working. We started from algos and tried to find the
    minimal common api. One of the goals is to make sure everyone is on
    the same page and not trying to create his/her own custom interface.
  - Javier: Should we try to get people with more algo expertise?
  - Joe: Simon do you have a stack diagram?
  - Simon: Can we get R Consortium to help write things up and draw
    things?
  - Next meeting: Javier is going to present SparkR next time.
