---
title: GHG Emissions due to Forest Fires
authors:
  - admin
tags: []
categories: []
projects: []
date: '2022-11-07T00:00:00Z'
image:
  caption: ''
focal_point: ''
---
## Introduction 

With accelarating climate change, news of wild fires have become very common. In this project we wanted to investigate the change in greenhouse gas emissions due to wild fire over last decade. We narrowed the scope of the project to look at the Savanna, grassland, and shrubland fires from South America. We employed GHG accounting here.

## Exploratory Data Analysis

The global wild fire emissions data was obtained from [Global Fire Emissions Data](https://www.globalfiredata.org/) version 4.1. For this project we used the dry matter emissions data over a period from 1997 to 2016. Here is a quick exploration of the DM emissions data: 


```python
import numpy as np
import h5py
import sys
import matplotlib.pyplot as plt
import calendar
import pandas as pd
from matplotlib import colors, cm
```


```python
directory    = '/Users/upasanasen/Downloads/GFED4/www.geo.vu.nl/~gwerf/GFED/GFED4'
```

Let's look at data for year 2010.


```python
year = 2010
```


```python
f = h5py.File(directory + f'/GFED4.1s_{year}.hdf5', 'r')
```

Looking at February 2010 emissions data.


```python
DM_emissions = f['/emissions/02/DM'][:]
```


```python
plt.imshow(DM_emissions , cmap=cm.rainbow, norm=colors.LogNorm())
plt.show()
```


    
![png](./index_12_0.png)
    


A spatial resolution of 0.25 degrees is available in the data.

The DM emissions data is separated into 6 sources:
* SAVA (Savanna, grassland, and shrubland fires)
* BORF (Boreal forest fires)
* TEMF (Temperature forest fires)
* DEFO (Tropical forest fires - deforestation and degradation)
* PEAT (Peat fires)
* AGRI (Agricultural waste burning)

Here we select the forest fires from Savanna, grassland, and shrubland fires.    


```python
contribution_SAVA = f['/emissions/02/partitioning/DM_SAVA'][:]
```


```python
plt.imshow(contribution_SAVA , cmap=cm.rainbow, norm=colors.LogNorm())
plt.show()
```


    
![png](./index_16_0.png)
    



```python
basis_regions = f['/ancill/basis_regions'][:]
```


```python
plt.imshow(basis_regions, cmap=cm.rainbow, norm=colors.LogNorm())
plt.show()
```


    
![png](./index_18_0.png)
    


Only selecting the region covering South America.


```python
south_america_mask = (basis_regions==4) | (basis_regions==5)
plt.imshow(south_america_mask.astype(int))
plt.show()
```


    
![png](./index_20_0.png)
    



```python
grid_area = f['/ancill/grid_cell_area'][:]
```


```python
plt.imshow(grid_area, cmap=cm.rainbow, norm=colors.LogNorm())
plt.show()
```


    
![png](./index_22_0.png)
    


## Extracting Dry Matter Data for Savannah in South America

Here we extract the dry matter emissions data for Savanna, grassland, and shrubland fires for the continent of South America over the period from 1997 through 2016. 


```python
DM_SAVA_South_America = np.sum(grid_area * south_america_mask * DM_emissions * contribution_SAVA)
DM_SAVA_South_America
```




    13959010000.0



We aggregate the DM emissions for the entire continent for out GHG accounting. 


```python
### creating for all years from 1997-2016

start_year = 1997
end_year = 2016

DM_SAVA_South_America = []
for year in range(start_year, end_year+1):
    DM_SAVA_South_America.append([])
    f = h5py.File(directory + f'/GFED4.1s_{year}.hdf5', 'r')
    basis_regions = f['/ancill/basis_regions'][:]
    south_america_mask = (basis_regions==4) | (basis_regions==5)
    grid_area = f['/ancill/grid_cell_area'][:]
    for month in range(1, 13):
        month = f"0{month}"[-2:]
        ## dry matter world-wide
        DM_emissions = f[f'/emissions/{month}/DM'][:]
        ## SAVA contribution world-wide
        contribution_SAVA = f[f'/emissions/{month}/partitioning/DM_SAVA'][:]
        ### creating Dry Matter from Savannah in South America
        DM_SAVA_South_America[-1].append(
            np.sum(grid_area * south_america_mask * DM_emissions * contribution_SAVA)
        )
DM_SAVA_South_America = np.array(DM_SAVA_South_America)
DM_SAVA_South_America
```




    array([[1.07564472e+10, 1.12795679e+10, 1.31613317e+10, 5.85500621e+09,
            4.93446349e+09, 3.83247078e+09, 3.15373834e+10, 1.63708502e+11,
            1.14328224e+11, 6.10001019e+10, 2.40187843e+10, 1.95779338e+10],
           [2.43370394e+10, 1.66070692e+10, 2.29313065e+10, 4.71031501e+09,
            3.53592576e+09, 1.24485048e+10, 4.26858865e+10, 1.55290321e+11,
            1.40196200e+11, 4.75809219e+10, 1.39173929e+10, 1.10200832e+10],
           [8.84242125e+09, 1.13719009e+10, 1.21075907e+10, 3.98496742e+09,
            6.82695526e+09, 7.81404979e+09, 3.59493837e+10, 1.81036171e+11,
            1.12393953e+11, 4.62456545e+10, 1.79637391e+10, 1.40932946e+10],
           [1.90436106e+10, 3.05566228e+10, 1.16720486e+10, 4.63011686e+09,
            4.96384614e+09, 1.26148659e+10, 1.52798218e+10, 3.36148787e+10,
            5.41593723e+10, 2.68407665e+10, 6.09394586e+09, 1.44529326e+10],
           [1.96409651e+10, 1.74623744e+10, 1.75155364e+10, 9.44802509e+09,
            5.85813811e+09, 6.61616026e+09, 2.67543020e+10, 1.03254188e+11,
            6.73383956e+10, 2.72850002e+10, 1.12769341e+10, 7.89172429e+09],
           [1.44907960e+10, 1.48294195e+10, 9.65941965e+09, 6.57713254e+09,
            7.24086784e+09, 1.58514340e+10, 3.31046912e+10, 1.08443419e+11,
            8.70752010e+10, 7.17664092e+10, 3.49377823e+10, 1.59906314e+10],
           [2.35706716e+10, 2.08104960e+10, 2.20699791e+10, 1.01209405e+10,
            7.23236352e+09, 1.31615795e+10, 3.87899433e+10, 7.41501256e+10,
            7.64388393e+10, 3.22938880e+10, 1.80318106e+10, 9.95897651e+09],
           [1.67079526e+10, 2.12569539e+10, 1.41984717e+10, 4.99265280e+09,
            5.22568346e+09, 1.42067579e+10, 2.73093489e+10, 8.46500250e+10,
            1.28628474e+11, 3.96283699e+10, 2.14022287e+10, 1.76857108e+10],
           [9.94925875e+09, 1.21512796e+10, 2.10586624e+10, 5.76713677e+09,
            4.81663283e+09, 7.92675021e+09, 3.21395057e+10, 1.14771091e+11,
            1.14999435e+11, 5.08969656e+10, 1.91561134e+10, 9.76892723e+09],
           [8.62552883e+09, 9.09249331e+09, 9.41051290e+09, 5.86147277e+09,
            6.28852941e+09, 6.73811200e+09, 2.42678600e+10, 8.67372564e+10,
            4.39304438e+10, 1.73467648e+10, 1.21816535e+10, 8.81138586e+09],
           [1.18553590e+10, 2.56185405e+10, 1.38479165e+10, 6.07351808e+09,
            5.98912154e+09, 1.49989448e+10, 3.21055416e+10, 1.36399118e+11,
            1.93385366e+11, 6.23172076e+10, 1.93540649e+10, 6.16418816e+09],
           [8.31166669e+09, 1.15560448e+10, 1.36208241e+10, 9.18022144e+09,
            4.51035392e+09, 5.66747494e+09, 1.50951680e+10, 4.67731907e+10,
            6.86481777e+10, 4.00975217e+10, 1.82736323e+10, 1.04217928e+10],
           [7.33480653e+09, 7.58138778e+09, 9.12038707e+09, 8.96761754e+09,
            5.60997939e+09, 4.23426099e+09, 8.19893350e+09, 2.66337956e+10,
            3.35434916e+10, 2.70765343e+10, 2.10152059e+10, 1.25077770e+10],
           [1.55792804e+10, 1.39590103e+10, 1.13328732e+10, 6.86274202e+09,
            6.31314330e+09, 1.44634716e+10, 4.22062940e+10, 1.80551975e+11,
            1.61128268e+11, 3.86001142e+10, 1.34799114e+10, 7.36865434e+09],
           [6.22851174e+09, 7.87543091e+09, 6.42450842e+09, 4.20258995e+09,
            3.71754624e+09, 5.98083942e+09, 1.38291313e+10, 4.33277993e+10,
            7.06190459e+10, 1.20154911e+10, 7.08185651e+09, 8.61719552e+09],
           [1.17091082e+10, 1.14714317e+10, 8.18583757e+09, 3.40067994e+09,
            4.13766272e+09, 7.60279654e+09, 2.07092818e+10, 8.28690186e+10,
            1.30151424e+11, 4.26451722e+10, 1.59598141e+10, 8.94905549e+09],
           [1.29048934e+10, 1.05981952e+10, 1.14263695e+10, 5.25510502e+09,
            3.83124813e+09, 4.46663373e+09, 1.30122977e+10, 4.32923730e+10,
            5.64880302e+10, 1.59414252e+10, 7.26887578e+09, 8.18643917e+09],
           [1.58133443e+10, 1.65570468e+10, 1.31030016e+10, 5.35687014e+09,
            4.39171277e+09, 6.25713101e+09, 1.34874368e+10, 4.98023629e+10,
            5.83576494e+10, 4.51349996e+10, 1.22111447e+10, 9.51611187e+09],
           [8.89186202e+09, 8.70193869e+09, 1.27483832e+10, 7.27652557e+09,
            5.66616218e+09, 4.91663360e+09, 1.05951273e+10, 3.81654876e+10,
            9.11266038e+10, 6.14953329e+10, 2.26881700e+10, 1.56356270e+10],
           [1.82609285e+10, 1.21341645e+10, 2.44890808e+10, 6.20188621e+09,
            4.23575654e+09, 6.85858662e+09, 2.91032556e+10, 7.40810506e+10,
            6.62612541e+10, 2.73002496e+10, 1.18438226e+10, 1.12149176e+10]],
          dtype=float32)




```python
DM_SAVA_South_America_df = pd.DataFrame(DM_SAVA_South_America, columns = [calendar.month_name[month] for month in range(1,13)])
DM_SAVA_South_America_df['year'] = [year for year in range(start_year, end_year+1)]
DM_SAVA_South_America_df.set_index('year', inplace=True)
DM_SAVA_South_America_df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>January</th>
      <th>February</th>
      <th>March</th>
      <th>April</th>
      <th>May</th>
      <th>June</th>
      <th>July</th>
      <th>August</th>
      <th>September</th>
      <th>October</th>
      <th>November</th>
      <th>December</th>
    </tr>
    <tr>
      <th>year</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1997</th>
      <td>1.075645e+10</td>
      <td>1.127957e+10</td>
      <td>1.316133e+10</td>
      <td>5.855006e+09</td>
      <td>4.934463e+09</td>
      <td>3.832471e+09</td>
      <td>3.153738e+10</td>
      <td>1.637085e+11</td>
      <td>1.143282e+11</td>
      <td>6.100010e+10</td>
      <td>2.401878e+10</td>
      <td>1.957793e+10</td>
    </tr>
    <tr>
      <th>1998</th>
      <td>2.433704e+10</td>
      <td>1.660707e+10</td>
      <td>2.293131e+10</td>
      <td>4.710315e+09</td>
      <td>3.535926e+09</td>
      <td>1.244850e+10</td>
      <td>4.268589e+10</td>
      <td>1.552903e+11</td>
      <td>1.401962e+11</td>
      <td>4.758092e+10</td>
      <td>1.391739e+10</td>
      <td>1.102008e+10</td>
    </tr>
    <tr>
      <th>1999</th>
      <td>8.842421e+09</td>
      <td>1.137190e+10</td>
      <td>1.210759e+10</td>
      <td>3.984967e+09</td>
      <td>6.826955e+09</td>
      <td>7.814050e+09</td>
      <td>3.594938e+10</td>
      <td>1.810362e+11</td>
      <td>1.123940e+11</td>
      <td>4.624565e+10</td>
      <td>1.796374e+10</td>
      <td>1.409329e+10</td>
    </tr>
    <tr>
      <th>2000</th>
      <td>1.904361e+10</td>
      <td>3.055662e+10</td>
      <td>1.167205e+10</td>
      <td>4.630117e+09</td>
      <td>4.963846e+09</td>
      <td>1.261487e+10</td>
      <td>1.527982e+10</td>
      <td>3.361488e+10</td>
      <td>5.415937e+10</td>
      <td>2.684077e+10</td>
      <td>6.093946e+09</td>
      <td>1.445293e+10</td>
    </tr>
    <tr>
      <th>2001</th>
      <td>1.964097e+10</td>
      <td>1.746237e+10</td>
      <td>1.751554e+10</td>
      <td>9.448025e+09</td>
      <td>5.858138e+09</td>
      <td>6.616160e+09</td>
      <td>2.675430e+10</td>
      <td>1.032542e+11</td>
      <td>6.733840e+10</td>
      <td>2.728500e+10</td>
      <td>1.127693e+10</td>
      <td>7.891724e+09</td>
    </tr>
    <tr>
      <th>2002</th>
      <td>1.449080e+10</td>
      <td>1.482942e+10</td>
      <td>9.659420e+09</td>
      <td>6.577133e+09</td>
      <td>7.240868e+09</td>
      <td>1.585143e+10</td>
      <td>3.310469e+10</td>
      <td>1.084434e+11</td>
      <td>8.707520e+10</td>
      <td>7.176641e+10</td>
      <td>3.493778e+10</td>
      <td>1.599063e+10</td>
    </tr>
    <tr>
      <th>2003</th>
      <td>2.357067e+10</td>
      <td>2.081050e+10</td>
      <td>2.206998e+10</td>
      <td>1.012094e+10</td>
      <td>7.232364e+09</td>
      <td>1.316158e+10</td>
      <td>3.878994e+10</td>
      <td>7.415013e+10</td>
      <td>7.643884e+10</td>
      <td>3.229389e+10</td>
      <td>1.803181e+10</td>
      <td>9.958977e+09</td>
    </tr>
    <tr>
      <th>2004</th>
      <td>1.670795e+10</td>
      <td>2.125695e+10</td>
      <td>1.419847e+10</td>
      <td>4.992653e+09</td>
      <td>5.225683e+09</td>
      <td>1.420676e+10</td>
      <td>2.730935e+10</td>
      <td>8.465002e+10</td>
      <td>1.286285e+11</td>
      <td>3.962837e+10</td>
      <td>2.140223e+10</td>
      <td>1.768571e+10</td>
    </tr>
    <tr>
      <th>2005</th>
      <td>9.949259e+09</td>
      <td>1.215128e+10</td>
      <td>2.105866e+10</td>
      <td>5.767137e+09</td>
      <td>4.816633e+09</td>
      <td>7.926750e+09</td>
      <td>3.213951e+10</td>
      <td>1.147711e+11</td>
      <td>1.149994e+11</td>
      <td>5.089697e+10</td>
      <td>1.915611e+10</td>
      <td>9.768927e+09</td>
    </tr>
    <tr>
      <th>2006</th>
      <td>8.625529e+09</td>
      <td>9.092493e+09</td>
      <td>9.410513e+09</td>
      <td>5.861473e+09</td>
      <td>6.288529e+09</td>
      <td>6.738112e+09</td>
      <td>2.426786e+10</td>
      <td>8.673726e+10</td>
      <td>4.393044e+10</td>
      <td>1.734676e+10</td>
      <td>1.218165e+10</td>
      <td>8.811386e+09</td>
    </tr>
    <tr>
      <th>2007</th>
      <td>1.185536e+10</td>
      <td>2.561854e+10</td>
      <td>1.384792e+10</td>
      <td>6.073518e+09</td>
      <td>5.989122e+09</td>
      <td>1.499894e+10</td>
      <td>3.210554e+10</td>
      <td>1.363991e+11</td>
      <td>1.933854e+11</td>
      <td>6.231721e+10</td>
      <td>1.935406e+10</td>
      <td>6.164188e+09</td>
    </tr>
    <tr>
      <th>2008</th>
      <td>8.311667e+09</td>
      <td>1.155604e+10</td>
      <td>1.362082e+10</td>
      <td>9.180221e+09</td>
      <td>4.510354e+09</td>
      <td>5.667475e+09</td>
      <td>1.509517e+10</td>
      <td>4.677319e+10</td>
      <td>6.864818e+10</td>
      <td>4.009752e+10</td>
      <td>1.827363e+10</td>
      <td>1.042179e+10</td>
    </tr>
    <tr>
      <th>2009</th>
      <td>7.334807e+09</td>
      <td>7.581388e+09</td>
      <td>9.120387e+09</td>
      <td>8.967618e+09</td>
      <td>5.609979e+09</td>
      <td>4.234261e+09</td>
      <td>8.198934e+09</td>
      <td>2.663380e+10</td>
      <td>3.354349e+10</td>
      <td>2.707653e+10</td>
      <td>2.101521e+10</td>
      <td>1.250778e+10</td>
    </tr>
    <tr>
      <th>2010</th>
      <td>1.557928e+10</td>
      <td>1.395901e+10</td>
      <td>1.133287e+10</td>
      <td>6.862742e+09</td>
      <td>6.313143e+09</td>
      <td>1.446347e+10</td>
      <td>4.220629e+10</td>
      <td>1.805520e+11</td>
      <td>1.611283e+11</td>
      <td>3.860011e+10</td>
      <td>1.347991e+10</td>
      <td>7.368654e+09</td>
    </tr>
    <tr>
      <th>2011</th>
      <td>6.228512e+09</td>
      <td>7.875431e+09</td>
      <td>6.424508e+09</td>
      <td>4.202590e+09</td>
      <td>3.717546e+09</td>
      <td>5.980839e+09</td>
      <td>1.382913e+10</td>
      <td>4.332780e+10</td>
      <td>7.061905e+10</td>
      <td>1.201549e+10</td>
      <td>7.081857e+09</td>
      <td>8.617196e+09</td>
    </tr>
    <tr>
      <th>2012</th>
      <td>1.170911e+10</td>
      <td>1.147143e+10</td>
      <td>8.185838e+09</td>
      <td>3.400680e+09</td>
      <td>4.137663e+09</td>
      <td>7.602797e+09</td>
      <td>2.070928e+10</td>
      <td>8.286902e+10</td>
      <td>1.301514e+11</td>
      <td>4.264517e+10</td>
      <td>1.595981e+10</td>
      <td>8.949055e+09</td>
    </tr>
    <tr>
      <th>2013</th>
      <td>1.290489e+10</td>
      <td>1.059820e+10</td>
      <td>1.142637e+10</td>
      <td>5.255105e+09</td>
      <td>3.831248e+09</td>
      <td>4.466634e+09</td>
      <td>1.301230e+10</td>
      <td>4.329237e+10</td>
      <td>5.648803e+10</td>
      <td>1.594143e+10</td>
      <td>7.268876e+09</td>
      <td>8.186439e+09</td>
    </tr>
    <tr>
      <th>2014</th>
      <td>1.581334e+10</td>
      <td>1.655705e+10</td>
      <td>1.310300e+10</td>
      <td>5.356870e+09</td>
      <td>4.391713e+09</td>
      <td>6.257131e+09</td>
      <td>1.348744e+10</td>
      <td>4.980236e+10</td>
      <td>5.835765e+10</td>
      <td>4.513500e+10</td>
      <td>1.221114e+10</td>
      <td>9.516112e+09</td>
    </tr>
    <tr>
      <th>2015</th>
      <td>8.891862e+09</td>
      <td>8.701939e+09</td>
      <td>1.274838e+10</td>
      <td>7.276526e+09</td>
      <td>5.666162e+09</td>
      <td>4.916634e+09</td>
      <td>1.059513e+10</td>
      <td>3.816549e+10</td>
      <td>9.112660e+10</td>
      <td>6.149533e+10</td>
      <td>2.268817e+10</td>
      <td>1.563563e+10</td>
    </tr>
    <tr>
      <th>2016</th>
      <td>1.826093e+10</td>
      <td>1.213416e+10</td>
      <td>2.448908e+10</td>
      <td>6.201886e+09</td>
      <td>4.235757e+09</td>
      <td>6.858587e+09</td>
      <td>2.910326e+10</td>
      <td>7.408105e+10</td>
      <td>6.626125e+10</td>
      <td>2.730025e+10</td>
      <td>1.184382e+10</td>
      <td>1.121492e+10</td>
    </tr>
  </tbody>
</table>
</div>



We convert the above extracted DM emissions data into MS Excel format so that we could do the GHG accounting in MS Excel. 


```python
DM_SAVA_South_America_df.to_excel('DM_SAVA_South_America_df.xlsx')
```

We have assumed zero emission factors for fluorinated greenhouse gases for this project.


```python
## get the emmision factor of GHG gasses for SAVA

f = open(directory+'/ancill/GFED4_Emission_Factors.txt')
EFs     = np.zeros((41, 6)) # 41 species, 6 sources
line = f.readline()
k = 0
Gas_names = []
while line != "":
    if line[0] != "#":
        contents = line.split()
        EFs[k,:] = contents[1:]
        Gas_names.append(contents[0])
        k += 1
    line = f.readline()

f.close()
```


```python
EFs.shape
```




    (41, 6)




```python
np.array(Gas_names)[[2,4,8]]
```




    array(['CO2', 'CH4', 'N2O'], dtype='<U14')




```python
efs_non_flouroghgs = dict(zip(np.array(Gas_names)[[2,4,8]], EFs[[2,4,8], 0]))
efs_non_flouroghgs
```




    {'CO2': 1686.0, 'CH4': 1.94, 'N2O': 0.2}



The GWP values for the greenhouse gasses were taken from [here](https://climatechangeconnection.org/emissions/co2-equivalents/)


```python
gwp = {'CO2': 1, 'CH4': 25, 'N2O': 298}
```

## GHG Accounting

The GHG accounting for yearly Savannah wild fire emissions (done in excel) is below:  

![excelfile1.png](./excelfile1.png)

The GHG accounting for monthly Savannah wild fire emissions (done in excel) is below:

![excelfile2.png](./excelfile2.png)

### GHG Emission over Time

We look at the greenhouse gas emissions for Savannah wild fire DM over the period 1997 to 2016

![excelfile3.png](./excelfile3.png)

![excelfile4.png](./excelfile4.png)
