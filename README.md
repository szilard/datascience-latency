 
### Latency numbers every data scientist should know

Analytical tasks vary on a huge scale of sophistication and utilization of
computational resources. Computational time will depend on a lot of factors 
(e.g. software tool, hardware, tuning etc.) and thus will vary even for the same
task. However, it is useful to know at least the *order of magnitude* of CPU time
for the most common classes of analytical tasks with the typically available tools on 
current hardware (2015). I'm making huge simplifications here (and I'm very 
liberal with rounding).

#### TL;DR

    | Analytical task                | Records  | Time | Rec/s 
----|--------------------------------|----------|------|-------
1   | Non-linear supervised learning | 1M       | 100s | 10K   
2   | Linear supervised learning     | 10M      | 10s  | 1M    
3   | SQL                            | 100M     | 1s   | 100M  
4   | Adding numbers                 | 1B       | 1s   | 1B    

Pyramid of analytical tasks: each class is 1-2 orders of magnitude more
computational time than the previous one.


##### Analytical tasks

Non-linear supervised learning: random forests, non-sparse data

Linear supervised learning: logistic regression

SQL: simple aggregates and joins (typically used in interactive EDA)

Adding numbers: sum of a vector in RAM


##### Hardware

Modern CPU, ~10 cores (EC2, 10 can be 8-32 to me - as I said I'm rounding like hell)

Single machine (laptop/desktop/server), no distributed computing 

Most analytical tasks do not require "big data" tools. Currently "big data" tools
have 1 order of magnitude performance hit vs the best single node tools and add 
a lot of additional complexity.


#### Details

##### Non-linear supervised learning

Random forests, ~100 features

Best open source tools: H2O and xgboost

Timing: 1M records, 100 trees, 32 cores (r3.8xlarge), H2O 130s, xgboost 30s, 
[details here](https://github.com/szilard/benchm-ml).


##### Linear supervised learning 

Logistic regression, ~100 features

Many tools: Vowpal Wabbit, H2O etc.

Timing: 10M records, 32 cores (r3.8xlarge), VW 15s (1 core), H2O 5s, 
[details here](https://github.com/szilard/benchm-ml).


##### SQL

Really simple aggregates and joins.

Tools: analytical databases, R's data.table package, Python pandas.

Aggregate 100M records, group by 1M groups. 

Join previous 100M table with another table of 1M records.

Timing: 8 cores (m3.2xlarge), 0.5-2s, [details here](https://github.com/szilard/benchm-databases).


##### Adding numbers

Adding elements of a vector in RAM.

`sum(x)` in R

Timing: 1s, [details here](https://gist.github.com/szilard/c8bce58c843296df9795).



#### Acknowledgements

Albert Einstein: Everything should be made as simple as possible, but not simpler.

[Latency Numbers Every Programmer Should Know](https://gist.github.com/jboner/2841832)



