Download Link: https://assignmentchef.com/product/solved-pols6481-lab3-foreign-car-hmisc
<br>
The objectives are to practice constructing multivariate linear models using variances and covariances, to examine omitted variable bias, and to introduce the quantities that go into calculating standard errors.

<ol>

 <li><strong> Datasets</strong>: <em>caschool.dta</em></li>

</ol>

<strong>III. Packages</strong>:  <em>foreign</em>, <em>car</em>, <em>Hmisc</em>

<ol>

 <li><strong> Preparation</strong></li>

</ol>

<ul>

 <li>Open RStudio by double-clicking the icon or selecting RStudio from the Windows Start menu.</li>

 <li>Open the project from the Projects menu</li>

 <li>Pull from Github for any updates</li>

 <li>Navigate in the lower right file area to the Lab 3 section</li>

 <li>Open the R script by typing <em>Ctrl</em>+<em>O</em> or by clicking on File in the upper-left corner, using the dropdown menu, and navigating to the script in your working directory.</li>

 <li>) Run lines 1-9 in the R script to load three packages that you will need. If any of these are not already installed, you need to install them using the <strong>Packages</strong> tab or using the command:

  <ol>

   <li>&gt; install.packages(“here”) #You can also uncomment #line 2 to accomplish this</li>

  </ol></li>

</ul>

&gt; install.packages(“car”); install.packages(“Hmisc”)

<strong> </strong>




<ol>

 <li><strong> Instructions for Lab 03</strong></li>

</ol>




You will use data on school districts in California to explore the factors that determine test scores. The policy question you might consider is whether the influential factors are policies decided by the school districts or demographic factors that are largely outside the control of districts.




Begin by loading the dataset, typing the following line, changing the directory if needed:

&gt; CAdta&lt;-read.dta(“C:/Users/Scott/caschool.dta”)

