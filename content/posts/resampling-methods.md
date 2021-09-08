+++
title= "Bootstrapping and Cost Analysis"
draft = true
tags = ["projects","learning","resampling","bootstrap"]
+++


Say you have a variable of interest that you know to be normally distributed, but you do not know its center or spread (we'll examine some other distributions later). In order to ascertain its center, you collect samples and use the sample mean. The best way to estimate the center of the distribution, naturally, would be to take as many samples as you feasibly can and calculate the sample mean. 

However, suppose each measurement imposes some fixed cost C. It is no longer feasible to simply collect more and more samples until you're sure you have the right answer. What's the alternative?

Resampling!

We define two different functions below for calculating the mean using resampling: one uses your standard non-parametric bootstrap, utilizing sampling with replacement, while the other uses the Fractional Random Weighted bootstrap outlined in Rubin's 1981 paper "The Bayesian Bootstrap". 

{{<highlight R "linenos=table">}}
resample_mean <- function(data, n){
	re_means <- replicate(n, mean(sample(data, size=length(data), replace=TRUE)))
	return(mean(re_means))
}


weighted_mean <- function(data, weights){
	wdat <- data*weights
	wmean <- sum(wdat)/length(data)
}

#rdirichlet(num_resamples, rep(shape, num_rows))
FRW_resample_mean <- function(data, n){
	re_weights <- length(data)*rdirichlet(n, rep(1, length(data)))
	re_weights <- as.data.frame(re_weights)
	re_means <- apply(re_weights, 1, FUN=function(x) weighted_mean(data, x))
	return(mean(re_means))
}
{{</highlight>}}

Let's further suppose that, not only is there a fixed sampling cost C, there is also a cost imposed by how far off from the "true" mean you are. We'll denote this cost F(D), where D is equal to the absolute value of the estimated mean subtracted from the true mean. Thus, we can define a generic total cost function: *T(n, D) = n*C + F(D)* 

Using these, we can examine which methods are the most "cost-efficient" for obtaining certain information about a parameter of interest. This cost-efficiency, of course, is dependent on how we define the cost function (and, for that matter, the sampling cost - which may not, in fact, be constant). 

Typically, we'd use a resampling function like the ones we've described to create a confidence interval or ascertain information about the sampling distribution of a parameter of interest. However, here we're going to start by simply getting the mean:

{{<highlight R "linenos=table">}}
> samp20 <- rnorm(20, mean=150, sd=15)
> samp100 <- rnorm(100, mean=150, sd=15)
> samp_mean_20 <- mean(samp20)
> samp_mean_100 <- mean(samp100)
> resamp_mean_20_10 <- resample_mean(samp20, 10)
> frw_resamp_mean_10_20 <- FRW_resample_mean(samp20, 10)
> c(samp_mean_20, samp_mean_100, resamp_mean_20_10, frw_resamp_mean_10_20)
[1] 151.5241 148.3297 152.0740 151.4010
{{</highlight>}}

Next we analyze the costs associated with each of the four methods. Let us assume that the fixed sampling cost C = 1 and the cost function F(D) = D, such that each additional unit off is 1 whole unit of cost. We'll examine other cost functions later. 

{{<highlight R "linenos=table">}}
samp_cost <- function(true_mean, est_mean, samp_size){
	distance <- abs(est_mean - true_mean)
	return(distance + samp_size)
}
> samp_cost(150,samp_mean_20, 20)
[1] 21.52415
> samp_cost(150, samp_mean_100, 100)
[1] 101.6703
> samp_cost(150, resamp_mean_20_10, 20)
[1] 22.07403
> samp_cost(150, frw_resamp_mean_10_20, 20)
[1] 21.40105
{{</highlight>}}

We repeat this process 100 times (drawing a new sample each time)...


{{<highlight R "linenos=table">}}
test_resample <- function(){
	samp_20 <- rnorm(20, mean=150, sd=15)
	samp_100 <- rnorm(100, mean=150, sd=15)
	samp_mean_20 <- mean(samp_20)
	samp_mean_100 <- mean(samp_100)
	resamp <- resample_mean(samp_20, 10)
	frw_resamp <- FRW_resample_mean(samp_20, 10)
	results <- c(samp_mean_20, samp_mean_100, resamp, frw_resamp)
	return(results)
}

cost_calc <- function(results){
	s20c <- samp_cost(150, results[1], 20)
	s100c <- samp_cost(150, results[2], 100)
	src <- samp_cost(150, results[3], 20)
	sfrwc <- samp_cost(150, results[4], 20)
	return(c(s20c, s100c, src, sfrwc))
}
> cost_test <- replicate(100, cost_calc(test_resample()))
> cost_test <- as.data.frame(t(cost_test))
{{</highlight>}}

With the resulting average costs:

{{<highlight R "linenos=table">}}
> c(mean(cost_test$V1), mean(cost_test$V2), mean(cost_test$V3), mean(cost_test$V4))
[1]  22.47455 101.12774  22.59581  22.55829
{{</highlight>}}

Obviously, the size 100 sample is the most expensive. The other three methods, however, seem to be roughly equivalent in cost. What if we try out different cost functions? Different distributions? 

We test with the exponential distribution instead. The maximum likelihood estimator for the rate parameter of the exponential distribution is the reciprocal of the sample mean. We modify the testing function as follows:

{{<highlight R "linenos=table">}}
test_resample <- function(){
	samp_10 <- rexp(10, rate=7)
	samp_50 <- rexp(50, rate=7)
	samp_mean_10 <- mean(samp_10)
	samp_mean_50 <- mean(samp_50)
	resamp <- resample_mean(samp_10, 10)
	frw_resamp <- FRW_resample_mean(samp_10, 10)
	results <- c(1/samp_mean_10, 1/samp_mean_50, 1/resamp, 1/frw_resamp)
	return(results)
}
> cost_test <- replicate(100, cost_calc(test_resample()))
> cost_test <- as.data.frame(t(cost_test))
> c(mean(cost_test$V1), mean(cost_test$V2), mean(cost_test$V3), mean(cost_test$V4))
[1] 162.1067 242.9304 162.0446 162.0556
{{</highlight>}}

Still relatively similar results! 

So let's try something harder. We'll test a linear regression on data generated using the following function:

{{<highlight R "linenos=table">}}
gen_TestData <- function(){
	param1 <- rnorm(1, mean=100, sd=20)
	param2 <- rnorm(1, mean=-50, sd=10)
	param3 <- rnorm(1, mean=1000, sd = 500)
	param4 <- rnorm(1, mean=5, sd= 2.5)
	noise <- rnorm(1, mean=0, sd=150)
	value <- 2.5*param1 + 3.3*param2 + 0.2*param3 + 84*param4 + noise
	return(c(param1, param2, param3, param4, value))
}
{{</highlight>}}
	