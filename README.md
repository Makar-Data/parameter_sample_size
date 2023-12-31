# Sample Size Calculation Based on Parameters
A simple code for the most basic formulas for sample size calculation. Only sample sizes for parameter point estimates without hypothesis testing and power analysis.

Variables:
- Data - the data itself. Only used to calculate standard deviation when the population standard deviation is unknown
- Prior - prior knowledge of the population standard deviation or proportion
- Conf - confidence level
- Error - margin of error


```Python
import math
import pandas as pd
import scipy.stats as stats


# Formula based on mean estimate in infinite population
def mean_est_inf(z, std, error): 
    n = (math.pow(z, 2) * math.pow(std, 2)) / math.pow(error, 2)
    return n


# Formula based on proportion esitmate in infinite population
def prop_est_inf(z, prior, error):
    n = (math.pow(z, 2) * prior * (1 - prior)) / math.pow(error, 2)
    return n


# Formula based on mean estimate in finite population
def mean_est_fin(z, std, error, pop_size):
    n = (math.pow(z, 2) * math.pow(std, 2) * int(pop_size)) \
        / (math.pow(error, 2) * (int(pop_size) - 1) + (math.pow(z, 2) * math.pow(std, 2)))
    return n


# Formula based on proportion esitmate in finite population
def prop_est_fin(z, prior, error, pop_size):
    n = (math.pow(z, 2) * prior * (1 - prior) * int(pop_size)) \
        / (math.pow(error, 2) * (int(pop_size) - 1) + math.pow(z, 2) * prior * (1 - prior))
    return n


# Main sample size calculation function
def sample_size(data, prior, conf, error):
    global n
    alpha = 1 - conf
    z = stats.norm.ppf(1 - alpha / 2)
    
    # Node 1 - determination of what formula to apply based on the infinite/finite nature of the population
    infinite = input('Input the population state:'
                     '\n1) Population is infinite'
                     '\n2) Population is finite'
                     '\n')
    
    if infinite == '1':
        
        # Node 2 - determination of what formula to apply based on mean/proportion target estimation
        type = input('Input the type of calculation:'
                     '\n1) Mean point estimate'
                     '\n2) Proportion estimate'
                     '\n')

        if type == '1':
            
            # Node 3 - determination of what formula to apply based on population parameter
            mode = input('Input the state of prior knowledge of std:'
                         '\n1) Data is pop'  # Data inserted into the function is itself the population
                         '\n2) Data is sample, estimate std based on its range'  # Std estimation based on sample range
                         '\n3) Prior std inputted'  # Population std is inserted into the function as 'prior'
                         '\n')

            if mode == '1':
                std = data.std()
                n = mean_est_inf(z, std, error)
                print('Sample size based on pop std:', round(n, 2))

            elif mode == '2':
                std = (data.max() - data.min()) / 4
                print(std)
                n = mean_est_inf(z, std, error)
                print('Sample size based on sample range:', round(n, 2))

            elif mode == '3':
                std = prior
                n = mean_est_inf(z, std, error)
                print('Sample size based on prior std:', round(n, 2))

        elif type == '2':

            # Node 3 - determination of what formula to apply based on population parameter
            mode = input('Input the state of prior knowledge of proportions:'
                         '\n1) Proportion is inputted'
                         '\n2) Proportion is not known'
                         '\n')

            if mode == '1':
                n = prop_est_inf(z, prior, error)
                print('Sample size based on prior proportion:', round(n, 2))

            elif mode == '2':
                n = prop_est_inf(z, 0.5, error)
                print('Sample size based on unknown proportion:', round(n, 2))

    elif infinite == '2':

        pop_size = input('Input the pop size:'
                         '\n')

        # Node 2 - determination of what formula to apply based on mean/proportion target estimation
        type = input('Input the type of calculation:'
                     '\n1) Mean point estimate'
                     '\n2) Proportion estimate'
                     '\n')

        if type == '1':
            
            # Node 3 - determination of what formula to apply based on population parameter
            mode = input('Input the state of prior knowledge of std:'
                         '\n1) Data is pop'  # Data inserted into the function is itself the population
                         '\n2) Data is sample, estimate std based on its range'  # Std estimation based on sample range
                         '\n3) Prior std inputted'  # Population std is inserted into the function as 'prior'
                         '\n')

            if mode == '1':
                std = data.std()
                n = mean_est_fin(z, std, error, pop_size)
                print('Sample size based on pop std:', round(n, 2))

            elif mode == '2':
                std = (data.max() - data.min()) / 4
                print(std)
                n = mean_est_fin(z, std, error, pop_size)
                print('Sample size based on sample range:', round(n, 2))

            elif mode == '3':
                std = prior
                n = mean_est_fin(z, std, error, pop_size)
                print('Sample size based on prior std:', round(n, 2))

        elif type == '2':

            # Node 3 - determination of what formula to apply based on population parameter
            mode = input('Input the state of prior knowledge of proportions:'
                         '\n1) Proportion is inputted'
                         '\n2) Proportion is not known'
                         '\n')

            if mode == '1':
                n = prop_est_fin(z, prior, error, pop_size)
                print('Sample size based on prior proportion:', round(n, 2))

            elif mode == '2':
                n = prop_est_fin(z, 0.5, error, pop_size)
                print('Sample size based on unknown proportion:', round(n, 2))

    return n


sample_size = sample_size(data=df[column],
                          prior=0.2,
                          conf=0.95,
                          error=0.02)

```
