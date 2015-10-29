 
### Latency numbers every data scientist should know

#### aka the pyramid of analytical tasks

Analytical tasks vary on a huge scale of both sophistication and utilization of
computational resources. Computational time will depend on several factors 
(e.g. software tool, hardware, tuning, dataset size/structure etc.) 
and thus will vary even for the same
task. However, it is useful to know at least the *order of magnitude* of CPU time
(per data size) for the most common analytical tasks with the typically available tools on 
commodity hardware (in 2015). I'm making huge simplifications here.

#### TL;DR

    | Analytical task                | Records  | Time | Rec/s 
----|--------------------------------|----------|------|-------
1   | Non-linear supervised learning | 1M       | 100s | 10K   
2   | Linear supervised learning     | 10M      | 10s  | 1M    
3   | SQL                            | 100M     | 1s   | 100M  
4   | Adding numbers                 | 1B       | 1s   | 1B    

Take these numbers with a caveat though. While hardware performance does not
vary orders of magnitude, the performance across tools unfortunately (and somewhat
surprisingly) does. Some tools are 10x-100x slower than the best ones (the above numbers
reflect the best tools). 
There might also be a significant variation depending on the
type/structure/shape of the data and several other factors.

In any case, **there is certainly a "pyramid of analytical tasks": each class requires 
1-2 orders of magnitude (10x-100x) more
computational time than the one below**. Also sexiness and hype increases from bottom
to top (from "janitorial" to "rock star"-like, though actually all these tasks are 
important for data science).

---------------------------------

#### Analytical tasks

Non-linear supervised learning: random forests (but one could do 
e.g. GBM or neural networks)

Linear supervised learning: logistic regression

SQL: simple aggregates and joins (typically used in interactive EDA)

Adding numbers: sum of a vector in RAM


#### Hardware

Modern CPU, ~10 cores (EC2, 10 can be 8-32 as order of magnitude is concerned.)

Single machine (laptop/desktop/server), no distributed computing

Most analytical tasks do not require "big data" tools. Currently "big data" tools (like Hadoop or Spark)
have 1-2 order of magnitude (10x-100x) performance hit vs the best single node tools and they also add 
a lot of additional complexity. You can get 250 GB RAM on a single EC2 instance now 
(2 TB from Spring 2016) and solve most of your analytics problems on a single-node 
efficiently (and cost effectively).


---------------------------------

#### Details

##### Non-linear supervised learning

Random forests, non-sparse data

Best open source tools: H2O and xgboost

1M records, ~100 features, 100 trees, 32 cores (r3.8xlarge), H2O 130s, xgboost 30s, 
[details here](https://github.com/szilard/benchm-ml).


##### Linear supervised learning 

Logistic regression

Many tools: Vowpal Wabbit, H2O etc.

10M records, ~100 features, 32 cores (r3.8xlarge), VW 15s (using 1 core), H2O 5s, 
[details here](https://github.com/szilard/benchm-ml).


##### SQL

Really simple aggregates and joins.

Tools: analytical databases (such as Vertica or Redshift), R's data.table package, Python pandas.

Aggregate 100M records, group by 1M groups. Join previous 100M table with another table of 1M records.
8 cores (m3.2xlarge), 0.5-2s (with the best tools), [details here](https://github.com/szilard/benchm-databases).


##### Adding numbers

Adding elements of a vector in RAM.

`sum(x)` in R (numbers stored contiguously in memory, loop executes at C level)

1B records, 1s, [details here](https://github.com/szilard/datascience-latency/blob/master/sum-1bn.md)
(this essentially runs at memory bandwidth, same for Python, C or Java).


#### Possible things to do

It would be interesting to do some more thinking in terms of #operations (FLOPS), 
CPU or memory bound (memory bandwidth), multicore, L1,L2,L3 caches, pipelining etc. 
for the above 4 classes of analytical tasks. It would be also great to do some
instrumentation and to measure/see the inner workings at this level. 

It might be helpful to verify whether the distributed systems that are best performant
on single-node as well scale linearly or not, in which case one can talk about
CPU time per datasize per node.

It would be also instructive to think about the previously mentioned harware/software
characteristics also for the distributed systems (adding networking), though for 
"big data" architectures like Hadoop or Spark the JVM-layer etc. makes this harder).

It could be interesting to extend this project to some more specialized analytical tasks 
e.g. text processing, graph processing (networks), recommendation systems etc.


#### Acknowledgements

All those (e.g. my parents/teachers) who encouraged/helped me learn Physics 20+ years ago. 

Albert Einstein: Everything should be made as simple as possible, but not simpler.

[Latency Numbers Every Programmer Should Know](https://gist.github.com/jboner/2841832)



