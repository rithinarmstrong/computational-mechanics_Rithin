---
jupytext:
  formats: notebooks//ipynb,md:myst
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.11.4
kernelspec:
  display_name: Python 3 (ipykernel)
  language: python
  name: python3
---

```{code-cell} ipython3
import numpy as np
import matplotlib.pyplot as plt
plt.style.use('fivethirtyeight')
```

# Computational Mechanics Project #01 - Heat Transfer in Forensic Science

We can use our current skillset for a macabre application. We can predict the time of death based upon the current temperature and change in temperature of a corpse. 

Forensic scientists use Newton's law of cooling to determine the time elapsed since the loss of life, 

$\frac{dT}{dt} = -K(T-T_a)$,

where $T$ is the current temperature, $T_a$ is the ambient temperature, $t$ is the elapsed time in hours, and $K$ is an empirical constant. 

Suppose the temperature of the corpse is 85$^o$F at 11:00 am. Then, 45
min later the temperature is 80$^{o}$F. 

Assume ambient temperature is a constant 65$^{o}$F.

1. Use Python to calculate $K$ using a finite difference approximation, $\frac{dT}{dt} \approx \frac{T(t+\Delta t)-T(t)}{\Delta t}$.

```{code-cell} ipython3
Tempi=85
Tempf=80
Timei=1100
Timef=1145
DeltaT=(Timef-Timei)/60
DT=(80-85)/(DeltaT)
K=DT/(20)*-1
K
```

2. Change your work from problem 1 to create a function that accepts the temperature at two times, ambient temperature, and the time elapsed to return $K$.

```{code-cell} ipython3
def return_K(T0=85,T1=80,deltat=45/60,TA=65):
    return -(T1-T0)/deltat/(T0-TA)
return_K(deltat=45/60)
```

```{code-cell} ipython3

```

3. A first-order thermal system has the following analytical solution, 

    $T(t) =T_a+(T(0)-T_a)e^{-Kt}$

    where $T(0)$ is the temperature of the corpse at t=0 hours i.e. at the time of discovery and $T_a$ is a constant ambient temperature. 

    a. Show that an Euler integration converges to the analytical solution as the time step is decreased. Use the constant $K$ derived above and the initial temperature, T(0) = 85$^o$F. 

    b. What is the final temperature as t$\rightarrow\infty$?
    
    c. At what time was the corpse 98.6$^{o}$F? i.e. what was the time of death?

```{code-cell} ipython3
T_an=lambda t:65 +(85-65)*np.exp(-0.444*t)
T_an(1000)
t=np.linspace(0,5)
plt.plot(t,T_an(t),'k',label='analytical equation')
for N in [5,10,20,50]:
    t_n=np.linspace(0,5,N)
    dt=t_n[1]-t_n[0]
    T_eul=np.zeros(len(t_n))
    T_eul[0]=85
    for i in range(0,len(t_n)-1):
        T_eul[i+1]=T_eul[i]-K*(T_eul[i]-65)*dt
    plt.plot(t_n,T_eul,'s-',label="dt")
plt.legend()
```

```{code-cell} ipython3
def predict_temp(tod):
    t=np.linspace(0,tod,100)
    dt=t[1]-t[0]
    T=np.zeros(len(t))
    T[0]=98.6
    for i in range(1,len(t)):
        T[i]=T[i-1]-K*(T[i-1]-65)*dt
    return 85-T[-1]
from scipy.optimize import fsolve
tod_sol=fsolve(predict_temp,1.5)
t=np.linspace(0,tod_sol,100)
dt=t[1]-t[0]
T=np.zeros(len(t))
T[0]=98.6
for i in range(1,len(t)):
    T[i]=T[i-1]-K*(T[i-1]-65)*dt
plt.plot(t,T,'s')
plt.plot(t,85*np.ones(len(t)))
print(tod_sol)
```

```{code-cell} ipython3
from datetime import timedelta
from datetime import datetime
found=datetime.fromisoformat('2023-09-12 11:00:00')
time_delta=timedelta(weeks=0,days=0,hours=1.5567)
```
