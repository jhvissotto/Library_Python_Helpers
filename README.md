# Python Helpers Library (No Black Box Definitions)

## About

⭐ Portal:     https://bit.ly/finance_analytics  
📊 Blog:       https://slashpage.com/jh-analytics  

📈 Softrader:  https://pypi.org/project/softrader

🐍 Python:     https://github.com/jhvissotto/Project_Finance_Api_Python  
🐍 Pypi:       https://pypi.org/project/jh-finance-api  

🟦 TScript:    https://github.com/jhvissotto/Project_Finance_Api_TScript  
🟦 NPM:        https://www.npmjs.com/package/finance-analytics-api  

🧮 PyHelpers:  https://github.com/jhvissotto/Library_Python_Helpers  

🔌 Server:     https://bit.ly/jh_finance_api  
🔌 Swagger:    https://bit.ly/jh_finance_api_swagger  



## Install

```py
!pip install py-helpers-lib
```

```py
from py_helpers_lib import *
```


## System

```py
def _pickle(CASES:Lit['COMPUTE','RELOAD','LOAD'], Lambda, dirs=[], name='', ext='.pkl', *a,**b):

    path    = os.path.join(*dirs, name + ext)
    EXISTS  = os.path.exists(path)

    def _calc():    return Lambda(*a,**b)
    def _read():    return joblib.load(path)
    def _save(X):   joblib.dump(X, path);  return X

    if (CASES == 'COMPUTE'):    return _calc()
    if (CASES == 'RELOAD'):     return _save(_calc())
    if (CASES == 'LOAD'):
        if     EXISTS:          return _read()
        if not EXISTS:          return _save(_calc())


def _parquet(CASES:Lit['COMPUTE','RELOAD','LOAD'], Lambda, dirs=[], name='', ext='.parquet', *a,**b):

    path    = os.path.join(*dirs, name + ext)
    EXISTS  = os.path.exists(path)

    def _calc():    return Lambda(*a,**b)
    def _read():    return pd.read_parquet(path)
    def _save(Df):  Df.to_parquet(path, index=False);  return Df

    if (CASES == 'COMPUTE'):    return _calc()
    if (CASES == 'RELOAD'):     return _save(_calc())
    if (CASES == 'LOAD'):
        if     EXISTS:          return _read()
        if not EXISTS:          return _save(_calc())
```


## Functions 1

```py
def _step(X, stp=nan): 
    if isinstance(X, int) and (X < 0) or (1 < X):
            return X[::stp]
    else:   return X

def _round(x, R=nan):
    if isinstance(x, int) and (x >= 0):
            return np.round(x, R)
    else:   return x
```


```py
def _sum(x):        return np.nansum(x)
def _prod(x):       return np.nanprod(x)

def _cumsum(x):     return np.nancumsum(x)
def _cumprod(x):    return np.nancumprod(x)

def _mean(x):       return np.nanmean(x)
def _std(x):        return np.nanstd(x)

def _gmean(x):      return exp(np.nanmean(log(x)))
def _gstd(x):       return exp(np.nanstd(log(x)))

def _med(x):        return np.nanmedian(x)
def _mad(x):        return sp_stats.median_abs_deviation(x, nan_policy='omit')

def _max(x):        return np.nanmax(x)
def _min(x):        return np.nanmin(x)

def _Q3(x):         return np.nanpercentile(x, 75)
def _Q1(x):         return np.nanpercentile(x, 25)
```


```py
def _pct(x, Lambda): return Lambda(1+x/100)*100-100

def _pct_gmean(x):   return _pct(x, _gmean)
def _pct_gstd(x):    return _pct(x, _gstd)
```


```py
def _Range(Max, Min):               return (Max - Min)
def _IQR(Q3, Q1):                   return (Q3 - Q1)

def _minmax(Val, Min, Range):       return (Val - Min) / Range *100
def _robust(val, med, IQR):         return (val - med) / IQR   *100

def _zscore(val, avg, dev):         return (val - avg) / dev
def _pscore(val, series):           return sp_stats.percentileofscore(series, val, 'mean', 'omit')

def _log1p_zscore(val, avg, dev):   return (log(1+val/100) - log(1+avg/100)) / log(1+dev/100)
```


