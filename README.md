 
### Latency numbers every data scientist should know

#### aka the pyramid of analytical tasks

Analytical tasks vary on a huge scale of both sophistication and utilization of
computational resources. Computational time will depend on several factors 
(e.g. software tool, hardware, tuning etc.) and thus will vary even for the same
task. However, it is useful to know at least the *order of magnitude* of CPU time
for the most common analytical tasks with the typically available tools on 
commodity hardware (in 2015). I'm making huge simplifications here (and I'm very 
liberal with rounding).

#### TL;DR

    | Analytical task                | Records  | Time | Rec/s 
----|--------------------------------|----------|------|-------
1   | Non-linear supervised learning | 1M       | 100s | 10K   
2   | Linear supervised learning     | 10M      | 10s  | 1M    
3   | SQL                            | 100M     | 1s   | 100M  
4   | Adding numbers                 | 1B       | 1s   | 1B    

Pyramid of analytical tasks: each class is 1-2 orders of magnitude more
computational time than the one below. Also sexiness and hype increases from bottom
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

Modern CPU, ~10 cores (EC2, 10 can be 8-32 to me - as I said I'm rounding like hell)

Single machine (laptop/desktop/server), no distributed computing

Most analytical tasks do not require "big data" tools. Currently "big data" tools
have 1 order of magnitude performance hit vs the best single node tools and they also add 
a lot of additional complexity. You can get 250GB RAM on a single EC2 instance now 
and 2TB from Spring 2016.


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

`sum(x)` in R (numbers stored contigously in memory, loop executes at C level)

1B records, 1s, [details here](https://gist.github.com/szilard/c8bce58c843296df9795).



#### TODO

One can think in terms of #operations (FLOPS), CPU or memory bound, memory bandwidth,
multicore, pipelining etc. for the above 4 classes of analytical tasks.

Would be also instructive to think about these for distributed systems, especially 
for the "big data" tools (though in most "big data" architectures like Hadoop or Spark
the JVM etc. makes this very hard).



#### Acknowledgements

All those who encouraged/helped me learn physics 20+ years ago. 

Albert Einstein: Everything should be made as simple as possible, but not simpler.

[Latency Numbers Every Programmer Should Know](https://gist.github.com/jboner/2841832)