If you failed to load the foreign package earlier, then R will not load the Stata dataset. Ensure that the package is both installed and loaded, and then re-run the line of code. Next, you will need to run lines 10–11 of the R script to drop twelve variables from the dataset (by selecting six that we will actually use in the lab. The new data frame that you will work with is named newdata.




<strong>One of the first places you’ll usually want to start when you work with an existing dataset is the Codebook, if one is available, or whatever other data description is available.</strong> The Codebook typically tells you the names of the variables, some of the methodology behind their collection, the type (continuous, discrete, binary, etc.), the unit of observation, the time period, etc. It’s a good idea to look at this before you try to do anything with the data.  In this case, we don’t have a Codebook, because the data isn’t that complicated. But we do have a data description (CaliforniaTestScores.pdf) which lists the variables you will use (on its second page). Some key demographic variables are the average income of families in the district, in thousands of dollars (avginc), the percent of students qualifying for the CalWORKs program, which is the state of California’s name for welfare (calw_pct), the percent of students qualifying for reduced-price lunch (meal_pct), and the percent of students who are learning English as a second language (el_pct). Three policy variables are the number of computers per student (comp_stu), the district’s expenditures per student (expn_stu), and the number of students per teacher (str).




<strong>You might wish to run the following command in order to see the correlation matrix.</strong> The example of movie revenues that we examined in class had the property that IMDB ratings and production budgets were positively correlated to each other, and that both these variables were positively correlated to revenues, but the correlation between production budgets and revenues was quite a bit stronger. Examine the correlations among explanatory variables in this dataset, and then examine their correlations to test scores.

&gt; cor(newdata[,c(2:6)])

<strong> </strong>

<ol>

 <li><strong> Simple Regression Coefficients</strong></li>

</ol>

The dependent variable that you will use in your analysis is average test scores in a district (testscr). Run lines 14–15 to find means and variances of test scores and the district’s expenditures (expn_stu); run line 16 to find the covariance between these variables.

The general command for finding the covariance is cov();  inside the parentheses you would place the data frame and variable names, as shown in the next line of code, or you can attach the data frame and then just list the variable names, as shown in the subsequent line of code.

&gt; cov(newdata$expn_stu,newdata$testscr)

&gt; attach(newdata); cov(expn_stu, testscr, use=”everything”)

Using the means, variances, and covariances, you can calculate the simple regression line for the student-to-teacher ratio’s effect on test scores. This entails filling in this table:




<table width="535">

 <tbody>

  <tr>

   <td width="142">Slope ()</td>

   <td width="197"></td>

   <td width="197"> </td>

  </tr>

  <tr>

   <td width="142">Intercept ()</td>

   <td width="197"></td>

   <td width="197"> </td>

  </tr>

 </tbody>

</table>




Run lines 17–18 to check the results of the first regression, showing the effects of student-to-teacher ratio:             &gt; reg1&lt;-lm(newdata$testscr~newdata$expn_stu)

&gt; summary(reg1)

The most important deduction from this model is that a one-unit increase in a district’s expenditures causes a <u>_____</u>-unit <u>________</u> (increase? decrease?) in the average test scores.




In lecture, we spent some time talking about competing explanations for the same phenomenon, which might take the following form: “Conventional wisdom holds that how much school districts spend is an important determinant of students’ test scores. However, an alternative theory posits that a school district’s demographics are more important.”




Run lines 20–21 to find the mean level of average household income in the district (avginc), to find the variance in average household incomes across districts, and to find the covariance of this variable with test scores.

&gt; mean(newdata$avginc); var(newdata$avginc)

&gt; cov(newdata$avginc,newdata$testscr)




Using the means, variances, and covariance, you can calculate the simple regression for average household income’s effect on test scores. This entails filling in the following table:




<table>

 <tbody>

  <tr>

   <td width="142">Slope ()</td>

   <td width="197"></td>

   <td width="197"> </td>

  </tr>

  <tr>

   <td width="142">Intercept ()</td>

   <td width="197"></td>

   <td width="197"> </td>

  </tr>

 </tbody>

</table>




Run lines 22–23 to check the results of the second regression, showing effects of average income:

&gt; reg2&lt;-lm(newdata$testscr~newdata$avginc)

&gt; summary(reg2)

The most important deduction from this model is that a one-unit increase in the value of average income causes a <u>_____</u>-unit <u>________</u> (increase? decrease?) in the average test scores. Average income explains ______ percent of the variation in test scores across districts.




<ol>

 <li><strong> Multiple Regression Coefficients</strong></li>

</ol>




To save time, you could simply run line 25 to find the correlation matrix between the five variables (the code [,c(2:6)] selects only the last five variables, since <em>district</em> is not numeric) or run lines 26–27 to find the variance-covariance matrix for four of the variables (the code [,c(2,4:6)] selects the second, third, fourth, and sixth variables only).




Run line 28 to show the mean value for a new variable, the percent of students who are English language learners:

&gt; mean(newdata$el_pct)




When examining the matrix of variances and covariances, be sure that you can locate the values that you examined above (covariances with the dependent variable are shown in the first column <em>and</em> the first row; variances are shown along the main diagonal).




Run lines 31–32 to estimate a <u>multiple</u> regression model using the average household income and the percent of English language learners in a district. These are two variables that are outside the control of school districts. Here is the code for the baseline model:

&gt; base&lt;-lm(newdata$testscr~newdata$avginc+newdata$el_pct)

&gt; summary(base)




Combined, average income and the percent English language learners explains ______ percent of variation in test scores. How does this compare to the R<sup>2</sup> from the simple regressions of test scores on district income in part A?




The coefficient for district income in the baseline model is _____ ; this coefficient’s value in the simple regression (lines 24–25) was _____ ; in your own words, what accounts for the difference?




Run line 33 to see the <em>added variable plots</em>, which illustrate the partial effect of one explanatory variable after accounting for the partial effect of the other explanatory variable. Since there were two variables in the baseline model, there will be two plots.

&gt; avPlots(base)




Run line 35 to check the first key property, which is that the mean residual equals zero:

&gt; round(mean(base$residuals), digits=4)




Run lines 36–37 to examine the correlations between the baseline model’s residuals and the two included variables. Both correlations ought to equal zero – indeed, this is the assumption that we use to begin the algebraic steps that lead to the equations for the multiple regression coefficients!

&gt; round(cor(base$residuals, newdata$el_pct), digits=4)

&gt; round(cor(base$residuals, newdata$avginc), digits=4)




Run lines 38–39 to examine the correlation between the baseline model’s residuals and two omitted variables, which in this case are the student-teacher ratio and per-pupil expenditures.

&gt; round(cor(base$residuals, newdata$str), digits=4)

&gt; round(cor(base$residuals, newdata$expn_stu), digits=4)

Do either of these variables seem to have a relationship to the dependent variable (test scores) that isn’t already accounted for in the included variables? In other words, is either of these variables “relevant”?




<ol>

 <li><strong> Exploring Omitted Variable Bias and Proxies</strong></li>

</ol>




Run lines 42–43 to estimate and then examine the results of a model using the two demographic variables plus a policy variable (per-pupil expenditures) that whose “relevance” you just assessed:

&gt; baseplus&lt;-lm(testscr ~ avginc + el_pct + expn_stu,newdata)

&gt; summary(baseplus)

Is your R-Studio showing you scientific notation? If it is and you would prefer it not to, then you can run the following line of code, then re-run line 43, to examine the regression estimates with standard notation.

&gt; options(scipen=999)




Examine the results, and please answer three questions:

<ul>

 <li>Does district expenditures have a significant effect on test scores in the multivariate model that controls for average income and English-language learners?</li>

 <li>Does either the effect of average income or the effect of English-language learners change from the baseline model when the district expenditures variable is added?</li>

 <li>Does the fit of the model improve when the district expenditures variable is added?</li>

</ul>

If you also run line 44, displaying the added-variable plots, then you ought to observe that the student-to-teacher ratio has no impact.

&gt; avPlots(baseplus)




Line 47 will run a new model that removes average income from the specification, while leaving in district expenditures. In other words, you are running a regression that you should strongly suspect will have an omitted variable bias. Run line 48 to examine the coefficients.

&gt; proxy&lt;-lm(testscr ~ el_pct + expn_stu, newdata)

&gt; summary(proxy)




Compared to the base and baseplus models, the coefficient for el_pct should have changed (base _____ ; baseplus _____ ; proxy _____) and the R-squared should be lower (base _____ ; baseplus _____ ; proxy _____) in the model using the proxy variable.




The coefficient for expn_stu also should have changed (baseplus _____ ; proxy _____) when we omit average income.




Run lines 50–52 to verify two properties of the residuals: that their mean equals zero, and that they are uncorrelated to the included regressors:

&gt; round(mean(resid(proxy)), digits=4)

&gt; round(cor(resid(proxy), newdata$el_pct), digits=4)

&gt; round(cor(resid(proxy), newdata$expn_stu), digits=4)




Next run line 53 to examine whether the residual is uncorrelated to the omitted regressor:

&gt; round(cor(resid(proxy), newdata$avginc), digits=4)

When you ran the baseline model earlier, the residual was uncorrelated to avginc, because residuals are forced to be uncorrelated to all included regressors. Now, when you run the model <em>without</em> avginc, the residual <em>is correlated</em> to this omitted variable!




Additionally, the coefficients on el_pct and expn_stu change when avginc is omitted, because the regression model gives some of the credit that average income deserves to other variables with which it covaries.

<strong> </strong>

<ol>

 <li><strong> Standard Errors for Simple (Bivariate) Regressions</strong></li>

</ol>




Now let us turn our attention to standard errors. We are interested in two types of standard errors: the standard error of the regression, and the standard error of a regression coefficient. For the moment, let’s focus on the second simple regression you estimated, exploring how school districts’ average income impacts test scores.




To find the correlation between average income and average test scores in a district, run line 56:

&gt; r = cor(newdata$testscr, newdata$avginc); r; r^2

This line of code retrieves the correlation coefficient for the variables you specify, and names it <em>r</em>, and then displays the correlation coefficient and the squared correlation coefficient.; the computed value of <em>r</em><sup>2</sup> = <u>                       </u>.




Now double-check that <em>r</em><sup>2</sup> = <em>R</em><sup>2</sup> by regressing testscr on avginc as shown in line 57.

&gt; summary(reg2)[8]




R lists the root mean square error (a.k.a. <em>sigma</em>) among the regression results when you use the summary() command, but it calls it  <strong>Residual standard error</strong>. You can run line 59:

&gt; summary(reg2)$sigma

Root MSE = _______ ; recall that <em>sigma</em> estimates the standard deviation of the disturbances, and that <em>sigma</em><sup>2</sup> is our sample estimate of the error variance.




You can obtain Root MSE by running lines 61–65 in the R script:

&gt; reg2.res&lt;-resid(reg2)

&gt; res2&lt;-reg2.res^2

&gt; ssr&lt;-sum(res2)

&gt; mse&lt;-ssr/(length(res2)-2)

&gt; rmse&lt;-sqrt(mse)

The first three lines generate the sum of squared residuals (SSR = ______ ). In your own words, describe what steps are being taken when you these three lines of code.




The next two lines of code shown above generate the mean squared error, which is an estimate of the variance of the disturbances (MSE = ______ ), and the root mean squared error, also known as <em>sigma</em> (root MSE = ______ ).




Earlier in the semester, I claimed that the standard error of a simple regression coefficient equaled the square root of a ratio: <em>sigma</em><sup>2</sup> divided by the sum: ; this latter term equals <em>n</em>–1 times the variance of <em>x</em>. Run line 67 to display the coefficients and standard errors; focus on the standard error of avginc , which we are treating as <em>x</em> at the moment. Then run lines 69–71 to calculate the denominator (<em>n</em>–1 times the variance of <em>x</em>), to calculate the ratio, and to take the root.

Do the values match?




Over the next couple lectures we will explore how changing the model specification impacts the standard error, and the equations will be slightly more complicated. But, hopefully this gives you some insight into the inputs for computing standard errors.




To clear the <strong>Environment</strong>, type rm(list=ls()) or click on the broom icon.

To clear the <strong>Console</strong> window, type Ctrl-<em>l</em>