## Functions 2

```py
def Groupby(Df, By=''):
    if By:  return Df.groupby(By, sort=0, group_keys=0, dropna=0)
    else:   return Df

def Roll(Df, Col, Lambda, win, wmin, By='', stp=nan, R=nan):
    return _round(Groupby(_step(Df, stp), By)[Col].rolling(win, wmin).apply(Lambda).reset_index(0,drop=1), R)
```

```py
def Mean      (Df, Col, win, wmin, By='', stp=nan, R=nan):   return Roll(Df, Col, _mean,      win, wmin, By, stp, R)
def Std       (Df, Col, win, wmin, By='', stp=nan, R=nan):   return Roll(Df, Col, _std,       win, wmin, By, stp, R)

def Gmean     (Df, Col, win, wmin, By='', stp=nan, R=nan):   return Roll(Df, Col, _gmean,     win, wmin, By, stp, R)
def Gstd      (Df, Col, win, wmin, By='', stp=nan, R=nan):   return Roll(Df, Col, _gstd,      win, wmin, By, stp, R)

def Pct_Gmean (Df, Col, win, wmin, By='', stp=nan, R=nan):   return Roll(Df, Col, _pct_gmean, win, wmin, By, stp, R)
def Pct_Gstd  (Df, Col, win, wmin, By='', stp=nan, R=nan):   return Roll(Df, Col, _pct_gstd,  win, wmin, By, stp, R)

def Med       (Df, Col, win, wmin, By='', stp=nan, R=nan):   return Roll(Df, Col, _med,       win, wmin, By, stp, R)
def Mad       (Df, Col, win, wmin, By='', stp=nan, R=nan):   return Roll(Df, Col, _mad,       win, wmin, By, stp, R)

def Max       (Df, Col, win, wmin, By='', stp=nan, R=nan):   return Roll(Df, Col, _max,       win, wmin, By, stp, R)
def Min       (Df, Col, win, wmin, By='', stp=nan, R=nan):   return Roll(Df, Col, _min,       win, wmin, By, stp, R)

def Q3        (Df, Col, win, wmin, By='', stp=nan, R=nan):   return Roll(Df, Col, _Q3,        win, wmin, By, stp, R)
def Q1        (Df, Col, win, wmin, By='', stp=nan, R=nan):   return Roll(Df, Col, _Q1,        win, wmin, By, stp, R)

def Range     (Df, Col, win, wmin, By='', stp=nan, R=nan):   return _Range(Max=Max(Df, Col, win), Min=Min(Df, Col, win))  .round(R)
def IQR       (Df, Col, win, wmin, By='', stp=nan, R=nan):   return   _IQR( Q3= Q3(Df, Col, win),  Q1= Q1(Df, Col, win))  .round(R)
```

```py
def Minmax(Df, Col, win, R=3):     return       _minmax(Val=Df[Col],  Min=      Min(Df, Col, win),  Range= Range(Df, Col, win)) .round(R)
def Robust(Df, Col, win, R=3):     return       _robust(val=Df[Col],  med=      Med(Df, Col, win),  IQR=     IQR(Df, Col, win)) .round(R)
def Zscore(Df, Col, win, R=3):     return       _zscore(val=Df[Col],  avg=     Mean(Df, Col, win),  dev=     Std(Df, Col, win)) .round(R)
def Pct_Gscore(Df, Col, win, R=3): return _log1p_zscore(val=Df[Col],  avg=Pct_Gmean(Df, Col, win),  dev=Pct_Gstd(Df, Col, win)) .round(R)

def Pscore(Df, Col, win):          return Roll(Df, Col, win, lambda X: _pscore(val=X.tail(1), series=X))
```