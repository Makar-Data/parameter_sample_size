# point_estimate_sample_size
A simple code for the most basic formulas for sample size calculation. Only sample sizes for point estimates without power analysis

```Python
import math
import pandas as pd
import scipy.stats as stats

df = pd.read_pickle('sales_data.pkl')


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
                n = (math.pow(z, 2) * math.pow(std, 2)) / math.pow(error, 2)
                print('Sample size based on pop std:', round(n, 2))

            elif mode == '2':
                std = (data.max()-data.min()) / 4
                n = (math.pow(z, 2) * math.pow(std, 2)) / math.pow(error, 2)
                print('Sample size based on sample range:', round(n, 2))

            elif mode == '3':
                std = prior
                n = (math.pow(z, 2) * math.pow(std, 2)) / math.pow(error, 2)
                print('Sample size based on prior std:', round(n, 2))

        elif type == '2':

            mode = input('Input the state of prior knowledge of proportions:'
                         '\n1) Proportion is inputted'
                         '\n2) Proportion is not known'
                         '\n')

            if mode == '1':
                n = (math.pow(z, 2) * prior * (1 - prior)) / math.pow(error, 2)
                print('Sample size based on prior proportion:', round(n, 2))

            elif mode == '2':
                n = (math.pow(z, 2) * 0.5 * (1 - 0.5)) / math.pow(error, 2)
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
                n = (math.pow(z, 2) * math.pow(std, 2) * int(pop_size)) / (math.pow(error, 2) * (int(pop_size)-1) + (math.pow(z, 2) * math.pow(std, 2)))
                print('Sample size based on pop std:', round(n, 2))

            elif mode == '2':
                std = (data.max()-data.min()) / 4
                n = (math.pow(z, 2) * math.pow(std, 2) * int(pop_size)) / (math.pow(error, 2) * (int(pop_size)-1) + (math.pow(z, 2) * math.pow(std, 2)))
                print('Sample size based on sample range:', round(n, 2))

            elif mode == '3':
                std = prior
                n = (math.pow(z, 2) * math.pow(std, 2) * int(pop_size)) / (math.pow(error, 2) * (int(pop_size)-1) + (math.pow(z, 2) * math.pow(std, 2)))
                print('Sample size based on prior std:', round(n, 2))

        elif type == '2':

            mode = input('Input the state of prior knowledge of proportions:'
                         '\n1) Proportion is inputted'
                         '\n2) Proportion is not known'
                         '\n')

            if mode == '1':
                n = (math.pow(z, 2) * prior * (1 - prior) * int(pop_size)) \
                    / (math.pow(error, 2) * (int(pop_size)-1) + math.pow(z, 2) * prior * (1 - prior))
                print('Sample size based on prior proportion:', round(n, 2))

            elif mode == '2':
                n = (math.pow(z, 2) * 0.5 * (1 - 0.5) * int(pop_size)) \
                    / (math.pow(error, 2) * (int(pop_size)-1) + math.pow(z, 2) * 0.5 * (1 - 0.5))
                print('Sample size based on unknown proportion:', round(n, 2))

    return n

sample_size_p = sample_size(data=df['price'],
                            prior=0.24,
                            conf=0.95,
                            error=0.07)

```
