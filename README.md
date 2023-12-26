# point_estimate_sample_size
A simple code for the most basic formulas for sample size calculation. Only sample sizes for point estimates without power analysis

```Python
import math
import pandas as pd
import scipy.stats as stats

df = pd.read_pickle('sales_data.pkl')


def mean_est_inf(z, std, error):
    n = (math.pow(z, 2) * math.pow(std, 2)) / math.pow(error, 2)
    return n


def prop_est_inf(z, prior, error):
    n = (math.pow(z, 2) * prior * (1 - prior)) / math.pow(error, 2)
    return n


def mean_est_fin(z, std, error, pop_size):
    n = (math.pow(z, 2) * math.pow(std, 2) * int(pop_size)) \
        / (math.pow(error, 2) * (int(pop_size) - 1) + (math.pow(z, 2) * math.pow(std, 2)))
    return n


def prop_est_fin(z, prior, error, pop_size):
    n = (math.pow(z, 2) * prior * (1 - prior) * int(pop_size)) \
        / (math.pow(error, 2) * (int(pop_size) - 1) + math.pow(z, 2) * prior * (1 - prior))
    return n


def sample_size(data, prior, conf, error):
    global n
    alpha = 1 - conf
    z = stats.norm.ppf(1 - alpha / 2)

    infinite = input('Input the population state:'
                     '\n1) Population is infinite'
                     '\n2) Population is finite'
                     '\n')

    if infinite == '1':

        type = input('Input the type of calculation:'
                     '\n1) Mean point estimate'
                     '\n2) Proportion estimate'
                     '\n')

        if type == '1':

            mode = input('Input the state of prior knowledge of std:'
                         '\n1) Data is pop'
                         '\n2) Data is sample, estimate std based on its range'
                         '\n3) Prior std inputted'
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

        type = input('Input the type of calculation:'
                     '\n1) Mean point estimate'
                     '\n2) Proportion estimate'
                     '\n')

        if type == '1':
            mode = input('Input the state of prior knowledge of std:'
                         '\n1) Data is pop'
                         '\n2) Data is sample, estimate std based on its range'
                         '\n3) Prior std inputted'
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


sample_size = sample_size(data=df['price'].loc[df['price'] != 0],
                          prior=0.2,
                          conf=0.95,
                          error=0.02)
```
