Today I was working on finding the optimal solution for task allocation in data center with CPLEX OPL. Since it is under the consideration that no time is considered (tasks with long running time), the goal is to minimize the power consumption. I solve it as bin packing problem. During the procedure, I realized:
- in CPLEX OPL, some NOs: 
- - No support of reading JSON file as input-> my solution: create Maven project in eclipse, add json object related dependency in pom.xml, then parse json object and store data into a text file.
- - the declaration of decision variable *dvar* doesn't support float, dexpr does. So one solution is to give a scale in dexpr as proposed by Alex from IBM:

```
int scale=1000;
dvar int scalex in 0..scale;
dexpr float x=scalex/scale;
```
And remember the difference between *dvar* and *dexpr*, dvar needs a value range specifying by "in", dexpr is a decision expression, so it needs a formula specifying by "=".
- To solve it, I define a dvar boolean x[task][server], to indicate whether a task is allocated to a server or not. Then in the constraints, besides the capacity limitation, I set for each task, the sum of x taking into consideration of all servers, the value should be one (which guarantee each task must be allocated to one and only one server).
- I find when i try the complete instance (nearly 3000 tasks and 1000 servers), no reaction from the solver in mac, and in another windows laptop, it showed the error: Oplrun process is not responding, you must relaunch the Run Configuration. Check online: pass one null input set, stack overflow and so on. Then I run the solver with a much smaller instance (46 tasks, 16 servers), the best bound (the achievable result) is about 3300, however, the best objective is over 4000 with gap 22%, I ran for several hours till the message (Oplrun process not responding) appeared again. To notice, with metaheuristic, just about 2 min, it find a solution about 3700.

Besides that, I had a review about gap, the [notes](https://www.ibm.com/developerworks/community/blogs/jfp/entry/what_is_the_gap3?lang=en "notes") I found from IBM forum is pretty interesting:
`we assume we are dealing with a minimization problem (the other case  being a maximization problem). A minimization problem is defined by two items, a set P, and a real valued function f.

Solving the minimization problem amounts to find x in P such that for every y in P, f(x) <= f(y). solving an optimization problem amounts to meet two conditions:
1. Find x in P
2. Such that for all y in P, f(x) <= f(y)
In such case we say that x is a solution to the problem.  If x only meets condition 1 then x is called a feasible solution.

However, for some other problems proving optimality is so difficult that is cannot be done in a reasonable time using today's technology.  You may have found x in P, but you don't have the time to prove condition 2.  What can you do?  Well, if you find a lower bound on all possible values of the objective function, i.e. a number L such that  
* for all y in P, L <= f(y)
then you can evaluate how good is your x.  You compute the gap as follow:
*  gap = (f(x) - L)/ f(x)
`
According to my notes in optimization course on branch and bound, we can set the lower bound by relaxing the type of the integer value into float (can get infeasible solutions).

P.S. One colleague said one quiz about optimization: 4 people on one part with a light, they need to cross the bridge to go to another part. The constraint: two people in maximum per time to cross the bridge and they must take a light. The time needed for each person to cross are: 1 min, 2min, 5min and 10min, what is the minimum possible time?
I didn't think thoroughly by considering only the quick person to take the light back each time then get the value of 19. The actual solution is to let 1min and 2min to cross first, and 1min to go to send light, then 10min and 5min cross together, 2min go back to send light and crossing with 1min together. So in optimization problem, we should not be greedy in considering incomplete aspects, dynamic programming may solve the problem better.
