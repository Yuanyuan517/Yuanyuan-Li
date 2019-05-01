For generating the random process time variations, I create function to generate either positive or negative numbers from 2 uniform distribution functions (1st select a random float number, if less than 0.5, generate a positive number; otherwise, generate a negative number). To illustrate the generated random data distribution, I followed the [link](https://stackoverflow.com/questions/6939136/how-to-overlay-density-plots-in-r "link") to plot the two uniforms density graphs on one picture. The code is as following (I added "-" before runif to generate negative numbers)
```r
> data <- data.frame(positive_uniform=runif(1000000, min=10, max=40), negative_uniform=-runif(1000000, min=5, max=30))
> dens <- apply(data, 2, density)
> plot(NA, xlim=range(sapply(dens, "[", "x")), ylim=range(sapply(dens, "[", "y")))
> mapply(lines, dens, col=1:length(dens))
> legend("topright", legend=names(dens), fill=1:length(dens))
> 
```
Then the graph is in the [link](https://i.imgur.com/x9niX7x.png). Notice that the graph is not so smooth, it is because the lines are composed by points, more points, more flat the lines are.
