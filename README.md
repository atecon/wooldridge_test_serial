# wooldridge_test_serial
Function for computing Wooldridge's test for 1st order serial correlation of residuals in panel models. The null hypothesis is: no serial correlation of order one.
Since version 2.1., the user can either access the procedure via **scripting** or using the *post-model* **GUI** entry *Tests -> Autocorrelation-Wooldridge* for panel models.

Reference: Wooldridge, J.M. 2002. Econometric Analysis of Cross
Section and Panel Data. Cambridge, MA: MIT Press, pp.282-283.

*Note*: This type of test will be natively shipped by gretl in version 2019d.

Please ask questions and report bugs on the gretl mailing list if possible. Or write to: atecon@posteo.de or giuseppe.vittucci@unimib.it


Public Funtion
--------------
The main function is also called **wooldridge_test_serial** and takes the following arguments:

INPUT:
* y 		            - SERIES, dependent variable
* X 		            - LIST, regressors
* robustness_type	  - INT, optional parameter selecting the standard error type
                      + 0=asymptotic, 1=HAC type Arellano (default), 2=HC type clustered Beck & Katz
                      + Please note that the the degrees of freedom for HAC and clustered standard errors
are equal to the number of cross-sectional units minus 1.
* verbose		        - INT, optional parameter for printing test output: 0=NO, 1=YES (default)

RETURN:
1 by 3 matrix of the following statistics:

1) the point estimate of the AR coefficient;
2) the test statistic of the t-test;
3) the p-value of the two-sided t-test.

The function prints:
1) The results of the auxiliary regression for the model in first-differences;
2) The results of the regression of the estimated residuals in the previous
regression against its first lag;
3) The two-sided t-test (with the related p-value) for the null that the coefficient
attached to the lagged residuals is equal to -0.5.

When comparing the results of this package with STATA's function "xtseria"
(see Drukker, D.M. 2003. Testing for serial correlation in linear panel-data
models, Stata Journal 3(2), 168-177) note that STATA conducts an F-type test
rather than a t-test.

Please also note that the results obtained with the sample data set provided
with xtserial in Stata are different from the results obtained in gretl with
the same data set. This is due to the fact that the data set includes a time
variable with breaks. Ex.: year 75, 77, 80, 81...
While Stata checks at the content of the variable and does not treat two time
periods as successive when they are actually not contiguous, gretl does not
run such a check. Therefore the two programs return different series for the
first-differenced variables.

NB: Since gretl version 2019d, this type of test is shipped natively with gretl.
However, the gretl version only supports HAC robust Arellano standard errors.

**Changelog**:
v2.1
- Add GUI access in the model menu for FE and RE panel models
- Add different methods to compute std. errors
- Switch from F-test to t-test version
- Add some santity checks
- Minor adaptions

v2.0
- Return value is now a matrix instead of a scalar

Sample script
-------------
```hansl
clear
pkg install wooldridge_test_serial    # load from gretl pckg server
include wooldridge_test_serial.gfn --force

open abdata.gdt

list X = const n k
scalar robustness_type = 2	# optional
matrix M = wooldridge_test_serial(ys, X, robustness_type)
print M
```

This prints:
```
Pooled OLS, using 891 observations
Included 140 cross-sectional units
Time-series length: minimum 6, maximum 8
Dependent variable: y
Beck-Katz standard errors

             coefficient   std. error   t-ratio   p-value
  -------------------------------------------------------
  dn          0.104329     0.0318656     3.274    0.0013  ***
  dk          0.0392218    0.0315845     1.242    0.2164 

Mean dependent var  −0.021559   S.D. dependent var   0.051610
Sum squared resid    2.445485   S.E. of regression   0.052448
Uncentered R-squared 0.121829   Centered R-squared  -0.031582
F(2, 139)            5.373650   P-value(F)           0.005650
Log-likelihood       1363.330   Akaike criterion    −2722.659
Schwarz criterion   −2713.075   Hannan-Quinn        −2718.996
rho                  0.338653   Durbin-Watson        1.254767


First-Differenced equation (dependent d_ys):
With Beck & Katz panel corrected HC robust standard errors.

             coefficient   std. error     z     p-value
  -----------------------------------------------------
  dn          0.104329     0.0318656    3.274   0.0011  ***
  dk          0.0392218    0.0315845    1.242   0.2143 


Regression of residuals of the first-differenced equation on lagged residuals:
uhat = rho*uhat(-1) + e

Pooled OLS, using 751 observations
Included 140 cross-sectional units
Time-series length: minimum 5, maximum 7
Dependent variable: y
Beck-Katz standard errors

             coefficient   std. error   t-ratio   p-value
  -------------------------------------------------------
  u_1         0.339914      0.277088     1.227    0.2220 

Mean dependent var  -0.020396   S.D. dependent var   0.051998
Sum squared resid    2.077215   S.E. of regression   0.052627
Uncentered R-squared 0.112388   Centered R-squared  -0.024356
Log-likelihood       1146.214   Akaike criterion    -2290.428
Schwarz criterion   -2285.806   Hannan-Quinn        -2288.647
rho                  0.028473   Durbin-Watson        1.798116


             coefficient   std. error     z     p-value
  -----------------------------------------------------
  uhat(-1)    0.339914      0.277088    1.227   0.2199 


Wooldridge t-test for autocorrelation in panel data.
H0: no first-order autocorrelation (rho = -0.5):
With Beck & Katz panel corrected HC robust standard errors.
Test statistic: t(139) = 3.03122
with p-value = P(t(139) > 3.03122) = 0.00145297
Generated matrix M

M (1 x 3)

     ARcoeff    Teststat.      P-value 
     0.33991       3.0312    0.0014530 
```
